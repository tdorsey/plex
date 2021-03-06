#  A quick and dirty Makefile to download/build and install
#
#  Usage:
#    make
#    sudo make install

include ../config.mk

# lib name, version
LIBNAME=samba
VERSION=3.5.6
SOURCE=$(LIBNAME)-$(VERSION)
# download location and format
BASE_URL=http://mirrors.xbmc.org/build-deps/darwin-libs
ARCHIVE=$(SOURCE).tar.gz
RETRIEVE_TOOL=/usr/bin/curl
RETRIEVE_TOOL_FLAGS=-Ls --create-dirs --output $(TARBALLS_LOCATION)/$(ARCHIVE)
ARCHIVE_TOOL=tar
ARCHIVE_TOOL_FLAGS=xf

# configuration settings
export LDFLAGS+=-Wl,-unexported_symbol,_MD5* -lc
CONFIGURE=./configure --prefix=$(PREFIX) --host=$(HOST) --enable-shared=yes \
  --without-cluster-support --disable-swat --without-ldap --without-pam --without-pam_smbpass --with-fhs \
  ac_cv_header_libunwind_h=no \
  ac_cv_header_execinfo_h=no \
  samba_cv_SYSCONF_SC_NPROCESSORS_ONLN=no

LIBDYLIB=$(SOURCE)/source3/bin/libsmbclient.dylib

CLEAN_FILES=$(ARCHIVE) $(SOURCE)

all: $(LIBDYLIB) .installed

$(TARBALLS_LOCATION)/$(ARCHIVE):
	$(RETRIEVE_TOOL) $(RETRIEVE_TOOL_FLAGS) $(BASE_URL)/$(ARCHIVE)

$(LIBDYLIB): $(TARBALLS_LOCATION)/$(ARCHIVE)
	rm -rf $(SOURCE)
	$(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	echo $(SOURCE) > .gitignore
	cd $(SOURCE)/source3; ./autogen.sh
	cd $(SOURCE)/source3; $(CONFIGURE)
	#cp -f /Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator$(platform_sdk_version).sdk/usr/include/crt_externs.h $(PREFIX)/include/
	cd $(SOURCE)/source3; make bin/libsmbclient.dylib

.installed:
	cp -f $(SOURCE)/source3/include/libsmbclient.h $(PREFIX)/include/
	cp -f $(SOURCE)/source3/bin/libtdb.dylib.1 $(PREFIX)/lib/
	cp -f $(SOURCE)/source3/bin/libtalloc.dylib.2 $(PREFIX)/lib/
	cp -f $(SOURCE)/source3/bin/libsmbclient.dylib.0 $(PREFIX)/lib/
	cd $(PREFIX)/lib; ln -sf libsmbclient.dylib.0 libsmbclient.dylib
	install_name_tool -id $(PREFIX)/lib/libtdb.dylib.1 $(PREFIX)/lib/libtdb.dylib.1
	install_name_tool -id $(PREFIX)/lib/libtalloc.dylib.2 $(PREFIX)/lib/libtalloc.dylib.2
	install_name_tool -id $(PREFIX)/lib/libsmbclient.dylib.0 $(PREFIX)/lib/libsmbclient.dylib.0
	install_name_tool -change bin/libtdb.dylib.1 $(PREFIX)/lib/libtdb.dylib.1 \
                $(PREFIX)/lib/libsmbclient.dylib.0
	install_name_tool -change bin/libtalloc.dylib.2 $(PREFIX)/lib/libtalloc.dylib.2 \
		$(PREFIX)/lib/libsmbclient.dylib.0
	touch $@

clean:
	make -C $(SOURCE)/source3 clean
	rm -f .installed

distclean::
	rm -rf $(SOURCE) .installed
