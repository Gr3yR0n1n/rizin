include ../global.mk
include ../librz/config.mk

DESTDIR:=$(DESTDIR)
BTOP=$(shell pwd)

.PHONY: all clean install install-symlink deinstall uninstall mrproper preload

BINS=rz-ax rz-asm rz-bin rz-hash rz-diff rizin rz-find rz-run rz-gg rz-agent rz-sign

ifeq ($(WANT_RZ_TEST),1)
    BINS+=rz-test
endif

LIBR2=$(call libname-version,librz.$(EXT_SO),${LIBVERSION})

all: preload
	@for BINARY in $(BINS) ; do $(MAKE) -C $${BINARY} all || exit 1; done
ifeq ($(COMPILER),ios-sdk)
	$(MAKE) ios-sign
endif

preload:
ifeq ($(WITHPIC),1)
	$(MAKE) -C preload
endif

install:
	mkdir -p "${DESTDIR}${BINDIR}"
	for BINARY in ${BINS} ; do \
		rm -f "${DESTDIR}${BINDIR}/$$BINARY" ; \
		${INSTALL_PROGRAM} "$$BINARY/$$BINARY" "${DESTDIR}${BINDIR}/$$BINARY" ; \
	done
	${INSTALL_SCRIPT} "$(BTOP)/rz-pm/rz-pm" "${DESTDIR}${BINDIR}/rz-pm"
	rm -rf "${DESTDIR}${DATADIR}/rizin/${VERSION}/rz-pm"
ifeq ($(WITHPIC),1)
	${INSTALL_LIB} "preload/librz.$(EXT_SO)" "${DESTDIR}${LIBDIR}"
	-cd "${DESTDIR}${LIBDIR}" && \
		mv librz.$(EXT_SO) $(LIBR2) && \
		ln -fs $(LIBR2) librz.$(EXT_SO)
endif

ios_sign ios-sign ios-sdk-sign:
	-for a in $(BINS) ; do xcrun --sdk iphoneos codesign -s- --entitlements rizin/rizin_ios.xml $$a ; done
	-for a in ../librz/*/*.${EXT_SO} ; do xcrun --sdk iphoneos codesign -s- --entitlements rizin/rizin_ios.xml $$a ; done
	-for a in ../librz/*/p/*.${EXT_SO} ; do xcrun --sdk iphoneos codesign -s- --entitlements rizin/rizin_ios.xml $$a ; done

symstall install-symlink:
	cd .. && \
	mkdir -p "${DESTDIR}${BINDIR}" && \
	for BINARY in ${BINS} ; do ln -fs "${BTOP}/$$BINARY/$$BINARY" "${DESTDIR}${BINDIR}/$$BINARY" ; done
	cd .. && ln -fs "${BTOP}/rz-pm/rz-pm" "${DESTDIR}${BINDIR}/rz-pm"
	cd .. && rm -rf "${DESTDIR}${DATADIR}/rizin/${VERSION}/rz-pm"
	cd .. && mkdir -p "${DESTDIR}${DATADIR}/rizin/${VERSION}/"
	-cd .. && ln -fs "${BTOP}/preload/librz.$(EXT_SO)" "${DESTDIR}${LIBDIR}/librz.$(EXT_SO)"
	-cd .. && cd "${DESTDIR}${LIBDIR}" && \
		mv librz.$(EXT_SO) $(LIBR2) && \
		ln -fs $(LIBR2) librz.$(EXT_SO)

deinstall uninstall:
	-cd .. ; \
	for BINARY in ${BINS} ; do rm -f "${DESTDIR}${BINDIR}/$$BINARY" ; done ; \
	rm -f "${DESTDIR}${BINDIR}/rz-pm" ; \

clean:
	@for DIR in preload ${BINS} ; do ${MAKE} -C $$DIR clean; done

mrproper:
	@for DIR in preload ${BINS} ; do ${MAKE} -C $$DIR mrproper; done

