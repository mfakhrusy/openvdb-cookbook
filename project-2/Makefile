# Program Name		:	Grid Generation
# Maintainer		:	Muhamad Fakhrusy
# 
#
# ========================================================== #
#


DESTDIR := .
PROGRAM := grid_generate

CXX=g++-5

# INCLUDES
INCL_DIR := /usr/include
BOOST_INCL_DIR := $(INCL_DIR)
EXR_INCL_DIR := $(INCL_DIR)
ILMBASE_INCL_DIR := $(EXR_INCL_DIR)/OpenEXR
TBB_INCL_DIR := $(INCL_DIR)
INCLDIRS := -I . -I .. -isystem $(BOOST_INCL_DIR) -isystem $(ILMBASE_INCL_DIR) -isystem $(TBB_INCL_DIR)

OPTIMIZE := -O3 -DNDEBUG
CXXFLAGS += -std=c++11
CXXFLAGS += -pthread $(OPTIMIZE) $(INCLDIRS)

#LIBS_DIRECTORIES
LIB_DIR := /usr/lib
BOOST_LIB_DIR := $(LIB_DIR)
ILMBASE_LIB_DIR := $(LIB_DIR)
TBB_LIB_DIR := $(LIB_DIR)
CONCURRENT_MALLOC_LIB_DIR := $(LIB_DIR)

#LIBS
BOOST_LIB := -lboost_iostreams -lboost_system
HALF_LIB := -lHalf
TBB_LIB := -ltbb
CONCURRENT_MALLOC_LIB := -ljemalloc

LIBS_RPATH := \
	-ldl -lm -lz \
	-Wl,-rpath,$(ILMBASE_LIB_DIR) -L$(ILMBASE_LIB_DIR) $(HALF_LIB)\
	-Wl,-rpath,$(TBB_LIB_DIR) -L$(TBB_LIB_DIR) $(TBB_LIB) \
	-Wl,-rpath,$(BOOST_LIB_DIR) -L$(BOOST_LIB_DIR) $(BOOST_LIB) \

LIBS_RPATH += -lrt
LIBS_RPATH += -Wl,-rpath,$(CONCURRENT_MALLOC_LIB_DIR) -L$(CONCURRENT_MALLOC_LIB_DIR)

LIBOPENVDB_NAME=libopenvdb
LIB_MAJOR_VERSION=$(shell grep 'define OPENVDB_LIBRARY_MAJOR_VERSION_NUMBER ' \
    version.h | sed 's/[^0-9]*//g')
LIB_MINOR_VERSION=$(shell grep 'define OPENVDB_LIBRARY_MINOR_VERSION_NUMBER ' \
    version.h | sed 's/[^0-9]*//g')
LIB_PATCH_VERSION=$(shell grep 'define OPENVDB_LIBRARY_PATCH_VERSION_NUMBER ' \
    version.h | sed 's/[^0-9]*//g')
LIB_VERSION=$(LIB_MAJOR_VERSION).$(LIB_MINOR_VERSION).$(LIB_PATCH_VERSION)

LIBOPENVDB_DIR := /usr/local/lib
LIBOPENVDB_SHARED := $(LIBOPENVDB_DIR)/$(LIBOPENVDB_NAME).so.$(LIB_VERSION)
LIBOPENVDB_RPATH := -Wl,-rpath,$(LIBOPENVDB_DIR)

SO_VERSION=$(LIB_MAJOR_VERSION).$(LIB_MINOR_VERSION)
LIBOPENVDB_SONAME := $(LIBOPENVDB_NAME).so.$(SO_VERSION)
LIBOPENVDB_SONAME_FLAGS := -Wl,-soname,$(LIBOPENVDB_SONAME)

LIBOPENVDB := $(LIBOPENVDB_SHARED)

all: $(LIBOPENVDB) main.cpp
	@echo "Building $@ because of $(list_deps)"
	$(CXX) $(CXXFLAGS) -o $(PROGRAM) main.cpp -I . \
	    $(LIBOPENVDB_RPATH) -L$(CURDIR) $(LIBOPENVDB) \
	    $(LIBS_RPATH) $(CONCURRENT_MALLOC_LIB)

$(LIBOPENVDB_SHARED): $(OBJ_NAMES)
	@echo "Building $@ because of $(list_deps)"
	$(CXX) $(CXXFLAGS) -shared -o $@ $^ $(LIBS_RPATH) $(LIBOPENVDB_SONAME_FLAGS)


$(OBJ_NAMES): %.o: %.cc
	@echo "Building $@ because of $(call list_deps)"
	$(CXX) -c -DOPENVDB_PRIVATE $(CXXFLAGS) -fPIC -o $@ $<

# Get the list of dependencies that are newer than the current target,
# but limit the list to at most three entries.
list_deps = $(if $(wordlist 4,5,$(?F)),$(firstword $(?F)) and others,$(wordlist 1,3,$(?F)))

clean:
	$(RM) $(PROGRAM)
