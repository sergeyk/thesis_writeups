# Paths to the programs used by this Makefile.
PDFLATEX = pdflatex
BIBTEX = bibtex
RM = rm
CP = cp
GREP = grep
DIFF = diff

# Paths to the input and output files.  These can be changed.
TEXFILE = poster.tex
BIBFILE = poster.bib
PDFFILE = $(basename $(TEXFILE)).pdf

FIG_DIR = figures
FIGFILES = $(wildcard $(FIG_DIR)/*.fig)
EPSFILES = $(wildcard $(FIG_DIR)/*.eps)
PDF_FILES = $(addsuffix .pdf, $(basename $(FIGFILES) $(EPSFILES)))
PDFTEX_FILES = $(addsuffix .pdftex, $(basename $(FIGFILES)))
FIGURES = $(PDF_FILES) $(PDFTEX_FILES) 

# Paths to intermediate files that are created by the make process.
# Do not change these.
TEXLOG = $(basename $(TEXFILE)).log
BIBLIOGRAPHYFILE = $(basename $(TEXFILE)).bbl
BIBTEXLOG = $(basename $(TEXFILE)).blg
OUTFILE = $(basename $(TEXFILE)).out
AUXFILE = $(basename $(TEXFILE)).aux
MISCAUXFILE = $(wildcard *.aux)
BRFFILE = $(basename $(TEXFILE)).brf
OLDAUXFILE = ".$(basename $(TEXFILE)).aux.old"

# If this message appears in TEXLOG, we must re-run LaTeX.
RERUNLATEX = "Rerun to get cross-references right"

# The primary target (created by the command "make") is the PDF file.
pdf: $(PDFFILE)

# This rule builds a bibliography file for the first time.
$(BIBLIOGRAPHYFILE): $(BIBFILE)
	if ! [ -e $(AUXFILE) ]; then $(PDFLATEX) $(TEXFILE); fi
	$(BIBTEX) $(basename $(TEXFILE))

# This rule uses PDFLaTeX to create a PDF file from a LaTeX file.  If
# the AUX file has changed.  PDFLaTeX is
# re-run to resolve references, if necessary.
$(PDFFILE): $(TEXFILE) $(BIBLIOGRAPHYFILE) $(FIGURES)
	$(CP) $(AUXFILE) $(OLDAUXFILE)
	$(PDFLATEX) $<
	if ! $(DIFF) -q $(AUXFILE) $(OLDAUXFILE); then $(BIBTEX) $(basename $<); $(PDFLATEX) $<; fi
	while $(GREP) -q $(RERUNLATEX) $(TEXLOG); do $(PDFLATEX) $<; done

# This rule cleans out the files made by the make process.
clean:
	$(RM) -f $(MISCAUXFILE) \
                 $(BRFFILE) \
                 $(OLDAUXFILE) \
                 $(TEXLOG) \
                 $(OUTFILE) \
                 $(BIBLIOGRAPHYFILE) \
                 $(BIBTEXLOG) \
                 $(PDFFILE) \
		 *~
run:
	open $(PDFFILE)

# Generic patterns
.SUFFIXES: .fig .pstex .pstex_t .pdf .pdftex

%.pstex: %.fig
	fig2dev -L pstex $< > $@

%.pstex_t: %.fig %.pstex
	fig2dev -L pstex_t -p $*.pstex $*.fig > $@

%.pdf: %.pstex
	epstopdf $< > $@

%.pdf: %.eps
	epstopdf $< > $@

%.pdftex: %.pstex_t %.pdf
	sed "s/$(notdir $*).pstex/$(notdir $*).pdf/" $*.pstex_t > $@
