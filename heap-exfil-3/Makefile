CC = cc
#CFLAGS = -ggdb -Wall -fcallgraph-info
#CFLAGS = -ggdb -Wall -fdump-tree-all-graph 
#CFLAGS = -ggdb -Wall -M
CFLAGS = -ggdb -Wall
#CFLAGS = -Xclang -ast-dump -fsyntax-only -fno-color-diagnostics -Wno-visibility
#CFLAGS = -Xclang -ast-dump=json -fsyntax-only -fno-color-diagnostics -Wno-visibility

CFLAGS+= -ffunction-sections -cheri-codeptr-relocs
DPADD=	Compartments.json
LDFLAGS = -Wl,--compartment-policy=Compartments.json

SRCS=$(wildcard *.c)
OBJS=$(subst .c,.o,$(SRCS))

.PHONY: all

all: server

%.o: %.c
	$(CC) ${CFLAGS} -c $< 

server: $(OBJS) ${DPADD}
	$(CC) ${CFLAGS} ${LDFLAGS} $(OBJS) -o $@  -l pthread

.PHONY: clean
clean:
	rm -f *.o server Compartments.json

Compartments.json: dec.json
	jq '.dsos | .[] | .[]' $< | \
	jq -s '[ (.[0] | keys[]) as $$k | reduce .[] as $$item (null; .[$$k] += $$item[$$k]) ] | add' | \
	jq 'delpaths([.compartments | to_entries | .[] | select(.key | startswith("resources/sift/")) | .key | ["compartments", . ] ])' > $@
