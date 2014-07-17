nlp
===

A sandbox for playing around with various script ideas related to linguistics

Environment
-----------

For ease of reconstructing things when stuff breaks, I have to delete everything, and then I have to rebuild from scratch, I'll write here some notes on the running environment for this project.

This project is based on [Python](python), with help from the following packages.

* [Beautiful Soup](bs4)
* [NLTK](nltk)

I'm sandboxing this project in a Python virtual environment set up by [Pyenv](pyenv).  And of course I'm writing it in an [IPython](ipython) notebook, so you'll need to install that for things to work.

Ultimately I'll install [NumPy](numpy), so you'll need that.  That requires a fortran compiler, so make sure you've already done the following with [Homebrew](brew):

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

Now we can finally install the NLPish stuff, specifically [NLTK](nltk) and [Beautiful Soup 4](bs4):

    pip install beautifulsoup4
    pip install -U nltk

As I've come to expect, an error has to occur somewhere, and this time it's with `pip install -U nltk`.  For some reason, the [NLTK website](nltk) says this should be prefaced by `sudo`.  I don't see why.  As I understand it, this goes against a lot of the underlying principles of the package manager system in the first place, and here it goes against the idea of a virtual environment in the second.

That notwithstanding, the line for installing `nltk` leads to the following error:

    Cleaning up...
    Command python setup.py egg_info failed with error code 1 in /usr/local/var/lib/pyenv/versions/nlp3/build/nltk
    Storing debug log for failure in /Users/bobtodd/.pip/pip.log


As it turns out, this error was encountered once before -- [by me](nltk-err)!  So I've tried the solution proposed there, namely going directly to the source file.

    pip install https://pypi.python.org/packages/source/n/nltk/nltk-3.0.0b1.tar.gz

That seems to work.

That should do it for now.

[brew]: http://brew.sh/
[python]: https://www.python.org/
[pyenv]: https://github.com/yyuu/pyenv
[ipython]: http://ipython.org/
[numpy]: http://www.numpy.org/
[bs4]: http://www.crummy.com/software/BeautifulSoup/bs4/doc/
[nltk]: http://www.nltk.org/
[nltk-err]: https://groups.google.com/forum/#!msg/nltk-users/5hojEAby6Vo/uXgdgTaLwLAJ