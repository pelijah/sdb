include ../config.mk

CFLAGS+=-g
OBJ=cdb.o buffer.o cdb_make.o ls.o sdbht.o ht_uu.o sdb.o num.o base64.o match.o
OBJ+=json.o ns.o lock.o util.o disk.o query.o array.o fmt.o journal.o
OBJ+=dict.o ht_pp.o ht_up.o
SOBJ=$(subst .o,.o.o,${OBJ})
WITHPIC?=1
BIN=sdb${EXT_EXE}

.PHONY: all static shared clean

all: ${BIN} sdb_version.h
	${MAKE} static
ifeq ($(WITHPIC),1)
	${MAKE} shared
endif

bin: $(BIN)

json.o: json/api.c json/js0n.c json/path.c json/rangstr.c json/indent.c

install:
	$(MAKE) -C .. install

sdb_version.h:
	cd .. ; ${MAKE} src/sdb_version.h

shared: sdb_version.h
	${MAKE} libsdb.${SOVER}

static: sdb_version.h
	${MAKE} libsdb.a

libsdb.a: ${OBJ}
ifneq ($(SILENT),)
	@echo AR libsdb.a
endif
	rm -f libsdb.a
	${AR} q libsdb.a ${OBJ}
	${RANLIB} libsdb.a

libsdb.${SOVER}: ${SOBJ}
ifneq ($(EXT_SO),${SOVER})
	ln -fs libsdb.${SOVER} libsdb.${EXT_SO}
endif
ifneq ($(SILENT),)
	@echo LIB libsdb.${SOVER}
endif
	${CC} ${CFLAGS} ${LDFLAGS} $(LDFLAGS_SHARED) -o $@ ${SOBJ}

${BIN}: sdb_version.h libsdb.a main.o
ifneq ($(SILENT),)
	@echo BIN ${BIN}
endif
	${CC} ${CFLAGS} ${LDFLAGS} -o ${BIN} main.o ${OBJ}

mrproper clean:
	rm -rf ${OBJ} ${SOBJ} main.o libsdb.a a.out ${BIN} sdb.dSYM
	rm -rf *.d *._d json/*.d json/*._d *.sdb *.db *.${EXT_SO} *.${EXT_SO}.*

# rules #
.c:
ifneq ($(SILENT),)
	@echo LD $<
endif
	$(CC) $(LDFLAGS) -c $(CFLAGS) -o $@ $<

S=$

%.o: %.c
ifneq ($(SILENT),)
	@echo CC $<
endif
	${CC} -c ${CPPFLAGS} ${CFLAGS} ${CFLAGS_SHARED} -o $@ $<

%.o.o: %.c
ifneq ($(SILENT),)
	@echo CC PIC $<
endif
	@mv `echo $<|sed -e 's,\.c$S,\.d,g'` $<.tmp 2>/dev/null || true
	${CC} -c ${CPPFLAGS} ${CFLAGS} ${CFLAGS_SHARED} -o $@ $<
	@mv `echo $<|sed -e 's,\.c$S,\.d,g'` `echo $<|sed -e 's,\.c$S,\._d,g'` 2>/dev/null || true
	@mv $<.tmp `echo $<|sed -e 's,\.c$S,\.d,g'` 2>/dev/null ||true

-include ${OBJ:.o=.d}
-include ${OBJ:.o=._d}
