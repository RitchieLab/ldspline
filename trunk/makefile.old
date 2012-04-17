BINPATH=./bin
ROOTPATH=.

include $(ROOTPATH)/platform.make
include $(ROOTPATH)/color.make

ifeq ($(INC_VERSION), 1)
	INCREMENT=INC_BUILD=1
endif 

ifeq ($(DEBUG), 1)
	LIBSUFFIX=d
	RELEASEMODE=RELEASE=0
	RELEASE_DESC="Debug Mode"
	INCREMENT=
else
	LIBSUFFIX=
	RELEASEMODE=RELEASE=1
	RELEASE_DESC="Release Mode"
endif

ifeq ($(USE_DOPT), 1)
	OPTION_DOPT=USE_DOPT=1
endif

ifeq ($(ENABLE_CV), 0)
	OPTION_CV=
	CV_SUFF=
else
	OPTION_CV=ENABLE_CV=1
	CV_SUFF="_cv"
endif
GNUCC=g++

ifdef COMPAT
	CPP=$(GNUCC)$(COMPAT)
else
	CPP=$(GNUCC)
endif

ifeq ($(VERBOSE), 1)
	PROJECT_OPTIONS=$(PROJECT_OPTIONS) VERBOSE=1
endif


ifeq ($(USE_MPI), 1)
	FLAGS_MPI=-D USE_MPI
	MPI_LINK=$(MPI_LIBS)
	EXEC_PREFIX=p
	MPIPATH=parallel
	MPI_DESC="w/ MPI"
else
	MPI_LINK=
	EXEC_PREFIX=
	MPIPATH=serial
	MPI_DESC="w/o MPI"
endif

include $(ROOTPATH)/platform.make
#LIBPATH=$(ROOTPATH)/bin/lib

LIBPATH=$(ROOTPATH)/bin/lib/$(MPIPATH)/$(PLATFORM)-$(BITCOUNT)bit/$(CPP)

GCC_VERSION=$(shell $(CXX) --version | grep gcc | cut -f3 -d\ | cut -f1-2 -d. )


ifdef VAMPIRE
    MPI_LIBS= -lmpich -lpthread
    MPI_INC=/usr/local/mpich-ether/p4/absoft_gcc/include/
    CL=mpiCC
	VMP_SUFF="_vmp"
else
	MPI_LIBS=-lmpichcxx -lmpich -lpthread
    MPI_INC=
    CL=$(CPP)
	VMP_SUFF=
endif



PROJECT_OPTIONS=-s $(OPTION_DOPT) $(OPTION_CV) $(RELEASE_MODE) $(INCREMENT)

LOCAL_LIBS=utility random

DEPENDANTLIBS=$(patsubst %, $(LIBPATH)/lib%$(EXEC_PREFIX)$(LIBSUFFIX).a, $(LOCAL_LIBS))

all: ldspline-app

.PHONY: clean

utility: $(LIBPATH)/libutility$(EXEC_PREFIX)$(LIBSUFFIX).a
random: $(LIBPATH)/librandom$(LIBSUFFIX).a
ldspline: $(LIBPATH)/libldspline$(LIBSUFFIX).a
ldspline-app: $(BINPATH)/ldspline$(LIBSUFFIX)


release:
	make COMPAT=32 

debug: 
	make DEBUG=1 

clean:
	@$(MAKE) -C $(ROOTPATH)/src/utility $(RELEASEMODE) clean
	@$(MAKE) -C $(ROOTPATH)/src/ldspline $(RELEASEMODE) clean

cleantmps:
	@$(MAKE) -C $(ROOTPATH)/src/utility $(RELEASEMODE) cleantmps
	@$(MAKE) -C $(ROOTPATH)/src/ldspline $(RELEASEMODE) clean

$(BINPATH)/ldspline$(LIBSUFFIX): $(DEPENDANTLIBS) 
	@echo "Dependents: $(DEPENDANTLIBS)"
	@$(MAKE) -C $(ROOTPATH)/src/ldspline -f main.make $(RELEASEMODE) $(PROJECT_OPTIONS)


$(LIBPATH)/libldspline$(EXEC_PREFIX)$(LIBSUFFIX).a:
	$(TERM_CYAN)
	@echo "EXEC_PREFIX: $(EXEC_PREFIX)\nLIBSUFFIX: $(LIBSUFFIX)"
	@echo "$(LIBPATH)/libutility$(EXEC_PREFIX)$(LIBSUFFIX).a:"
	@echo "Building $@ $^"
	$(TERM_RESET)
	@$(make) -C $(ROOTPATH)/src/ldspline $(RELEASEMODE) $(PROJECT_OPTIONS)

$(LIBPATH)/librandom$(EXEC_PREFIX)$(LIBSUFFIX).a:
	$(TERM_CYAN)
	@echo "Building $@ $^"
	$(TERM_RESET)
	@$(MAKE) -C $(ROOTPATH)/src/random $(RELEASEMODE) $(PROJECT_OPTIONS)

$(LIBPATH)/libutility$(EXEC_PREFIX)$(LIBSUFFIX).a:
	$(TERM_CYAN)
	@echo "Building $@ $^"
	$(TERM_RESET)
	@$(MAKE) -C $(ROOTPATH)/src/utility $(RELEASEMODE) $(PROJECT_OPTIONS)

