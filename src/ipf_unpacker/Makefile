 #
 # IPFUnpacker is a free tool for extract and decrypt ipf files
 #
 # Spl3en <spl3en.contact@gmail.com> 2015 ~ 2016
 # Lara Maia <dev@lara.click> 2017
 #
 # IPFUnpacker is free software; you can redistribute it and/or
 # modify it under the terms of the GNU General Public License
 # as published by the Free Software Foundation; either version 2
 # of the License, or (at your option) any later version.
 #
 # IPFUnpacker is distributed in the hope that it will be useful,
 # but WITHOUT ANY WARRANTY; without even the implied warranty of
 # MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 # GNU General Public License for more details.
 #
 # You should have received a copy of the GNU General Public License
 # along with IPFUnpacker.  If not, see <http://www.gnu.org/licenses/>.
 #

TARGET := ipf_unpacker

SRCEXT   := c
SRCDIR   := .
INCPATH  := ..
OBJDIR   := .
BINDIR   := ../..
BIN_DEBUGDIR := $(BINDIR)/debug
COMMON_DIR := ../common
COMMON_SRC := dbg zlib crc32 fs md5

INCLUDES := -I. \
			-I$(INCPATH) \
			-I$(COMMON_DIR)

# C compiler flag
CFLAGS := -D_GNU_SOURCE -D__DBG_ACTIVATED__ -Wall $(INCLUDES) -c -Wno-strict-aliasing

# Linker library flags
LIBDIRS  := -L$(API_DIR)/lib
LDFLAGS  := -Wl,-rpath -Wl,$(API_DIR)/lib $(LIBDIRS)
LDLIBS   := -lz

SRCS	:= $(shell find $(SRCDIR) -name '*.$(SRCEXT)')
COMMONS	:= $(shell find $(addprefix $(COMMON_DIR)/,$(COMMON_SRC)) -name '*.$(SRCEXT)')
SRCDIRS := $(shell find . -name '*.$(SRCEXT)' -exec dirname {} \; | uniq)
OBJS	:= $(patsubst %.$(SRCEXT),$(OBJDIR)/%.o,$(SRCS) $(COMMONS))

###########################################################################
# Rules to compile our files  - Do not change below this line!
###########################################################################
CFLAGS += -std=gnu99

.PHONY: all debug release clean distclean 

all: $(BINDIR)/$(TARGET)

# Build debug library
debug: CFLAGS += -g
debug: clean $(BIN_DEBUGDIR)/$(TARGET)

# strip all symbols from release verison
release: LDFLAGS += -s
release: CFLAGS += -s -O2
release: $(BINDIR)/$(TARGET)

$(BINDIR)/$(TARGET): buildrepo $(OBJS)
	@mkdir -p `dirname $@`
	@echo -e "\033[1;34mLinking '$@'\033[0m"
	@$(CC) $(LDFLAGS) -o $@ $(OBJS) $(LDLIBS)
	@echo -e "\033[1;32m ---> '$(TARGET)' sucessfully built !\033[0m"

$(BIN_DEBUGDIR)/$(TARGET): buildrepo $(OBJS)
	@mkdir -p `dirname $@`
	@echo -e "\033[1;34mLinking '$@'\033[0m"
	@$(CC) $(LDFLAGS) -o $@ $(OBJS) $(LDLIBS)
	@echo -e "\033[1;32m ---> '$(TARGET)' sucessfully built !\033[0m"

$(OBJDIR)/%.o: %.$(SRCEXT)
	@$(call make-depend,$<,$@,$(subst .o,.dep,$@))
	@echo -e "\033[1;34mCompiling '$<'"
	@$(CC) $(CFLAGS) $< -o $@

clean:
	@echo -e "\033[1;34mCleaning ..."
	@$(RM) -rf $(TARGET) $(OBJS) $(subst .o,.dep,$(OBJS))

distclean: clean
	@$(RM) -r $(BINDIR)/$(TARGET)
	@$(RM) -r $(BIN_DEBUGDIR)/$(TARGET)

buildrepo:
	@$(call make-repo)

define make-repo
   for dir in $(SRCDIRS); \
   do \
	mkdir -p $(OBJDIR)/$$dir; \
   done
endef

# usage: $(call make-depend,source-file,object-file,depend-file)
define make-depend
  $(CC) -MM	   \
		-MMD	  \
		-MF $3	\
		-MP	   \
		-MT $2	\
		$(CFLAGS) \
		$(LDFLAGS) \
		$1
endef