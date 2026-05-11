# Elk Development Kit Documentation

To read the documentation, follow this link: https://elk-audio.github.io/elk-docs/

This repository contains the source files, and generated HTML output, for the Elk Development Kit Documentation.

If you have cloned/downloaded this repository you can browse the documentation by opening the `./docs/index.html` file in your browser.

Most sections are written in MarkDown, and have to use the [CommonMark flavor](https://commonmark.org/), the accepted standard by [Sphinx](http://www.sphinx-doc.org) and [ReadtheDocs](https://readthedocs.com/). Some exceptions, such as is the index, use ReStructuredText - the main format for Sphinx.

## Building HTML From Source Files

If you have made any changes to an .MD documentation file, you also need to take the below steps to make the changes available through the web link.

Install [Sphinx](http://www.sphinx-doc.org/en/stable/):

	pip install -r requirements.txt

Alternatively, via apt (discouraged)

	apt-get install python3-sphinx

Build with:

	make html

Alternatively, if you've pip-installed Sphinx:w

	python3 -m sphinx -M html ./ ./docs/

The output will be generated in `docs`.

## Releasing 

The [Elk Audio Docs](https://elk-audio.github.io/elk-docs/) are hosted on GitHub
pages. The Github CI is configured to automatically build and publish the
website from a build artifact **whenever a tag is pushed**. Therefore, pushing
the generated HTML files manually is no longer necessary.

To not break old links from other projects, the site is served under the prefix
`/html/`. Navigating to
[https://elk-audio.github.io/elk-docs/index.html](https://elk-audio.github.io/elk-docs/index.html)
will redirect to
[https://elk-audio.github.io/elk-docs/html/index.html](https://elk-audio.github.io/elk-docs/html/index.html).

