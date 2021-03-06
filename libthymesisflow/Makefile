CC=gcc
CCFLAGS=-fPIC -Wall 
#LDFLAGS=-shared
#LIBOCXL_DIR=../libocxl/lib

# mock thymesisflow backend
MOCK ?=0

IDIR=include
ODIR=obj
BINDIR=bin
LIB_DIR=lib
TESTBIN_DIR=testbin

OUT_DIRS= $(ODIR) $(BINDIR) $(LIB_DIR) $(TESTBIN_DIR)

CCFLAGS+=-I $(IDIR)/
CCFLAGS+= -I/usr/include/


CORE_SRCS := $(wildcard src/*.c)
CORE_OBJ:= $(addprefix $(ODIR)/,$(notdir $(CORE_SRCS:.c=.o)))

TEST_SRCS := $(wildcard unittest/*.c)
TEST_BIN:= $(addprefix $(TESTBIN_DIR)/,$(notdir $(TEST_SRCS:.c=)))

CMD_SRCS := $(wildcard cmd/*.c)
CMD_BIN:= $(addprefix $(BINDIR)/,$(notdir $(CMD_SRCS:.c=)))


VPATH= src cmd unittest


ifeq ($(DEBUG),1)
	CCFLAGS+= -g -O0
else
	CCFLAGS+= -O2
endif

ifeq ($(MOCK),1)
	CCFLAGS+= -DMOCK
else
	LDFLAGS+= -L$(LIBOCXL_DIR) -locxl -lpthread -locxl
endif

ifeq ($(PREFIX),)
	PREFIX := /usr/local
endif

all: make_dirs libthymesisflow cmds

make_dirs: 
	$(info making output directories: $(OUT_DIRS))
	mkdir -p $(OUT_DIRS)

$(ODIR)/%.o: %.c 
	$(CC) $(CCFLAGS) -c -o $@ $< $(LDFLAGS)

libthymesisflow: $(CORE_OBJ)
	ar rcs lib/$@.a $(CORE_OBJ)


$(TESTBIN_DIR)/%: $(ODIR)/%.o
	$(CC) $(CCFLAGS) -o $@ $^ -L$(LIB_DIR) -lthymesisflow $(LDFLAGS)

tests: $(TEST_BIN)


$(BINDIR)/%: $(ODIR)/%.o
	$(CC) $(CCFLAGS) -o $@ $^ -L$(LIB_DIR) -lthymesisflow $(LDFLAGS)

cmds: $(CMD_BIN) 


.PHONY: all clean clean_tests cmds tests docs


clean:
	rm -rf $(OUT_DIRS)
