SUBMAKES_REQUIRED=logo/mu locale style style/mu
SUBMAKES_MISCELLANEOUS=guide/mu test
SUBMAKES=$(SUBMAKES_REQUIRED) $(SUBMAKES_MISCELLANEOUS)
.PHONY: all complete clean dist dist-implode implode \
	install uninstall test $(SUBMAKES_REQUIRED)

CLASSFILES=fithesis.cls fithesis2.cls fithesis3.cls
STYLEFILES=style/*.sty style/*/*.sty style/*/*.clo
EPSLOGOS=logo/*/*.eps
PDFLOGOS=logo/*/*.pdf
LOGOS=$(EPSLOGOS) $(PDFLOGOS)
LOCALES=locale/*.def locale/*/*.def locale/*/*/*.def
DTXFILES=*.dtx locale/czech.dtx locale/english.dtx \
	locale/slovak.dtx style/*.dtx style/*/*.dtx
INSFILES=*.ins locale/czech.ins locale/english.ins \
	locale/slovak.ins style/*.ins style/*/*.ins
TESTS=test/*.tex
MAKES=Makefile */Makefile */*/Makefile */*/*/Makefile
USEREXAMPLES=../*.tex ../Makefile ../DESCRIPTION
DEVEXAMPLES=guide/EXAMPLE/DESCRIPTION guide/mu/DESCRIPTION \
	guide/DESCRIPTION locale/DESCRIPTION locale/EXAMPLE.dtx \
	locale/EXAMPLE.ins logo/EXAMPLE/DESCRIPTION logo/mu/DESCRIPTION \
	logo/DESCRIPTION style/EXAMPLE/DESCRIPTION style/mu/DESCRIPTION \
	style/DESCRIPTION test/DESCRIPTION
EXAMPLES=$(USEREXAMPLES) $(DEVEXAMPLES)
MISCELLANEOUS=guide/*/*.tex guide/*/*/*.tex guide/*/*.bib
RESOURCES=$(STYLEFILES) $(LOGOS) $(LOCALES)
SOURCES=$(DTXFILES) $(INSFILES) LICENSE.tex
AUXFILES=fithesis.aux fithesis.log fithesis.toc fithesis.ind \
	fithesis.idx fithesis.out fithesis.ilg fithesis.gls \
	fithesis.glo fithesis.hd
MANUAL=fithesis.pdf
PDFSOURCES=fithesis.dtx
GUIDES=guide/mu/econ.pdf guide/mu/fi.pdf guide/mu/fsps.pdf \
	guide/mu/fss.pdf guide/mu/law.pdf guide/mu/med.pdf \
	guide/mu/ped.pdf guide/mu/phil.pdf guide/mu/sci.pdf
PDFS=$(MANUAL) $(GUIDES)
DOCS=$(PDFS) README
VERSION=VERSION.tex
MAKEABLES=$(PDFS) $(CLASSFILES) $(VERSION)
TDSARCHIVE=fithesis.tds.zip
CTANARCHIVE=fithesis.ctan.zip
DISTARCHIVE=fithesis.zip
ARCHIVES=$(TDSARCHIVE) $(CTANARCHIVE) $(DISTARCHIVE)
LATEXFILES=$(CLASSFILES) $(RESOURCES)

TEXLIVEDIR=$(shell kpsewhich -var-value TEXMFLOCAL)

# This pseudo-target expands all the docstrip files, converts the
# logos and creates the class files.
all: $(SUBMAKES_REQUIRED)
	make $(CLASSFILES)

# This pseudo-target creates the class files and typesets the
# technical documentation and the guides.
complete: all
	make $(PDFS) clean

# This pseudo-target calls a submakefile
$(SUBMAKES_REQUIRED):
	make -C $@ all

# This pseudo-target performs the unit tests
test: all
	make -C test

# This pseudo-target creates the distribution archive.
dist: complete
	make $(TDSARCHIVE) $(DISTARCHIVE) $(CTANARCHIVE)

# This target creates the class files.
$(CLASSFILES): fithesis.ins fithesis.dtx
	xetex $<

# This target typesets the guides.
$(GUIDES): $(CLASSFILES) $(RESOURCES)
	make -BC $(dir $@)

# This target typesets the technical documentation.
$(MANUAL): $(DTXFILES)
	pdflatex $<
	makeindex -s gind.ist $(basename $@)
	makeindex -s gglo.ist -o $(basename $@).gls $(basename $@).glo
	pdflatex $<
	pdflatex $<

# This target generates a TeX directory structure file
$(TDSARCHIVE):
	DIR=`mktemp -d` && \
	make install to="$$DIR" nohash=true && \
	(cd "$$DIR" && zip -r -v -nw $@ *) && \
	mv "$$DIR"/$@ $@ && rm -rf "$$DIR"

# This target generates a distribution file
$(DISTARCHIVE): $(SOURCES) $(LATEXFILES) $(MAKES) $(TESTS) \
	$(DOCS) $(PDFSOURCES) $(MISCELLANEOUS) $(EXAMPLES) $(VERSION)
	DIR=`mktemp -d` && mkdir -p "$$DIR/fithesis" && \
	cp --verbose $(TDSARCHIVE) "$$DIR"/fithesis && \
	cp --parents --verbose $^ "$$DIR/fithesis" && \
	(cd "$$DIR" && zip -r -v -nw $@ *) && \
	mv "$$DIR"/$@ . && rm -rf "$$DIR"

# This target generates a CTAN distribution file
$(CTANARCHIVE): $(SOURCES) $(MAKES) $(TESTS) \
	$(MISCELLANEOUS) $(EPSLOGOS) $(DOCS) $(DEVEXAMPLES) $(VERSION)
	DIR=`mktemp -d` && mkdir -p "$$DIR/fithesis" && \
	cp --verbose $(TDSARCHIVE) "$$DIR" && \
	cp --parents --verbose $^ "$$DIR/fithesis" && \
	(cd "$$DIR" && zip -r -v -nw $@ *) && \
	mv "$$DIR"/$@ . && rm -rf "$$DIR"

# This pseudo-target installs the class files and the technical
# documentation into the TeX directory structure, whose root
# directory is specified within the "to" argument. Specify
# "nohash=true", if you wish to forgo the reindexing of the package
# database.
install:
	@if [ -z "$(to)" ]; then \
		printf "Usage: make install to=DIRECTORY"; \
		printf "Detected TeXLive directory: %s\n" $(TEXLIVEDIR); \
		exit 1; \
	fi
	
	# Class, locale, style and logo files
	mkdir -p "$(to)/tex/latex/fithesis"
	cp --parents --verbose $(LATEXFILES) "$(to)/tex/latex/fithesis"
	
	# Source files
	mkdir -p "$(to)/source/latex/fithesis"
	cp --parents --verbose $(SOURCES) "$(to)/source/latex/fithesis"
	
	# Documentation
	mkdir -p "$(to)/doc/latex/fithesis"
	cp --parents --verbose $(DOCS) "$(to)/doc/latex/fithesis"
	
	# Rebuild the hash
	[ "$(nohash)" = "true" ] || texhash

# This pseudo-target installs the class files and the technical
# documentation into the TeX directory structure, whose root
# directory is specified within the "to" argument. Specify
# "nohash=true", if you wish to forgo the reindexing of the package
# database.
uninstall:
	@if [ -z "$(from)" ]; then \
		printf "Usage: make uninstall from=DIRECTORY"; \
		printf "Detected TeXLive directory: %s\n" $(TEXLIVEDIR); \
		exit 1; \
	fi
	
	# Class, locale, style and logo files
	rm -rf "$(from)/tex/latex/fithesis"
	
	# Source files
	rm -rf "$(from)/source/latex/fithesis"
	
	# Documentation
	rm -rf "$(from)/doc/latex/fithesis"
	
	# Rebuild the hash
	[ "$(nohash)" = "true" ] || texhash

# This pseudo-target removes any existing auxiliary files.
clean:
	rm -f $(AUXFILES)

# This pseudo-target removes the distribution archives.
dist-implode:
	rm -f $(ARCHIVES)

# This pseudo-target removes any makeable files.
implode: clean dist-implode
	rm -f $(MAKEABLES)
	for dir in $(SUBMAKES); do make implode -C "$$dir"; done
