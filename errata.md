# Errata

## Wrestling with PyTagCloud

I wanted to install [PyTagCloud][pytag] to handle making word-clouds.  But it seems that has a list of requirements, including `simplejson`, which can be installed easily via `pip`, and `pygame`, which seems like it's going to be a pain: see [here][pygame-install], and maybe a better version [here][pygame-install-2] from Coding 2 Learn.[^1]

### A Warning: Pyenv & Homebrew

Before we go adding things via Homebrew, we should first make sure that Homebrew is seeing the correct system Python.  [This article][pyenv-brew] explains nicely the possible conflicts that can arise and the proper way to get things squared away.  The upshot is we should do the following:

	pyenv deactivate
	pyenv global system

... and make sure that the Pyenv path *precedes* `/usr/local/bin` in the `PATH`:

	export PATH=/usr/local/bin:$PATH
	export PYENV_ROOT="$HOME/.pyenv"
	export PATH="$PYENV_ROOT/bin:$PATH"

Try the following sanity checks:

	which python
	echo $PATH
	brew doctor

Unfortunately the output from the last still gives warnings about "Pythons installed to /Library can interfere with other software installs."  Let's willfully ignore that.

### Back to Our Regularly Scheduled Program

Now let's get back to install more system stuff, all painful dependencies for [PyTagCloud][pytag] or its more insidious dependency [Pygame][pygame]:

    brew install sdl sdl_image sdl_mixer sdl_ttf portmidi

This installs a ton of additional junk: `sdl_image` requires `jpeg, libpng, libtiff, webp`; `portmidi` requires `cmake`.

Supposedly we need to install [XQuartz][xquartz].  That's something we achieve via a `.dmg` download from the web page.

Then we continue as follows:

	brew tap homebrew/headonly
	brew install --HEAD smpeg

This too installs more junk: `smpeg` requires `gettext, libffi, glib, gobject-introspection, gdk-pixbuf, fontconfig, pixman, cairo, icu4c, harfbuzz, pango, atk, gtk+`.

Evidently we have to install [Mercurial][hg], since the [PyTagCloud][pytag] source is bundled as a Mercurial repo.  The eternal debate: Mercurial is based on Python and can be installed via `pip` as a Python package.  So should I install Mercurial via `pip` or via `brew`?

My reasoning: Mercurial is a source code management (SCM) system.  This is something that, in terms of function, is independent of the particular project and the particular language it's written in.  Moreover, it's independent of the particular version of Python we're using.  But my environment is set up using Pyenv to switch between several different Pythons, each with their own `pip`.  So any install of Mercurial via `pip` would leave Mercurial available only in that virtual environment where I installed it.  That's not desirable for an SCM system, so we probably shouldn't install via `pip`.  So `brew` it is:

	brew install mercurial

