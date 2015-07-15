# nlp

A sandbox for playing around with various script ideas related to linguistics

## Basic Projects

This repo is designed to hold a few simultaneous projects happening at the same time in various states of disarray.  Here's a short outline:

* **Rap**: I'm trying to munge rap lyrics to see what I can discover.
* **Latin**: I'd like to find a convenient way of creating study materials for Latin.  In particular I'd like to take some data found surfing the web and turn it magically into material easily imported into flashcard programs.  Wish me luck!

## Environment

For ease of reconstructing things when stuff breaks, I have to delete everything, and then I have to rebuild from scratch, I'll write here some notes on the running environment for this project.

This project is based on [Python][python], with help from the following packages.

* [Beautiful Soup][bs4]
* [NLTK][nltk]

I'm sandboxing this project in a Python virtual environment set up by [Pyenv][pyenv].  And of course I'm writing it in an [IPython][ipython] notebook, so you'll need to install that for things to work.

Ultimately I'll install [NumPy][numpy], so you'll need that.  That requires a fortran compiler, so make sure you've already done the following with [Homebrew][brew]:

    brew install gfortran

Then we can create the new environment, which I'll call `nlp3`, the `3` denoting that it's based on Python 3.x.

    pyenv virtualenv 3.4.0 nlp3
    pyenv virtualenvs

Within that environment, we'll install our packages.

    pyenv activate nlp3
    
    # For scientific computations
    pip install nose
    pip install numpy
    pip install matplotlib
    
    # For IPython
    pip install pyzmq
    pip install pygments
    pip install -U PySide
    pyside_postinstall.py -install
    pip install ipython[all]

This also installs `gnureadline`, `numpydoc`, `Sphinx`, `jinja2`, `docutils`, `markupsafe`.  Run the following to check that everything essential is working correctly:

    ipython qtconsole --pylab=inline
    ipython notebook --pylab=inline

Now we can finally install the NLPish stuff, specifically [NLTK][nltk] and [Beautiful Soup 4][bs4]:

    pip install beautifulsoup4
    pip install -U nltk

As I've come to expect, an error has to occur somewhere, and this time it's with `pip install -U nltk`.  For some reason, the [NLTK website][nltk] says this should be prefaced by `sudo`.  I don't see why.  As I understand it, this goes against a lot of the underlying principles of the package manager system in the first place, and here it goes against the idea of a virtual environment in the second.

That notwithstanding, the line for installing `nltk` leads to the following error:

    Cleaning up...
    Command python setup.py egg_info failed with error code 1 in /usr/local/var/lib/pyenv/versions/nlp3/build/nltk
    Storing debug log for failure in /Users/bobtodd/.pip/pip.log


As it turns out, this error was encountered once before -- [by me][nltk-err]!  So I've tried the solution proposed there, namely going directly to the source file.

    pip install https://pypi.python.org/packages/source/n/nltk/nltk-3.0.0b1.tar.gz

That seems to work.

That should do it for now.


## Adding Tag Cloud Functionality

I had originally wanted to work completely within Python, and so I tried installing [PyTagCloud][pytag].  But after a harrowing process ([see here](errata.md)), it turns out that PyTagCloud hasn't made it to the era of Python 3.  So it's ultimately a fool's errand to install that.  I still have a lot of packages to uninstall after that wild goose chase.

Another idea was to use Andreas Mueller's [WordCloud][wc-gh], discussed in a blog post [here][pboo-wc].  But it turns out that it's dependence on outdated Python libraries (again [see here](errata.md)) means that this winds up in failure.


[brew]: http://brew.sh/
[python]: https://www.python.org/
[pyenv]: https://github.com/yyuu/pyenv
[ipython]: http://ipython.org/
[numpy]: http://www.numpy.org/
[bs4]: http://www.crummy.com/software/BeautifulSoup/bs4/doc/
[nltk]: http://www.nltk.org/
[nltk-err]: https://groups.google.com/forum/#!topic/nltk-users/5hojEAby6Vo
[pytag]: https://pypi.python.org/pypi/pytagcloud
[pboo-wc]: http://peekaboo-vision.blogspot.com/2012/11/a-wordcloud-in-python.html "Andreas Mueller's description of creating a word-cloud generator"
[wc-gh]: https://github.com/amueller/word_cloud "GitHub repo for Mueller's word-count generator"

