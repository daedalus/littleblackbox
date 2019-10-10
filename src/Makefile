CC=gcc
CFLAGS=-static -Wall -g -O2
BUILD_LIBS=y
LDFLAGS=-lsqlite3 -lssl -lcrypto -lpcap -lcurl -lpthread -lrt -ldl
TARGET=littleblackbox
MANPAGE=$(TARGET).1.gz
DB=lbb.db
prefix=/usr/local
exec_prefix=${prefix}
datarootdir=${prefix}/share

$(TARGET): littleblackbox.o
	$(CC) $(CFLAGS) main.c -o $(TARGET) *.o -I./openssl/include -I./libpcap/include -I./sqlite/include -I./libcurl/include -L./openssl/lib -L./libpcap/lib -L./sqlite/lib -L./libcurl/lib $(LDFLAGS)
	cp $(TARGET) ../bin/$(TARGET)

littleblackbox.o: sqlite common.o sql.o certs.o network.o dbupdate.o
	$(CC) $(CFLAGS) -I./openssl/include -I./libpcap/include -I./sqlite/include -I./libcurl/include -c littleblackbox.c

common.o: openssl
	$(CC) $(CFLAGS) -I./openssl/include -I./libpcap/include -I./sqlite/include -I./libcurl/include -c common.c

network.o: openssl libpcap
	$(CC) $(CFLAGS) -I./openssl/include -I./libpcap/include -I./sqlite/include -I./libcurl/include -c network.c

certs.o: openssl
	$(CC) $(CFLAGS) -I./openssl/include -I./libpcap/include -I./sqlite/include -I./libcurl/include -c certs.c

sql.o: sqlite
	$(CC) $(CFLAGS) -I./openssl/include -I./libpcap/include -I./sqlite/include -I./libcurl/include -c sql.c

dbupdate.o: libcurl
	$(CC) $(CFLAGS) -I./openssl/include -I./libpcap/include -I./sqlite/include -I./libcurl/include -c dbupdate.c

openssl:
ifeq ($(BUILD_LIBS), y)
	(mkdir openssl && tar -zxvf openssl*.tar.gz -C openssl) 
	(cd openssl/openssl* && ./config --prefix=$(shell pwd)/openssl && make && make install)
endif

libpcap:
ifeq ($(BUILD_LIBS), y)
	(mkdir libpcap && tar -zxvf libpcap*.tar.gz -C libpcap)
	(cd libpcap/libpcap* && ./configure --prefix=$(shell pwd)/libpcap && make && make install)
endif

sqlite:
ifeq ($(BUILD_LIBS), y)
	(mkdir sqlite && tar -zxvf sqlite*.tar.gz -C sqlite)
	(cd sqlite/sqlite* && ./configure --prefix=$(shell pwd)/sqlite && make && make install)
endif

libcurl:
ifeq ($(BUILD_LIBS), y)
	(mkdir libcurl && tar -zxvf curl*.tar.gz -C libcurl)
	(cd libcurl/curl* && ./configure --prefix=$(shell pwd)/libcurl --disable-debug --disable-curldebug --disable-ares --disable-ftp --disable-ldap --disable-ldaps --disable-rtsp --disable-proxy --disable-dict --disable-telnet --disable-tftp --disable-pop3 --disable-imap --disable-smtp --disable-gopher --disable-manual --disable-sspi --disable-crypto-auth --disable-cookies --disable-hidden-symbols --disable-soname-bump --without-ssl --without-zlib --without-gnutls --without-polarssl --without-nss --without-libssh2 --without-librtmp --without-libidn && make && make install)
endif

install: docinstall
	cp ../bin/$(DB) /etc/$(DB)
	cp ../bin/$(TARGET) ${exec_prefix}/bin/$(TARGET)
	
docinstall:
	cp ../docs/$(MANPAGE) ${datarootdir}/man/man1/$(MANPAGE)

clean:
	rm -f $(TARGET) *.o

distclean: clean
	rm -rf openssl libpcap sqlite libcurl
	rm -rf *.cache config.*
	rm -f ${datarootdir}/man/man1/$(MANPAGE) ${exec_prefix}/bin/$(TARGET) /etc/$(DB)
