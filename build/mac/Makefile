# Make file for Clang compiler on macOS.

# to test on Linux use "make  linux=1"

# list of source files for astyle
SRC = astyle_main.cpp \
      ASBeautifier.cpp \
      ASFormatter.cpp \
      ASEnhancer.cpp \
      ASLocalizer.cpp \
      ASResource.cpp

# list of source files for libraries without ASLocalizer
SRCx = astyle_main.cpp \
       ASBeautifier.cpp \
       ASFormatter.cpp \
       ASEnhancer.cpp \
       ASResource.cpp

# source directories
vpath %.cpp ../../src
vpath %.h   ../../src

# NOTE for java compiles the environment variable $JAVA_HOME must be set
# example: export JAVA_HOME=/usr/lib/jvm/java-6-sun-1.6.0.00
ifndef JAVA_HOME
    JAVA_HOME = /usr/lib/jvm/default-java
endif

# set prefix if not defined on the command line
ifndef prefix
    prefix=/usr/local
endif
SYSCONF_PATH=$(prefix)/share/doc/astyle

# define macros
dylib = dylib
dynamiclib = -dynamiclib
bindir = bin
objdir = obj
ipath=$(prefix)/bin
CBASEFLAGS = -W -Wall -std=c++17 -stdlib=libc++ -fno-rtti -fno-exceptions  \
-mmacosx-version-min=10.9
LDBASEFLAGS =
JAVAINCS  = -I/Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home/include \
-I/Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home/include/darwin \
-I/System/Library/Frameworks/JavaVM.framework/Headers
CXX = clang++
INSTALL=install -o $(USER) -g wheel
# INSTALL=install -o 0 -g 0

# for testing on linux
ifdef linux
    dylib = so
    dynamiclib = -shared
    CXX = g++
    JAVAINCS   = -I$(JAVA_HOME)/include -I$(JAVA_HOME)/include/linux
endif

##################################################

# define compile options for each build
ifdef CFLAGS
    CFLAGSr   = -DNDEBUG $(CBASEFLAGS) $(CFLAGS)
    CFLAGSd   = -g $(CBASEFLAGS) $(CFLAGS)
else
    CFLAGSr   = -DNDEBUG -O3 $(CBASEFLAGS)
    CFLAGSd   = -g $(CBASEFLAGS)
endif
CFLAGSs   = -DASTYLE_LIB -fPIC $(CFLAGSr)
CFLAGSsd  = -DASTYLE_LIB -fPIC $(CFLAGSd)
CFLAGSa   = -DASTYLE_LIB $(CFLAGSr)
CFLAGSad  = -DASTYLE_LIB $(CFLAGSd)
CFLAGSsj  = -DASTYLE_JNI -fPIC $(CFLAGSr) $(JAVAINCS)
CFLAGSsjd = -DASTYLE_JNI -fPIC $(CFLAGSd) $(JAVAINCS)

# define link options
ifdef LDFLAGS
    LDFLAGSr = $(LDBASEFLAGS) $(LDFLAGS)
    LDFLAGSd = $(LDBASEFLAGS) $(LDFLAGS)
else
    LDFLAGSr = $(LDBASEFLAGS)
    LDFLAGSd = $(LDBASEFLAGS)
endif

# object files are built from the source list $(SRC)
# a suffix is added for each build
OBJ   = $(patsubst %.cpp,$(objdir)/%.o,$(SRC))
OBJd  = $(patsubst %.cpp,$(objdir)/%_d.o,$(SRC))
OBJs  = $(patsubst %.cpp,$(objdir)/%_s.o,$(SRCx))
OBJsd = $(patsubst %.cpp,$(objdir)/%_sd.o,$(SRCx))
OBJa  = $(patsubst %.cpp,$(objdir)/%_a.o,$(SRCx))
OBJad = $(patsubst %.cpp,$(objdir)/%_ad.o,$(SRCx))
OBJsj  = $(patsubst %.cpp,$(objdir)/%_sj.o,$(SRCx))
OBJsjd = $(patsubst %.cpp,$(objdir)/%_sjd.o,$(SRCx))

