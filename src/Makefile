TITLE="Beej's Guide to C Programming"
SUBTITLE=""
AUTHOR='Brian “Beej Jorgensen” Hall'
VERSION_DATE="v0.6.2, Copyright © April 19, 2021"

GUIDE_ID=bgc

PDF_MAINFONT="Liberation Serif"
PDF_SANSFONT="Liberation Sans"
PDF_MONOFONT="Liberation Mono"
#PDF_MAINFONT="DejaVu Serif"
#PDF_SANSFONT="DejaVu Sans"
#PDF_MONOFONT="DejaVu Sans Mono"

USLETTER_COLOR=$(GUIDE_ID)_usl_c_1.pdf $(GUIDE_ID)_usl_c_2.pdf
USLETTER_BW=$(GUIDE_ID)_usl_bw_1.pdf $(GUIDE_ID)_usl_bw_2.pdf
A4_COLOR=$(GUIDE_ID)_a4_c_1.pdf $(GUIDE_ID)_a4_c_2.pdf
A4_BW=$(GUIDE_ID)_a4_bw_1.pdf $(GUIDE_ID)_a4_bw_2.pdf
BOOKS=$(USLETTER_BW) $(USLETTER_COLOR) $(A4_BW) $(A4_COLOR)

HTML=$(GUIDE_ID).html $(GUIDE_ID)-wide.html
GUIDE_MD=$(sort $(wildcard $(GUIDE_ID)_part_*.md))

PREPROC=../bin/preproc
WORKDIR=./work
PREPROC_MD=$(WORKDIR)/preproc.md

COMMON_OPTS= \
	--variable title:$(TITLE) \
	--variable subtitle:$(SUBTITLE) \
	--variable author:$(AUTHOR) \
	--variable date:$(VERSION_DATE) \
	--number-sections \
	--toc

PDF_OPTS= \
	-H latex/header_index.latex \
	-A latex/after_index.latex \
	--pdf-engine=xelatex \
	--variable mainfont=$(PDF_MAINFONT) \
	--variable sansfont=$(PDF_SANSFONT) \
	--variable monofont=$(PDF_MONOFONT) \
	--variable geometry:"top=1in,bottom=1in" \
	-V documentclass=book \
	$(COMMON_OPTS)
	#-V indent \

HTML_OPTS=$(COMMON_OPTS) \
	--metadata title:$(TITLE) \
	--mathjax

ONESIDE=--variable classoption:oneside
TWOSIDE=--variable classoption:twoside
USLETTER=--variable papersize:letter
A4=--variable papersize:a4
CROWNQUARTO=--variable geometry:"paperwidth=7.444in,paperheight=9.681in,top=1in,bottom=1in,left=1in,right=1.5in" # Lulu press
CROWNQUARTO_AMAZON=--variable geometry:"paperwidth=7.444in,paperheight=9.681in,top=1in,bottom=1in,left=1.25in,right=1.25in" # Amazon
#SIZE_75x925_AMAZON=--variable geometry:"paperwidth=7.5in,paperheight=9.25in,top=1in,bottom=1in,left=1.125in,right=1.375in" # Amazon 7.5" x 9.25", margins too far inside
SIZE_75x925_AMAZON=--variable geometry:"paperwidth=7.5in,paperheight=9.25in,top=1in,bottom=1in,left=1.25in,right=1.25in" # Amazon 7.5" x 9.25"
BLANKLAST=-A latex/after_blank.latex # add a blank last page
BW=--no-highlight  # black and white options

all: $(HTML) $(BOOKS)

$(WORKDIR):
	mkdir -p $@

$(PREPROC_MD): $(GUIDE_MD) | $(WORKDIR)
	$(PREPROC) $^ $(PREPROC_MD)

$(WORKDIR)/bg-css.html: common-css-src.html | $(WORKDIR)
	cat $^ > $@

$(WORKDIR)/bg-css-wide.html: common-css-src.html widescreen-css-src.html | $(WORKDIR)
	cat $^ > $@

$(GUIDE_ID).html: $(PREPROC_MD) $(WORKDIR)/bg-css.html
	pandoc $(HTML_OPTS) -s $(PREPROC_MD) -o $@ -H $(WORKDIR)/bg-css.html
	sed 's/src="\(.*\)\.pdf"/src="\1.svg"/g' $@ > $@.tmp # use svg images
	mv $@.tmp $@

$(GUIDE_ID)-wide.html: $(PREPROC_MD) $(WORKDIR)/bg-css-wide.html
	pandoc $(HTML_OPTS) -s $(PREPROC_MD) -o $@ -H $(WORKDIR)/bg-css-wide.html
	sed 's/src="\(.*\)\.pdf"/src="\1.svg"/g' $@ > $@.tmp # use svg images
	mv $@.tmp $@

$(WORKDIR)/$(GUIDE_ID)_usl_c_1.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(USLETTER) $(ONESIDE) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_usl_c_2.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(USLETTER) $(TWOSIDE) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_a4_c_1.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(A4) $(ONESIDE) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_a4_c_2.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(A4) $(TWOSIDE) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_usl_bw_1.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(USLETTER) $(ONESIDE) $(BW) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_usl_bw_2.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(USLETTER) $(TWOSIDE) $(BW) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_a4_bw_1.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(A4) $(ONESIDE) $(BW) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_a4_bw_2.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(A4) $(TWOSIDE) $(BW) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_lulu.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(TWOSIDE) $(CROWNQUARTO) $(BLANKLAST) -o $@ $<

$(WORKDIR)/$(GUIDE_ID)_amazon.tex: $(PREPROC_MD)
	pandoc $(PDF_OPTS) $(TWOSIDE) $(SIZE_75x925_AMAZON) $(BLANKLAST) -o $@ $<

%.pdf: $(WORKDIR)/%.tex
	cd $(WORKDIR); xelatex $*.tex; makeindex $*.idx; xelatex $*.tex; xelatex $*.tex
	mv $(WORKDIR)/$*.pdf $@

clean:
	rm -rf $(WORKDIR)

pristine: clean
	rm -f $(HTML) $(BOOKS)

.PHONY: all, clean, pristine
