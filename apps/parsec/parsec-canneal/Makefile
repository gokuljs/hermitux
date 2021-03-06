# Edit this with the path to HermiTux base install folder:
HERMITUX_BASE=$(HOME)/Desktop/hermitux

HERMIT_LOCAL_INSTALL=$(HERMITUX_BASE)/hermitux-kernel/prefix
MUSL_PREFIX=$(HERMITUX_BASE)/musl/prefix
MUSL_GCC=$(MUSL_PREFIX)/bin/musl-gcc
DLSO=$(MUSL_PREFIX)/lib/libc.so

CFLAGS=-g -O1
LDFLAGS=-static -lm

SRC := $(shell ls *.cpp)
OBJ=$(SRC:.c=.o)
PROG?=prog

KERNEL=$(HERMIT_LOCAL_INSTALL)/x86_64-hermit/extra/tests/hermitux
VERBOSE?=0
# Should the binary be stripped?
STRIP?=0
# Enable the profiler when != 0, use this number as the number of samples per
# second. Remeber to add -g and remove -O3 for compile flags
PROF?=0
# Command line arguments
ARGS?=1 5 100 10.nets
# Memory to give to the VM
MEM?=2G

# Set to 1 to enable minifs
MINIFS?=0
# File containing the list of files to load from the host when using minifs
HOSTLOAD?=.minifs

# Set this to 1 to enable gdb support, see tools/hermitux-gdb. Don't forget
# to disable stripping and -O3 to be able to debug.
DEBUG?=0

# Network parameters
NETIF=
IP=
GATEWAY=

CC=g++

all: $(PROG)

$(PROG): $(SRC)
	$(CC) $(CFLAGS) $(SRC) -o $(PROG) $(LDFLAGS)
ifeq ($(STRIP),1)
	strip $(PROG)
endif

%.o: %.c
	$(CC) $(CFLAGS) -c $^ -o $@

objdump: $(PROG)
	objdump --source $(PROG) > /tmp/objdump.txt && vim /tmp/objdump.txt

objdump-kernel:
	objdump --source $(KERNEL) > /tmp/objdump.txt && vim /tmp/objdump.txt

test: $(PROG)
	HERMIT_VERBOSE=$(VERBOSE) HERMIT_ISLE=uhyve HERMIT_TUX=1 \
	HERMIT_DEBUG=$(DEBUG) \
	HERMIT_PROFILE=$(PROF) HERMIT_MINIFS=$(MINIFS) \
	HERMIT_MEM=$(MEM) HERMIT_MINIFS_HOSTLOAD=$(HOSTLOAD) \
	HERMIT_NETIF=$(NETIF) HERMIT_IP=$(IP) HERMIT_GATEWAY=$(GATEWAY) \
	$(HERMIT_LOCAL_INSTALL)/bin/proxy $(KERNEL) $(PROG) $(ARGS)

clean:
	rm -rf *.o $(PROG) hermitux-prof.txt checkpoint/

