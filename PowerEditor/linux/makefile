# Linux host, Windows target. Reuse the source lists, flags and packaging rules
# maintained in ../gcc/makefile. All generated files stay in this directory.

LINUX_DIRECTORY := $(abspath $(dir $(lastword $(MAKEFILE_LIST))))
.DEFAULT_GOAL := all

ifneq ($(CURDIR),$(LINUX_DIRECTORY))
.PHONY: all binary data clean sciclean lexclean fullclean
all binary data clean sciclean lexclean fullclean:
	+$(MAKE) -C $(LINUX_DIRECTORY) -f makefile $@
else

CROSS_COMPILE ?= x86_64-w64-mingw32-
PYTHON ?= python3

include $(LINUX_DIRECTORY)/../gcc/makefile

# The upstream makefiles must use the Windows tools for their static libraries.
# GCC's win32 thread variant in Ubuntu 26.04 lacks the condition-variable
# support used by the C++ standard library. Select its POSIX variant explicitly.
CXX := $(CROSS_COMPILE)g++-posix
LD := $(CXX)
export CXX
export AR := $(CROSS_COMPILE)ar
export RANLIB := $(CROSS_COMPILE)ranlib
export WINDRES := $(CROSS_COMPILE)windres

SCINTILLA_TARGET_PATH := $(abspath $(SCINTILLA_TARGET))
SCINTILLA_BUILD_PATH := $(abspath $(SCINTILLA_BUILD_DIRECTORY))
LEXILLA_TARGET_PATH := $(abspath $(LEXILLA_TARGET))
LEXILLA_BUILD_PATH := $(abspath $(LEXILLA_BUILD_DIRECTORY))

GENERATED_DIRECTORY := $(BUILD_DIRECTORY)/generated
GENERATED_HEADER := $(GENERATED_DIRECTORY)/NppLibsVersion.h
CPP_PATH := $(GENERATED_DIRECTORY) $(LINUX_DIRECTORY)/compat $(CPP_PATH)
PREBUILD_EVENT_CMD := :

$(GENERATED_HEADER): $(LINUX_DIRECTORY)/prepare.py \
                    $(SCINTILLA_DIRECTORY)/win32/ScintRes.rc \
                    $(LEXILLA_DIRECTORY)/src/LexillaVersion.rc \
                    ../../boostregex/boost/version.hpp
	$(AT)$(PYTHON) $(LINUX_DIRECTORY)/prepare.py $(GENERATED_DIRECTORY)

# Also prepare generated headers for a direct `make binary`, with parallel make.
.PHONY: pre-build-event
pre-build-event: $(GENERATED_HEADER)
$(CXX_TARGETS) $(RC_TARGETS): | $(GENERATED_HEADER)
$(BUILD_DIRECTORY)/WinControls/AboutDlg/AboutDlg.o: $(GENERATED_HEADER)

# Always let the library makefiles check their dependencies, including when
# this makefile was entered through the forwarding rule above.
$(SCINTILLA_TARGET) $(LEXILLA_TARGET): .force

endif
