# Elk Development Kit Documentation

To read the documentation, follow this link: https://elk-audio.github.io/elk-docs/

This repository contains the source files, and generated HTML output, for the Elk Development Kit Documentation.

If you have cloned/downloaded this repository you can browse the documentation by opening the `./docs/index.html` file in your browser.

All sections are written in MarkDown, and have to use the [CommonMark flavor](https://commonmark.org/), the accepted standard by [Sphinx](http://www.sphinx-doc.org) and [ReadtheDocs](https://readthedocs.com/). The only exception is the index, which uses ReStructuredText - the main format for Sphinx - so that indexes can be generated.

## Building HTML From Source Files

If you have made any changes to an .MD documentation file, you also need to take the below steps to make the changes available through the web link.

Install [Sphinx](http://www.sphinx-doc.org/en/stable/):

	pip install sphinx

Install [recommonmark](https://github.com/rtfd/recommonmark):

	pip install recommonmark

Build with:

	make html

The output will be generated in `docs`, the folder expected by GitHub Pages for the HTML files it serves. 

Do commit and push both your changed .MD, and generated HTML files, for your changes to be reflected on the online HTML version of the documentation.

In the  `docs` folder, there are two files added manually, needed for GitHub Pages: `.nojekyll`, and `index.html`. If you every type `make clean`, these two will be deleted - ensure you then reinstate them before committing.