You may need to set up Mercurial.  You can find a guide here in [Mercurial's Quick Start][hg-qs].  The upshot is to create the file `~/.hgrc` and in a text editor add the lines

	[ui]
	username = John Doe <john@example.com>

changing the data to match your name and email.  As a sanity check, try the command

	hg debuginstall

to see if Mercurial is configured properly.  It should terminate with a line "no problems detected".

Now on to the truly project-specific stuff again:

	pyenv activate nlp3
	pip install hg+http://bitbucket.org/pygame/pygame
	pip install simplejson
	pip install -U pytagcloud

Let's see if that works.  An example from the Quick Start section of the [PyTagCloud guide][pytag]:

	from pytagcloud import create_tag_image, make_tags
	from pytagcloud.lang.counter import get_tag_counts
	
	YOUR_TEXT = "A tag cloud is a visual representation for text data, typically\
	used to depict keyword metadata on websites, or to visualize free form text."
	
	tags = make_tags(get_tag_counts(YOUR_TEXT), maxsize=120)
	create_tag_image(tags, 'cloud_large.png', size=(900, 600), fontname='Lobster')

Hopefully that will give non-garbage.  Crapola: the next to last line gives

	AttributeError: 'dict' object has no attribute 'iteritems'

Looks like `pytagcloud` hasn't made it into the era of Python 3.  Fudge.

### The Great Uninstall

To get rid of all this crap, do the following:

	pyenv activate nlp3
	pip uninstall pytagcloud
	pip uninstall simplejson
	pip uninstall pygame
	
	pyenv deactivate
	
	brew uninstall sdl sdl_image sdl_mixer sdl_ttf portmidi
	
	brew uninstall jpeg libpng libtiff webp
	
	# brew uninstall cmake
	
	brew uninstall smpeg
	
	brew uninstall gettext libffi glib gobject-introspection gdk-pixbuf fontconfig pixman cairo icu4c harfbuzz pango atk gtk+
	
	# brew uninstall mercurial
	# rm ~/.hgrc

I've commented out `brew uninstall cmake` and `brew uninstall mercurial`.  These are packages I've found one might want to keep around: in particular `cmake` is "useful" in trying to install IPython with the QtConsole and Notebook.  And Mercurial could be useful for general SVM, though if you're reading this, you've likely seen that `git` has the greater momentum in the community than `hg`.  If you remove Mercurial, you'll also want to delete the file `~/.hgrc`.


## Andreas Mueller's WordCloud

Perhaps a better way to go is to use Andreas Mueller's hand-rolled word-cloud generator.  He discusses the creation process in a [superb blog post][pboo-wc].  You can find the code on GitHub [here][wc-gh].  We can try the "fast install" listed in the README, sans the `sudo` bit:

	pyenv activate nlp3
	pip install Cython
	pip install Pillow
	# pip install numpy
	pip install git+git://github.com/amueller/word_cloud.git

The problem here is that the package really requires the [Python image library (PIL)][pil], but I've installed Pillow since ["PIL is basically dead"][pil-rip].  So we need to run some tests.  When I try

	import wordcloud

in an IPython shell, I get a bunch of PIL import statements and eventually

	ImportError: No module named 'query_integral_image'

So it seems this won't work either.

Let's back out:

	pip uninstall wordcloud
	pip uninstall Pillow
	pip uninstall Cython


[pytag]: https://pypi.python.org/pypi/pytagcloud
[pygame-install]: http://dudeslife.com/blog/2014/programming/installing-python-3-3-3-pygame-on-os-x-mavericks/ "PyGame install from Dude's Life"
[pygame-install-2]: http://coding2learn.org/blog/2014/03/11/installing-pygame-on-mac-os-x-with-python-3/ "Clearer install procedure from Coding 2 Learn"
[pygame-install-3]: http://www.reddit.com/r/pygame/comments/21tp7n/how_to_install_pygame_on_osx_mavericks/ "Install Pygame with Homebrew"
[pygame-install-vid]: http://www.youtube.com/watch?v=L0Cl4Crg7FE "Video for installing Pygame"
[pyenv-brew]: https://amaral.northwestern.edu/blog/troubleshooting-pyenv "How to Get Pyenv & Homebrew to Work Together"
[xquartz]: http://xquartz.macosforge.org
[hg]: http://mercurial.selenic.com/ "Mercurial"
[hg-qs]: http://mercurial.selenic.com/wiki/QuickStart "Mercurial Quick Start"
[pboo-wc]: http://peekaboo-vision.blogspot.com/2012/11/a-wordcloud-in-python.html "Andreas Mueller's description of creating a word-cloud generator"
[wc-gh]: https://github.com/amueller/word_cloud "GitHub repo for Mueller's word-count generator"
[pil]: http://www.pythonware.com/products/pil/ "Python image library"
[pil-rip]: http://stackoverflow.com/questions/20060096/installing-pil-with-pip "Stack Overflow thread discussing PIL and Pillow"


[^1]: There's even a reference to straight-up Homebrew installation [here](pygame-install-3), but I haven't determined if I can get it to play with Python 3 yet.  Moreover, if you scroll down, you'll find it ultimately reverts to installing Pygame with `pip`.  The `pip` version would be preferable, which seems to be what the [Coding 2 Learn](pygame-install-2) post gets at.