
include ../Makefile.conf

PACKAGES=compiler-libs.common
# cmdliner
#base64 yojson

ifeq "${WITH_FINDLIB}" "YES"
PACKAGES+= findlib
FINDLIB_SUPPORT=findlib_support/jsoo_findlib_support.cma \
                findlib_support/jsoo_findlib_support.cmx \
                findlib_support/jsoo_findlib_support.cmxa \
                findlib_support/jsoo_findlib_support.cmxs
endif

all: $(COMPILER) $(MINIFIER) $(LINKER) $(FINDLIB_SUPPORT) man

compilerlib: lib/js_of_ocaml_compiler.cma lib/js_of_ocaml_compiler.cmxa lib/js_of_ocaml_compiler.cmxs lib/js_of_ocaml_compiler.cmx


COMPOBJS=cmdliner.cmx commonArg.cmx compileArg.cmx jsoo_compile.cmx
MINIOBJS=cmdliner.cmx commonArg.cmx minifyArg.cmx minify.cmx
LINKEROBJS=cmdliner.cmx commonArg.cmx linkerArg.cmx jsoo_link.cmx
#OCAMLC=ocamlfind ocamlc -g  -w +A-4-7-9-37-38-41-44-45-58
#OCAMLOPT=ocamlfind ocamlopt -g -w +A-4-7-9-37-38-41-44-45-58
OCAMLC=ocamlc -g  -w +A-4-7-9-37-38-41-44-45-58
OCAMLOPT=ocamlopt -g -w +A-4-7-9-37-38-41-44-45-58
BYTE_COMP=lib/js_of_ocaml_compiler.cmo
NAT_COMP=lib/js_of_ocaml_compiler.cmx

lib/%:
	make -C lib $*

findlib_support/%:
	make -C findlib_support $*

$(COMPILER): jsoo_compile.$(BEST)
	mv -f $< $@

$(MINIFIER): minify.$(BEST)
	mv -f $< $@

$(LINKER): jsoo_link.$(BEST)
	mv -f $< $@


man: man/$(MINIFIER).1 man/$(COMPILER).1 man/$(LINKER).1
man/$(MINIFIER).1: $(MINIFIER)
	mkdir -p man
	./$(MINIFIER) --help=groff >$@
man/$(COMPILER).1: $(COMPILER)
	mkdir -p man
	./$(COMPILER) --help=groff >$@
man/$(LINKER).1: $(LINKER)
	mkdir -p man
	./$(LINKER) --help=groff >$@


.INTERMEDIATE: jsoo_compile.byte jsoo_compile.opt minify.byte minify.opt jsoo_link.byte jsoo_link.opt

jsoo_compile.byte: $(BYTE_COMP) $(COMPOBJS:cmx=cmo)
	$(OCAMLC) $(SAFESTRING) -o $@ $^
jsoo_compile.opt: $(NAT_COMP) $(COMPOBJS)
	$(OCAMLOPT) $(SAFESTRING) -I +compiler-libs ocamlcommon.cmxa -o $@ $^

minify.byte: $(BYTE_COMP) $(MINIOBJS:cmx=cmo)
	$(OCAMLC) $(SAFESTRING) -o $@ $^
minify.opt: $(NAT_COMP) $(MINIOBJS:cmx=cmx)
	$(OCAMLOPT) $(SAFESTRING) -I +compiler-libs ocamlcommon.cmxa -o $@ $^

jsoo_link.byte: $(BYTE_COMP) $(LINKEROBJS:cmx=cmo)
	$(OCAMLC) $(SAFESTRING) -o $@ $^
jsoo_link.opt: $(NAT_COMP) $(LINKEROBJS:cmx=cmx)
	$(OCAMLOPT) $(SAFESTRING) -I +compiler-libs ocamlcommon.cmxa -o $@ $^

%.cmx: %.ml
	$(OCAMLOPT) -I lib $(SAFESTRING) -c $<

%.cmo: %.ml
	$(OCAMLC) -I lib $(SAFESTRING) -c $<

%.cmi: %.mli
	$(OCAMLC) -I lib $(SAFESTRING) -c $<

clean:
	rm -f *.cm[aiox] *.cmxa *.cmxs *.o *.a *.conflicts
	rm -f lib/*.cm[aiox] lib/*.cmxa lib/*.cmxs lib/*.o lib/*.a
	rm -f *.opt *.byte
	rm -f $(MINIFIER) $(COMPILER) $(LINKER)
	rm -f compiler_version.ml
	rm -f annot_lexer.ml annot_parser.ml annot_parser.mli
	rm -f js_lexer.ml js_parser.ml js_parser.mli
	rm -f util.ml
	rm -Rf man
	$(MAKE) -C lib clean
	$(MAKE) -C findlib_support clean

.PHONY: depend
depend: $(COMPOBJS:cmx=ml) $(MINIOBJS:cmx=ml) $(LINKEROBJS:cmx=ml) compileArg.mli minifyArg.mli
	echo $^ | xargs -n1 | sort -u | xargs ocamldep > .depend

include .depend
