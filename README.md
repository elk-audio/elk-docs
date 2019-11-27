# Elk Development Kit Manual

All manual chapters are written in MarkDown, and have to use the [CommonMark flavor](https://commonmark.org/). 

This is accepted as a standard by [Sphinx](http://www.sphinx-doc.org) and [ReadtheDocs](https://readthedocs.com/).

The only exception is the index, which uses ReStructuredText - the main format for Sphinx - so that indexes can be generated.

## Building

Install [Sphinx](http://www.sphinx-doc.org/en/stable/):

	pip install sphinx

Install [recommonmark](https://github.com/rtfd/recommonmark):

	pip install recommonmark

Build with:

	make html

The output will be generated in `docs`, the folder expected by GitHub Pages for the HTML files it serves.

In the  `docs` folder, there are two files added manually, needed for GitHub Pages: `.nojekyll`, and `index.html`. If you every type `make clean`, these two will be deleted - ensure you then reinstate them before committing.