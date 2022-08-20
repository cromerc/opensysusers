manfiles5 = sysusers.d.5
manfiles8 = systemd-sysusers.8 systemd-sysusers.service.8
docfiles = sysusers.d.html systemd-sysusers.html

xsltargs = --nonet \
	--xinclude \
	--maxdepth 9000 \
	--stringparam man.output.quietly 1 \
	--stringparam funcsysnopsis.style ansi \
	--stringparam man.authors.section.enabled 0 \
	--stringparam man.copyright.section.enabled 0 \
	--stringparam systemd.version 238.51

all: man
ifeq ($(HAVEDOC),yes)
all: doc
endif

man:
	xsltproc $(xsltargs) custom-man.xsl systemd-sysusers.xml
	xsltproc $(xsltargs) custom-man.xsl sysusers.d.xml

doc:
	xsltproc $(xsltargs) -o systemd-sysusers.html custom-html.xsl systemd-sysusers.xml
	xsltproc $(xsltargs) -o sysusers.d.html custom-html.xsl sysusers.d.xml

# Delete files because wget can't overwrite them
download-docs:
	rm -f *.xml *.xsl
	if command -v wget; then \
		download='wget'; \
	elif command -v curl; then \
		download='curl --location --remote-name-all'; \
	elif command -v fetch; then \
		download='fetch'; \
	fi; \
	$$download \
		'https://github.com/systemd/systemd/raw/main/man/custom-html.xsl' \
		'https://github.com/systemd/systemd/raw/main/man/custom-man.xsl' \
		'https://github.com/systemd/systemd/raw/main/man/standard-options.xml' \
		'https://github.com/systemd/systemd/raw/main/man/systemd-sysusers.xml' \
		'https://github.com/systemd/systemd/raw/main/man/sysusers.d.xml'

clean:
	rm -f $(manfiles5)
	rm -f $(manfiles8)
	rm -f $(docfiles)

install-man:
	$(INSTALL) -d $(DESTDIR)$(MANDIR)/man5 $(DESTDIR)$(MANDIR)/man8
	$(INSTALL) -m $(DOCMODE) $(manfiles5) $(DESTDIR)$(MANDIR)/man5
	$(INSTALL) -m $(DOCMODE) $(manfiles8) $(DESTDIR)$(MANDIR)/man8

install-doc:
	$(INSTALL) -d $(DESTDIR)$(DOCDIR)
	$(INSTALL) -m $(DOCMODE) $(docfiles) $(DESTDIR)$(DOCDIR)

install: install-man
ifeq ($(HAVEDOC),yes)
install: install-doc
endif

uninstall-man:
	for man in ${manfiles5}; do rm -f $(DESTDIR)$(MANDIR)/man5/$$man; done
	for man in ${manfiles8}; do rm -f $(DESTDIR)$(MANDIR)/man8/$$man; done

uninstall-doc:
	for doc in ${docfiles}; do rm -f $(DESTDIR)$(DOCDIR)/$$doc; done
	rm -rf --one-file-system $(DESTDIR)$(DOCDIR)

uninstall: uninstall-man
ifeq ($(HAVEDOC),yes)
uninstall: uninstall-doc
endif

.PHONY: all man doc download-docs clean install install-man install-doc uninstall-man uninstall-doc uninstall
