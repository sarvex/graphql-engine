BUILDDIR   := bin
BINARY_ASSETS := $(BUILDDIR)/cli-ext-hasura-linux $(BUILDDIR)/cli-ext-hasura-macos $(BUILDDIR)/cli-ext-hasura-win.exe
BINARY_ASSET_CHECKSUMS := $(patsubst %,%.sha256,$(BINARY_ASSETS))

COMPRESS := gzip --best -k -c

deps:
	npm install

ci-deps:
	if [ ! -d "node_modules" ]; then npm ci; fi

ci-copy-assets:
	mkdir -p /build/_cli_ext_output
	cp $(BUILDDIR)/* /build/_cli_ext_output/

# Not supported: Refer https://github.com/zeit/pkg/issues/50
upx-compress: $(BUILDDIR)
	ls $(BUILDDIR)/cli-ext-hasura-* | xargs upx

.PRECIOUS: %.zip
%.zip: %.exe
	cd $(BUILDDIR) && \
	zip $(patsubst $(BUILDDIR)/%, %, $@) $(patsubst $(BUILDDIR)/%, %, $<)

.PRECIOUS: %.gz
%.gz: %
	$(COMPRESS) "$<" > "$@"

%.tar: %
	tar cf "$@" -C $(BUILDDIR) $(patsubst $(BUILDDIR)/%,%,$^)

%.sha256: %
	shasum -a 256 $< > $@

.PHONY: build
build:
	npm run build
	$(MAKE) $(BINARY_ASSET_CHECKSUMS)

.PHONY: clean
clean:
	$(RM) -r $(BUILDDIR)