# define object file rule (with the suffix) for each build

# OBJ
$(objdir)/%.o:  %.cpp  astyle.h  astyle_main.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSr) -c $< -o $@

# OBJd
$(objdir)/%_d.o:  %.cpp  astyle.h  astyle_main.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSd) -c $< -o $@

# OBJs
$(objdir)/%_s.o:  %.cpp  astyle.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSs) -c $< -o $@

# OBJsd
$(objdir)/%_sd.o:  %.cpp  astyle.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSsd) -c $< -o $@

# OBJa
$(objdir)/%_a.o:  %.cpp  astyle.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSa) -c $< -o $@

# OBJad
$(objdir)/%_ad.o:  %.cpp  astyle.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSad) -c $< -o $@

# OBJsj
$(objdir)/%_sj.o:  %.cpp  astyle.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSsj) -c $< -o $@

# OBJsjd
$(objdir)/%_sjd.o:  %.cpp  astyle.h
	@ mkdir -p $(objdir)
	$(CXX) $(CFLAGSsjd) -c $< -o $@

##################################################
# define build dependencies for each command

release:  astyle
astyle:  $(OBJ)
	@ mkdir -p $(bindir)
	$(CXX) $(LDFLAGSr) -dead_strip -o $(bindir)/AStyle $^
	strip $(bindir)/AStyle
	@ echo

debug:  astyled
astyled:  $(OBJd)
	@ mkdir -p $(bindir)
	$(CXX) $(LDFLAGSd) -o $(bindir)/AStyled $^
	@ echo

shared:  libastyle.$(dylib)
libastyle.$(dylib):  $(OBJs)
	@ mkdir -p $(bindir)
	$(CXX) $(dynamiclib) $(LDFLAGSr) -o $(bindir)/libastyle32.$(dylib) $^
	@ echo

shareddebug:  libastyled.$(dylib)
libastyled.$(dylib):  $(OBJsd)
	@ mkdir -p $(bindir)
	$(CXX) $(dynamiclib) $(LDFLAGSd) -o $(bindir)/libastyle32d.$(dylib) $^
	@ echo

static:  libastyle.a
libastyle.a:  $(OBJa)
	@ mkdir -p $(bindir)
	ar crs $(bindir)/$@ $^
	@ echo

staticdebug:  libastyled.a
libastyled.a:  $(OBJad)
	@ mkdir -p $(bindir)
	ar crs $(bindir)/$@ $^
	@ echo

java:  libastylej.$(dylib)
libastylej.$(dylib):  $(OBJsj)
	@ mkdir -p $(bindir)
	$(CXX) $(dynamiclib) $(LDFLAGSr) -o $(bindir)/libastyle32j.$(dylib) $^
	@ echo

javadebug:  libastylejd.$(dylib)
libastylejd.$(dylib):  $(OBJsjd)
	@ mkdir -p $(bindir)
	$(CXX) $(dynamiclib) $(LDFLAGSd) -o $(bindir)/libastyle32jd.$(dylib) $^
	@ echo

all:  release debug shared shareddebug static staticdebug

javaall:  java javadebug

clean:
	rm -f $(objdir)/*.o $(bindir)/*astyle*

cleanobj:
	rm -f $(objdir)/*.o

install:
	$(INSTALL) -m 755 -d $(ipath)
	@$(INSTALL) -m 755 $(bindir)/astyle  $(ipath)

	@if [ -d $(SYSCONF_PATH)/html ]; then \
		rm -rf  $(SYSCONF_PATH)/html; \
	fi

	$(INSTALL) -m 755 -d $(SYSCONF_PATH)
	@mkdir -p $(SYSCONF_PATH)/html;
	@for files in astyle.html \
                  install.html \
                  news.html \
                  notes.html \
                  styles.css; \
	do \
		$(INSTALL)  -m 644  "../../doc/$$files"  $(SYSCONF_PATH)/html; \
	done

uninstall:
	rm -f $(ipath)/astyle
	rm -rf $(SYSCONF_PATH)
