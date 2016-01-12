[`cls-compile.py`](cls-compile.py) is a tool for compiling the proceedings of the Chicago Linguistic Society (CLS).

When the individual paper PDFs and other necessary PDFs (front matter, acknowledgments, etc) are in place, this tool compiles the proceedings PDF output, automatically taking care of the following:

* figuring out the page numbers for individual papers
* generating the paper headers and adding them to the paper PDFs
* generating the table of contents
* concatenating everything to create the final proceedings PDF output


Download
--------

`cls-compile.py` is currently available on GitHub: <https://github.com/JacksonLLee/cls-proceedings>

Two ways of downloading it:

* Run this if you have `git`:

    ```
    git clone https://github.com/JacksonLLee/cls-proceedings.git
    ```

* Download the zipped version:

  <https://github.com/JacksonLLee/cls-proceedings/archive/master.zip>

Contents
--------

This repository contains the following:

* `cls-compile.py`: where all the magic happens
* `example`: a folder as a sample "working directory" where all necessary input
  files are found
* `readme.md`: this readme file you are reading


System requirements
-------------------

1. Python 3

    Specifically, Python 3.3 or above is needed.

2. The Python package PyPDF2 (https://pypi.python.org/pypi/PyPDF2)

    We need this to manipulate PDF files in Python. Be sure to install it for your Python 3 distribution.
    Since PyPDF2 is distributed through PyPI,
    if you are on Ubuntu (or similar Linux/Unix-based systems) with `pip3`,
    do this (or equivalent):

    ```
    $ sudo pip3 install PyPDF2
    ```

3. The `pdflatex` program

    The `pdflatex` command has to be available in your path
    so that we can call it to compile LaTeX documents.
    If you are on Linux or Mac and TeX Live is installed, then you probably
    have `pdflatex` already.


Requirements for the input PDF files
------------------------------------

All the PDFs needed to compile the CLS proceedings must all be in a working directory --
let's call this directory [`example`](example) (as shown in this repository).

Inside `example`, all PDFs must be organized in the following way in order for `cls-compile.py` to work:

```
example/
    front-matter/
        front-matter.pdf
    acknowledgments/
        acknowledgments.pdf
    papers-without-headers/
        <all individual paper PDFs without headers or paper numbers>
    templates/
        headers.tex
        table-of-contents.tex
        blank.pdf
    organizer.csv
```

Four folders are expected inside `example`; a fifth required item right under `example`---`organizer.csv`---is explained in the next section.


* `front-matter`

    Only one PDF file is expected in this folder, e.g., [`front-matter.pdf`](example/front-matter/front-matter.pdf)
    (`.tex` template also provided).
    `cls-compile.py` ignores all other non-PDF files in this folder, so you may work within this folder
    with LaTeX files etc to generate the required PDF file.

* `acknowledgments`

    Only one PDF file is expected in this folder, e.g., [`acknowledgments.pdf`](example/acknowledgments/acknowledgments.pdf)
    (`.tex` template also provided).
    `cls-compile.py` ignores all other non-PDF files in this folder, so you may work within this folder
    with LaTeX files etc to generate the required PDF file.

* `papers-without-headers`

    This folder contains all PDFs of the individual papers without headers or page numbers.

* `templates`

    This folder contains templates and other files needed. Do NOT change their names, though the contents of `headers.tex` and `table-of-contents.tex` can be updated if necessary.
    `blank.pdf` is the blank PDF page inserted here and there in the final proceedings PDF so that all items start on the right-hand side in the printed volume.


The organizer CSV file
----------------------

An organizer CSV file (e.g., [`organizer.csv`](example/organizer.csv)) is required to provide the essential information about authors, paper titles, etc.
This CSV file must contain six columns with the following header names:

1. `index`

    An index number just for convenience. The first paper is 1 and so forth.
    Note that the order of the rows in this CSV file (regardless of what the column `index` says) determines the order by which the papers appear in the proceedings PDF.
    So make sure all the papers are ordered correctly in the organizer by alphabetical order of first authors' last names or whatever the CLS members would like.

2. `authors`

    The cell for each paper in the column `authors` shows exactly how author names appear in the table of contents.
    Use LaTeX formatting for non-ASCII characters (accented characters etc).

3. `paper title`

    (similar to `authors` above)

4. `authors in header`

    The cell for each paper in the column `authors in header` shows exactly how author names appear in
    the paper's header. Use LaTeX formatting for non-ASCII characters (accented characters etc).
    The cell content will be forced to be in the uppercase in the output proceedings PDF.

    If this cell is empty, then the cell content from `authors` for the paper in question will be used.

    Note the cell content of `authors in header` cannot exceed a certain character length
    (controlled by the optional argument `--maxheaderlength` for `cls-compile.py`;
    more on running `cls-compile.py` below)
    because the header naturally cannot accommodate something too long that would go over one line
    or cover up the page number.
    So for a paper with a long chain of author names,
    you have to put down something much shorter here (e.g., only the lastnames?).

5. `paper title in header`

    (similar to `authors in header` above)

6. `paper filename`

    The cell for each paper in the column `paper filename` shows the paper's filename (e.g., `smith.pdf`)
    as it appears in the folder `papers-without-headers`.


How to run `cls-compile.py`
---------------------------

If you have a working directory like `example` with all necessary files properly organized by the guidelines here,
then do this at your current directory where `cls-compile.py` is:

```
$ python3 cls-compile.py --directory=<relative-path-to-your-working-directory>
```

`python3` is meant to point to your Python 3 interpreter, one that recognizes the PyPDF2 package as well.

If you don't provide `--directory=<relative-path-to-your-working-directory>`
(i.e., if you run `python3 cls-compile.py` without any arguments),
`cls-compile.py` assumes the `example` folder is at the current directory and it is your working directory.

`cls-compile.py` allows various optional arguments for changing file/folder names etc.
Please run `python3 cls-compile.py -h` for details.
Among the array of optional parameters, you may be interested in the following:

* `--maxheaderlength`

    The maximum length (by number of characters) of the author or paper title headers in the
    paper PDFs (default: 55). This cap ensures that the header does not go over one line or
    cover up the page number in the header. To change the value to, say, 60, do something like
    `python3 cls-compile.py --maxheaderlength=60`.

* `--startpagenumber`

    The starting page number of the first paper by order in the proceedings volume (default: 1).

Multiple optional parameters are possible, in the form of `python3 cls-compile.py --<parametername1>=<parametervalue1> --<parametername2>=<parametervalue2>`.


Outputs
-------

If you run `cls-compile.py` and all goes well, the final proceedings PDF output should be sitting right inside your working directory (hurray!).
In addition, all intermediate files are kept for reference.
Inside the working directory, you should see the new folders `table-of-contents`, `headers`, and `papers-with-headers` (all individual paper PDFs nicely typeset with headers and page numbers here!).

Note that if you are running `cls-compile.py` multiple times, all already-existing contents inside
the folders `table-of-contents`, `headers`, and `papers-with-headers` will be removed at each run to ensure clean output files.


Technical support etc
---------------------

CLS officers are welcome to contact [Jackson Lee](http://jacksonllee.com/) for any questions regarding this tool.


Updates
-------

Please feel free to make changes to `cls-compile.py` as the CLS publication guidelines evolve. Git pull requests to incorporate changes into this repository are more than welcome; alternatively, you may also contact Jackson.


Dev notes
---------

The overarching strategies of `cls-compile.py`:

* Check if everything needed is in place before any PDF manipulation is done
* Issue an error (and exit) as soon as one is detected

Everything in the proceedings volume that comes after the table of contents is
treated as "papers". This means that, for instance,
if something like a prompt page to introduce the main
session or parasession papers is desired, it should be treated as a "paper" and
included in the organizer CSV file (but we probably don't want headers and page
numbers for these -- need some way to handle this).

The code by and large follows the [PEP8](https://www.python.org/dev/peps/pep-0008/) code style guide.
