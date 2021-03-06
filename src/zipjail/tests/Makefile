.PHONY: clean default

CFLAGS += -std=c99 -pedantic -Wall -Wextra -pipe
CFLAGS += -D_POSIX_SOURCE
CFLAGS += -D_GNU_SOURCE
CFLAGS += -ggdb -pthread

SOURCE=$(wildcard good/*.c bad/*.c)
BINARY32=$(SOURCE:%.c=%-x86.out)
BINARY64=$(SOURCE:%.c=%-x64.out)
RESULTS=$(BINARY32:%.out=%.result)
RESULTS+=$(BINARY64:%.out=%.result)
WORKDIR=/tmp/zipjail-workingdir

default: $(BINARY32) $(BINARY64) $(RESULTS) unpack

%-x86.out: %.c
	$(CC) $^ $(CFLAGS) $(LDFLAGS) -o $@ -m32

%-x64.out: %.c
	$(CC) $^ $(CFLAGS) $(LDFLAGS) -o $@

good/%.result: good/%.out
	touch /tmp/zipjail-input
	rm -rf $(WORKDIR) /tmp/zipjail-dirtydir
	mkdir -p $(WORKDIR)
	echo hello > $(WORKDIR)/a.c
	echo world > $(WORKDIR)/a.py
	../zipjail /tmp/zipjail-input /tmp/zipjail-dirtydir ./$^

bad/%.result: bad/%.out
	touch /tmp/zipjail-input
	rm -rf $(WORKDIR) /tmp/zipjail-dirtydir
	mkdir -p $(WORKDIR)
	echo hello > $(WORKDIR)/a.c
	echo world > $(WORKDIR)/a.py
	! ../zipjail /tmp/zipjail-input /tmp/zipjail-dirtydir ./$^

unpack:
	rm -rf /tmp/zipjail-simple
	../zipjail simple.zip /tmp/zipjail-simple unzip -o -d /tmp/zipjail-simple simple.zip
	rm -rf /tmp/zipjail-simple
	../zipjail simple.rar /tmp/zipjail-simple rar x -mt1 simple.rar /tmp/zipjail-simple
	rm -rf /tmp/zipjail-simple
	../zipjail simple.7z /tmp/zipjail-simple 7z x -mmt=off -o/tmp/zipjail-simple simple.7z
	rm -rf /tmp/zipjail-simple
	../zipjail pw.7z /tmp/zipjail-simple --clone=2 7z x -mmt=off -Pinfected -o/tmp/zipjail-simple pw.7z
	rm -rf /tmp/zipjail-simple
	../zipjail $(abspath simple.ace) /tmp/zipjail-simple unace x simple.ace /tmp/zipjail-simple/

clean:
	rm -f $(BINARY32) $(BINARY64)
