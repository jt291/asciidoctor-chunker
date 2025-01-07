# asciidoctor-chunker

[![npm version](https://badge.fury.io/js/asciidoctor-chunker.svg)](https://badge.fury.io/js/asciidoctor-chunker)
[![Node.js CI](https://github.com/wshito/asciidoctor-chunker/actions/workflows/node.js.yml/badge.svg)](https://github.com/wshito/asciidoctor-chunker/actions/workflows/node.js.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Generates chunked (multi-page) HTML from Asciidoctor's single HTML file with the support of fine-tuned splits by chapters, sections, or any depth of subsections.  Here is [the sample output.](http://www.seinan-gu.ac.jp/~shito/asciidoctor/html_chunk/index.html)  Each chapter can have different levels of extraction depth.  See [What it does](#what-it-does) for details.  The asciidoctor-chunker is written in JavaScript and is very easy to install.  See [Installation](#installation).

## Latest News

See [CHANGELOG.md](CHANGELOG.md) for the complete history

- 2025/1/7 [Ver 1.0.7](https://github.com/wshito/asciidoctor-chunker/releases) Updated all the dependencies and Node.js v22.x is supported.
- 2023/2/20 [Ver 1.0.6](https://github.com/wshito/asciidoctor-chunker/releases) Page navigation is disabled with shift+arrow (thanks to [@chloekek](https://github.com/chloekek)).
- 2022/9/14 [Ver 1.0.5](https://github.com/wshito/asciidoctor-chunker/releases) Node.js v18.x is supported.  The `npm` installs the shebang script so you can invoke asciidoctor-chunker with `npx`.  When the target link within the document is missing, the anchor is still created with `target-missing` class attribute.
- 2021/8/3 [Ver 1.0.4](https://github.com/wshito/asciidoctor-chunker/releases) Added the keyboard shotcuts for the page navigation with arrow keys.  Added the accessibility labels on the page navigation for screen readers.
- 2021/6/25 [Ver 1.0.3](https://github.com/wshito/asciidoctor-chunker/releases) Fixed the security vulnerabilities in the dependencies.
- 2021/5/9 [Ver 1.0.2](https://github.com/wshito/asciidoctor-chunker/releases) The toc item for the titlepage can be configured with `--titlePage` option (thanks to [@johnthad](https://github.com/johnthad)).

## What it does

Asciidoctor-Chunker generates chunked HTML from a single HTML generated by Asciidoctor.

1. Splits part preambles and chapters (or any depth of section level) into separate files. Each chapter can be configured to have a different depth for extractions.
1. Extracts css inside the style element into a separate file so the browser can cache and share it among all the pages.
1. Places footnotes in the file they belong to.  This also means that the multiply referred footnotes are placed in every referrer's files and sets the link back to the referrer's id within the page.
1. Re-writes the relative links (cross references) in order to point to the appropriate chunked files.  The `target-missing` class attribute is added to the anchor when the link target is missing (is undefined in the source document).
1. Copies the local images and linked files (with `link`, `script` and `img` tags) whose paths are relative, to the directory relative to the chunked html output.  Files are only copied if they are new or modified compared to the previously copied one.
1. Adds a titlepage link in the toc and non-opinionated page navigation at the bottom of each page.  Arrow-keys navigation is supported.
1. Highlights the current page toc items and they get scrolled into the viewport.

Here is [the sample output](http://www.seinan-gu.ac.jp/~shito/asciidoctor/html_chunk/index.html) created from the [Asciidoctor User Manual](https://asciidoctor.org/docs/user-manual/).  The footer on the sample page is added by setting the asciidoctor attribute and is not added by asciidoctor-chunker.


## Installation

Asciidoctor-Chunker is written in JavaScript and runs with NodeJS.

1. Install [Node.js](https://nodejs.org/), the JavaScript runtime.  This will install `npm`, the package manager CLI for Node.js.
1. If you want to install globally, invoke the following;
   ```
   npm install -g asciidoctor-chunker
   ```
   You can install locally under the current directory as;
   ```
   npm install asciidoctor-chunker
   ```
1. Or alternatively you can download the pre-built program from the [release](https://github.com/wshito/asciidoctor-chunker/releases).

## Usage

If you installed globally:
   ```
   asiidoctor-chunker [single-html-file] -o [output-directory]
   ```
If you installed locally invoke the following under the directory you installed:
   ```
   npx asciidoctor-chunker [single-html-file] -o [output-directory]
   ```
If you installed the prebuild program simply run the script as:
   ```
   ./asciidoctor-chunker.js [single-html-file] -o [output-directory]
   ```
`[single-html-file]` is the single HTML file generated by [Asciidoctor](https://asciidoctor.org) from the book doctype.  If the output directory is not specified, the default is `html_chunks` under the current directory.

More discription of usage is available with `--help` option.

## How to Configure the Depth of Extraction

You can list the multiple settings by connecting each specifier with a comma.  Each specifier is consisted of either a single number or a collon separated with two numbers.

The single number sets the default level of extraction.  Number 1 is the application's default and it extracts the chapter level.  Number 2 for section extraction, 3 for subsection, and so on to 6 which is the maximum section level of Asciidoctor.

The list of collon separated numbers, `chap:level`, can change the extraction depth for specific chapters, so `3:2` means chapter 3 is extracted down to 2 levels (ie. section level).  You can use a hyphen to specify the range of chapters to set as `chapFrom-chapTo:level`, so `1-3:5` means chapter 1 through 3 should be extracted with the depth level 5.

Example:
```
  --depth 2          The default level 2, all the chapters and
                     sections will be extracted.
  --depth 3,1:2,8:5  The default level 3, level 2 for Chap 1,
                     level 5 for Chap 8.
  --depth 1,3-8:2    The default level 1, level 2 for Chap 3 to 8.
  --depth 3-8:3      No default is set so default level is 1, and
                     level 3 for chap3 to 8.`
```

## Custom CSS

By default `asciidoctor-chunker.css` is included in the output directory.  It provides the non-opinionated page navigation at the bottom of every chunked page.  You can override this by giving a comma separated list of paths to your custom css files.  They are copied into the output directory so the paths must be accessible by `asciidoctor-chunker`.

## About Strict Mode

If you have any custom elements inserted under `<div id=#content></div>` in the source single html, `asciidoctor-chunker` ignores it by default.  If you want them to be included into the chunked html, set the option `--no-strictMode`.
The element will be copied to every chunked page.

## Customizing Titlepage

The `index.html` created by `asciidoctor-chunker` is selected by clicking the word **Titlepage** in the table of contents. To change the default value, use the option `--titlePage [string]` where `[string]` is the desired text.

## Example

The project contains the `example` directory where you can generate the chunked html for the [Asciidoctor User Manual](https://asciidoctor.org/docs/user-manual/) by invoking `make`.  Simply go into the `example` directory and invoke `make`.  This will clone the asciidoctor project from the github for the first time, then the chunked html will be generated under `test/output-chunk/html_chunk/` directory.  The `index.html` is the first page.

```
$ cd example
$ make
```

## Related Projects

- [owenh000/asciidoctor-multipage](https://github.com/owenh000/asciidoctor-multipage) - The similar tool to produce multipage html.  It is an extension of [Asciidoctor](https://asciidoctor.org/) and produces multipage html directly from the asciidoc source file.
- [asciidoctor/asciidoctor](https://github.com/asciidoctor/asciidoctor) - The official Asciidoctor repository.

## License

MIT

## Developer's Memo

- Unit test uses `test/resources/output/single/sample.html` generated from `test/resources/sample.adoc`.
- `npm install cheerio commander`
- `npm install --save-dev ava webpack webpack-cli webpack-shebang-plugin`
- Filename related modules: Toc.mjs
