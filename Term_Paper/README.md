# LNI Proceedings

This repository supports generating of proceedings based on the "Lecture Notes in Informatics" papers typeset using the [lni class](https://www.ctan.org/pkg/lni).
An example output is available at <https://gi-ev.github.io/LNI-proceedings/>.


## Table of Contents

<!-- toc -->

- [Success stories](#success-stories)
- [Aims of this work](#aims-of-this-work)
- [Howto](#howto)
  * [System setup](#system-setup)
    + [Recommended setup of MiKTeX](#recommended-setup-of-miktex)
    + [pax](#pax)
    + [Python 2.7](#python-27)
    + [Linux commands available at cmd.executed](#linux-commands-available-at-cmdexecuted)
  * [Proceedings](#proceedings)
    + [Advanced usage](#advanced-usage)
  * [Submitting to the GI and the printing service](#submitting-to-the-gi-and-the-printing-service)
- [FAQ](#faq)
- [Trouble shooting of compiled papers](#trouble-shooting-of-compiled-papers)
- [Implementation Documentation](#implementation-documentation)
- [Considered alternatives](#considered-alternatives)
  * [confproc](#confproc)
  * [combine](#combine)
  * [proc](#proc)
  * [Springer Computer Science Proceedings](#springer-computer-science-proceedings)
- [License](#license)
- [Further reading](#further-reading)

<!-- tocstop -->

## Success stories

Following proceedings were typeset using this template:

* [BTW 2017](https://www.gi.de/service/publikationen/lni/gi-edition-proceedings-2017/gi-edition-lecture-notes-in-informatics-lni-p-265.html)
* [BTW 2017 Workshopband](https://www.gi.de/service/publikationen/lni/gi-edition-proceedings-2017/gi-edition-lecture-notes-in-informatics-lni-p-266.html)


## Aims of this work

- Generate proceedings conforming with GI's requirements stated at the "[Herausgeberrichtlinien](https://www.gi.de/service/publikationen/lni/autorenrichtlinien.html)".
- Automatic generation of
  - running heads (including page numbers, authors, title of the paper)
  - table of contents
  - PDF bookmarks
  - index
  - `proceedings.bib` listing all papers including page numbers
- Working hyperlinks
  - from the TOC to the papers
  - within the papers
  - from the index to the papers


## Howto


### System setup

This section describes the setup of software required.
This howto is based on a Windows environment.
Linux users should have ready most of the tools required.


#### Recommended setup of MiKTeX

MiKTeX should be installed in a single-user setup to avoid troubles when updating packages.
Furthermore, it should be installed at `C:\MiKTeX` to enable easy installation of the pax utility.
Otherwise, you have to follow the steps described at <http://tex.stackexchange.com/a/108490/9075> to keep your MiKTeX distribution updated.

* Download the basic installer from http://miktex.org/download
* Start it
* First screen: Read the license condiditions and be sure that you really agree.
* Second screen: "Shared Installation": Install MiKTeX for: "Only for: `username`"
* Third screen: "Installation Directory": Install MiKTeX to: `C:\MiKTeX`. This enabled browsing for documentation at `C:\MiKTeX29\doc\latex`
* Fourth screen: "Settings": Preferred paper: A4 and Install missing packages on the fly: `Yes`
* Fifth screen: Press "Start"
* After the installation:
  1. Open `cmd.exe`
  2. Execute `mpm --update-db`
  3. Execute `mpm --update`
  4. Execute `mpm --install=cm-super`
  5. Execute `initexmf --update-fndb`


#### pax

[pax](http://ctan.org/pkg/pax) is a utitlity which enables hyperlinks still working when combining PDFs using pdflatex.
In the installation, we rely on [chocolatey](https://chocolatey.org/), because it eases installation much.

- Installl java runtime environment using `choco install jre8`. [chocolatey page](https://chocolatey.org/packages/jre8).
- Install unzip, wget, and curl using `choco install unzip wget curl`.
- Install perl using `choco install strawberryperl`. [chocolatey page](https://chocolatey.org/packages/StrawberryPerl).
- Install pax using the MiKTeX package manager
- Execute `perl C:\MiKTeX\scripts\pax\pdfannotextractor.pl --install` to enable downloading of a pdfbox version fitting for pax.
- Ignore the error regarding "MiKTeX Configuration Utility"
- Start "MiKTeX Settings"
- Click on "Refresh FNDB"
- Click on "Update Formats"
- Now, `pdfannotextractor.pl` is ready to go

Source for installing pax: http://tex.stackexchange.com/a/44104/9075


#### Python 2.7

This is required to automatically extract the authors and title from the papers source texs.

1. Install Python 2.7: `choco install python2`
2. Install pip
  - wget https://bootstrap.pypa.io/get-pip.py
  - `c:\Python27\python get-pip.py`
3. Install `pyparsing`
  - `c:\Python27\Scripts\pip install pyparsing`


#### Linux commands available at cmd.executed

We need `sed` being available at a cmd.exe shell.
This should be available when you executed `choco install git`.


### Proceedings

1. Check that LNI-Startseiten.docx is the latest version retrieved from <https://www.gi.de/fileadmin/redaktion/Autorenrichtlinien/LNI-Startseiten.docx>.
2. Adapt `LNI-Startseiten.docx` to your conference.
3. Adapt `pages=5-6` at `\includepdf[pagecommand={\thispagestyle{empty}},pages=5-6]{LNI-Startseiten.pdf}` to match the page numbers of your foreword and sponsoring.
4. Get the cover page ready. Template is available at <https://www.gi.de/fileadmin/redaktion/Autorenrichtlinien/LNI-Cover-Vorlage.ppt>.
  This preparation gives you the necessary information for step 5.
  You also need to submit the cover to the GI and to the printing service.
5. Adapt `config.tex` to your conference.
  Here, you also set the [DOI](https://en.wikipedia.org/wiki/Digital_object_identifier) prefix used for generating a unique DOI for each paper.
6. Create all paper folders using a naming scheme. See `papers/naming-scheme.txt`.
   `[Category][NumberOfSubcategory]-[NumberWithinSession]`.
7. Collect all papers. Place the source and the pdf within each paper's folder.
   For instance, the first paper goes into `papers/A-1/`.
   Name the PDFs of each paper `paper.pdf`.
8. Check for all `paper.tex` that all authors are the format `\author[Firstname Lastname \and ...]{...}`
9. Copy the author information from each `paper.tex` into `proceedings.tex`:
  - Open a [git bash](https://git-for-windows.github.io/)
  - cd into `papers`
  - `/c/Python27/python ../addAuthTi.py ../proceedings.tex */paper.tex > ../proceedings.x`
  - `cd ..`
  - `mv proceedings.x proceedings.tex`
  - For debugging: `/c/Python27/python ../addAuthTi.py ../proceedings.tex */paper.tex`
10. Fix spaces before `\and`: Replace ` \and` by `\and`.
   Reason: `\unskip` does nothing at `\texorpdfstring` in combination with hyperref
11. Create pax information: Execute `prepare-papers.bat`.
12. Execute `pdflatex -synctex=1 proceedings.tex` to see whether pdflatex gets through.
13. Execute `make-proceedings.bat` to execute all required steps
14. Check proceedings and make necessary adaptions. During the fixup phase, you can run `pdflatex -synctex=1 proceedings` to quickly build the proceedings. Nevertheless, run `make-proceedings.bat` every now and then to ensure a correctly generated index.
15. Ensure that crop is *not* activated: `\let\ifcrop\iffalse` in `proceedings.tex`
16. Execute `make-proceedings.bat`

During the process, following files are generated:

- `proceedings.pdf`. 
  It is not recommended to version this file during the process of proceedings generation, because it gets very large.
- `proceedings.bib` - BibTeX bibliography of the proceedings.
- `proceedings.csv` - CSV containing some information on the proceedings.
- `papers.txt` - list of paper id and starting page.


#### Advanced usage

It is possible to update the pages information in each paper's `paper.tex`.
Although this is uncessary, because of `cut-proceedings.sh`.
In case `cut-proceedings.sh` does not work on your side, this alternative way can be used.

1. Execute `generate-updatepages.sh-from-pages-txt.sh`.
   This generates `update-pages.sh`.
2. Execute `sh update-pages.sh`.
3. Recompile all pdfs.


### Submitting to the GI and the printing service

1. Check proceedings.pdf whether all fonts are embedded. In case some fonts are not embedded, follow folling steps:
  - go to the folder of the paper
  - locate the PDF containing the picture
  - embed the font using Acrobat Professional's preflight functionality
  - Recompile the paper (`pdflatex paper`, ...)
  - Recompile the proceedings (`pdflatex proceedings`)
2. Submit `proceedings.pdf` and `LNI-Cover-Vorlage.ppt` (see step 4 above) to the GI for approval.
3. After the approval, submit to the printing service.


## FAQ

Q: Some papers do not contain the short author list (i.e., `\author{...authorlist...}` instead of `\author[...authorlist...]{...authorlist...}`. <br>
A: Use the online service <https://regex101.com/>.
  The regex is `(\\footnote{.*})|(\\footnotemark\[.\])|%.*|\\textsuperscript{.*}`.
  Paste the `\author` content to "Test String" and expand "Substituation" at the bottom.

Q: The number of pages changed. What should I do? <br>
A: `pdflatex proceedings`, do it twice to be sure that the TOC is created correctly and that the TOC has more than one page.
   Continue at "Update the page numbers" above

Q: What can I do if the hyperlinks in the proceedings do not work? <br>
A: Run `pdflatex proceedings` one more time, because pax needs one more run.

Q: What if a paper needs adjustments? <br>
A : Sometimes, the GI required adjustments.
For instance, if an author did not use the LNI style for the bibliography.
You can either ask the authors directly or do it for yourself.
In case you decide to adjust the paper for yourself, replace `\editor{...}` and `\booktitle{...}` by `\input{../../config.tex}` to ensure that all papers have the same conference configuration.

Q: I recompiled some papers. How can I check for errors? <br>
A: Use [ack](https://beyondgrep.com/) to globally check for errors - run it from root directory to be sure everything compiled well. Insall it using [choco install ack](https://chocolatey.org/packages/ack).
  - `ack "LaTeX Warning: Label(s) may have changed."`
  - `ack "Package hyperref Warning: Token not allowed in a PDF string (PDFDocEncoding)"`

Q: How can I get the PDFs with the correct headers? <br/>
A: Execute `cut-proceedings.sh proceedings.pdf`. [pdftk](https://www.pdflabs.com/tools/pdftk-server/) and ghostscript installed.

Q: Some papers are cut strangely and the PDF is broken. What can I do? <br />
A: The authors use an old version of the template.
  Please ask them to update to the new version 1.1, available at <https://www.ctan.org/tex-archive/macros/latex/contrib/lni>.
  You can also update the `paper.tex` file for yourself.
  The differences are not too much.
  Finally, you could try to adapt `\addpaperWRONGLAYOUT`.
  That command is made for inclusion of papers of the old format.
  However, it is currently not maintained and may produce wrong output.

Q: Is it possible to show or not to show the page margins? <br />
A: Yes. The usage of the [crop package](https://www.ctan.org/pkg/crop) is prepared.
- Change `\let\ifcrop\iffalse` to `\let\ifcrop\iftrue` to show the crop lines.
- Change `\let\ifcrop\iftrue` to `\let\ifcrop\iffalse` to disable showing the crop lines.


## Trouble shooting of compiled papers

If you are in need to recompile the submitted papers, there might be errors occurring.
This section provides hints on some of the most prominent errors.

* `\openbox already defined` -> `\let\openbox\relax` before `\usepackage{amsthm}`
* If you get something about `\spacing in math mode`, ensure that your bib file is correct and that you re-ran bibtex.
* `! Undefined control sequence.` <br />
  `l.27 \ulp@afterend` <br />
    You have used pdfcomments package, but you disabled it.
    Delete `paper.aux` and recompile.
* `! You can't use ``\spacefactor' in internal vertical mode.`: Currently unknown


## Implementation documentation

This section discusses some design decisions done when implementing this way to generate proceedings.

`\IeC` is written into `authors.bib`.
This issue is discussed at http://tex.stackexchange.com/q/234501/9075.
Since the file is encoded in ASCII characters, we just need to strip out `\IeC`.
This is done using [sed](https://en.wikipedia.org/wiki/Sed).


## Considered alternatives

When designing this solution to typeset complete proceedings, several alternatives were investigated.
Nearly all possible alternatives are listed at <http://www.ctan.org/topic/confproc>.
In the following, evaluated alternatives are listed and discussed.


### confproc

[confproc](http://www.ctan.org/pkg/confproc) seems to the most suitable alternative.
Compared with this approach, it has following drawbacks:

- The PDFs of the papers do not take a proper heading (page numbers, editor).
- When clicking on a link in one included PDF, the linked PDF is opened instead of jumping to the link.
- Indexing of authors has to be done by manually.


### combine

The [combine](https://www.ctan.org/pkg/combine) class combines the sources of different LaTeX together.
Since there might be conflicting packges, we wanted to include each PDF on its own.
The PDFs can be typeset by itself.


### proc

[proc](http://www.ctan.org/pkg/proc) is a very basic class based on the article class.
No update since 1995.


###  Springer Computer Science Proceedings

Springer offers help for proceedings authors at <https://www.springer.com/gp/computer-science/lncs/editor-guidelines-for-springer-proceedings>.
It uses makeindex instead of biblatex for index generation.
We opted for biblatex+texindy to be UTF-8 save and to directly be able to use the content of `\authors` for index generation.


## License

This work is licensed under [CC0](https://creativecommons.org/publicdomain/zero/1.0/), so you especially can create proceedings out of it.
Feedback is welcome at the [GitHub page](https://github.com/latextemplates/LNI-proceedings).

`shrinkpdf.sh` is BSD-licensed.

## Further reading

* tex.stackexchange: [Constructing conference proceedings](http://tex.stackexchange.com/q/124942/9075)
