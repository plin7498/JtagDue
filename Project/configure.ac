
# Copyright (C) 2012 R. Diez - see the JtagDue project for licensing information.

AC_INIT([arduino-due-native-usb-speed-test],[1.0.0])
AC_PREREQ([2.65])  # Tested mainly with version 2.68.

AC_NO_EXECUTABLES  # This undocumented hack prevents error message "error: C compiler cannot create executables",
                   # see the toolchain makefile for more information about why this is needed. Short version:
                   # libgloss was not adapted for our board, so the toolchain alone cannot build executables.

AM_INIT_AUTOMAKE([-Wall -Werror])
LT_INIT

# Use "make V=1" in order to see the gcc command-lines during compilation.
m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])])

# According to the autoconf documentation:
#   If your package does not like this default, then it is acceptable to insert the line
#  ': ${CXXFLAGS=""}' after AC_INIT and before AC_PROG_CXX to select an empty default instead.
CXXFLAGS="-g -Wall"
CFLAGS="-g -Wall"
CCASFLAGS="-g -Wall"

AC_PROG_CC
AC_PROG_CXX
AM_PROG_AS
AC_PROG_INSTALL

AC_CONFIG_MACRO_DIR([m4])


# ----------- Check whether debug or release build -----------

AC_MSG_CHECKING(whether to generate a debug build)
AC_ARG_ENABLE([debug],
              [AS_HELP_STRING([--enable-debug=[[yes/no]]],
                              [generate a debug build with assertions and no optimization [default=no]])],
              [case "${enableval}" in
               yes) debug_build=true ;;
               no)  debug_build=false ;;
               *) AC_MSG_ERROR([bad value ${enableval} for --enable-debug]) ;;
               esac],
              debug_build=false)

if [ test x$debug_build = xtrue ]
then
    AC_MSG_RESULT(yes)
    # I had to add "-g" again at this point in order to get line number information generated
    # in the general debug information.
    DEBUG_FLAGS="-O0 -DDEBUG -g3"
else
    AC_MSG_RESULT(no)
    # One quick test I did showed these JTAG transfer speeds: with -Os: 25 KB/s
    #                                                         with -O3: 35 KB/s
    # The firmware did get somewhat bigger with -O3 though.
    DEBUG_FLAGS="-O3 -DNDEBUG"
fi

CXXFLAGS+=" $DEBUG_FLAGS"
CFLAGS+=" $DEBUG_FLAGS"
CCASFLAGS+=" $DEBUG_FLAGS"


# ----------- Path to the Arduino environment -----------

# AC_ARG_WITH(arduino,
# [AS_HELP_STRING([--with-arduino=PATH],
# 		[specify directory for the installed Arduino package, for example "$HOME/arduino-1.5.2".])])
#
# if test "x$with_arduino" == x; then
#   AC_MSG_ERROR([Please specify the --with-arduino=PATH configuration switch])
# else
#   ARDUINO_ENV_BASEDIR="$(readlink -f "$with_arduino")"
# fi
#
#
# if ! test -d "${ARDUINO_ENV_BASEDIR}/hardware"; then
#   AC_MSG_ERROR([The Arduino package does not seem to be installed at $ARDUINO_ENV_BASEDIR])
# fi
#
# AC_SUBST(ARDUINO_ENV_BASEDIR)


# ----------- Path to the Atmel Software Framework -----------

AC_ARG_WITH(atmel-software-framework,
[AS_HELP_STRING([--with-atmel-software-framework=PATH],
		[specify directory for the installed Atmel Software Framework, for example "$HOME/asf-3.7.3".])])

if test "x$with_atmel_software_framework" == x; then
  AC_MSG_ERROR([Please specify the --with-atmel-software-framework=PATH configuration switch])
else
  ASF_BASEDIR="$(readlink -f "$with_atmel_software_framework")"
fi


if ! test -d "${ASF_BASEDIR}/sam"; then
  AC_MSG_ERROR([The Atmel Software Framework does not seem to be installed at $ASF_BASEDIR])
fi

AC_SUBST(ASF_BASEDIR)


# ----------------------------------------
# Add some extra compilation flags.

ARCH_FLAGS=" -mcpu=cortex-m3 -mthumb"  # Switch "-march=armv7-m" could also be an option.

EXTRA_C_AND_CXX_FLAGS=""

# WARNING: All flags that affect the ABI must match those specified when building the toolchain.

EXTRA_C_AND_CXX_FLAGS+=" -ffunction-sections -fdata-sections"
EXTRA_C_AND_CXX_FLAGS+=" --param max-inline-insns-single=500"

EXTRA_CPP_FLAGS=""

EXTRA_CPP_FLAGS+=" -DBOARD=ARDUINO_DUE_X"

EXTRA_CPP_FLAGS+=" -D_GNU_SOURCE"  # This select the right version of strerror_r().

# Needed for both CMSIS and the lib under "arduino-1.5.2/hardware/arduino/sam/cores".
EXTRA_CPP_FLAGS+=" -D__SAM3X8E__"

# Needed by the Atmel Software Framework.
EXTRA_CPP_FLAGS+=" -DSAM3XA_SERIES"

# Needed by the Atmel Software Framework.
EXTRA_CPP_FLAGS+=" -DASSERT_MSG_BUFSIZE=300"

# I had problems building some files in the Atmel Software Framework version 3.7.3.69
# when I defined this flag:
#   EXTRA_CPP_FLAGS+=" -DUDD_NO_SLEEP_MGR"

# This is for the 'native' USB port.
EXTRA_CPP_FLAGS+=" -DUSB_PID=0x003e -DUSB_VID=0x2341"


# Assorted extra warnings.
EXTRA_C_AND_CXX_FLAGS+=" -fdiagnostics-show-option"
EXTRA_C_AND_CXX_FLAGS+=" -Wall -Wextra"
EXTRA_C_AND_CXX_FLAGS+=" -Wshadow -Wpointer-arith -Wcast-qual"
EXTRA_C_AND_CXX_FLAGS+=" -Wunreachable-code"
EXTRA_C_AND_CXX_FLAGS+=" -Wredundant-decls"
EXTRA_C_AND_CXX_FLAGS+=" -Wwrite-strings"
EXTRA_C_AND_CXX_FLAGS+=" -Wfloat-equal"
EXTRA_C_AND_CXX_FLAGS+=" -Wformat=2"
EXTRA_C_AND_CXX_FLAGS+=" -Wunknown-pragmas"
EXTRA_C_AND_CXX_FLAGS+=" -Wattributes"
EXTRA_C_AND_CXX_FLAGS+=" -Wtrampolines"
EXTRA_C_AND_CXX_FLAGS+=" -Wnormalized=nfc"
EXTRA_C_AND_CXX_FLAGS+=" -Wpragmas"
EXTRA_C_AND_CXX_FLAGS+=" -Wlogical-op"
EXTRA_C_AND_CXX_FLAGS+=" -Wunsafe-loop-optimizations"
EXTRA_C_AND_CXX_FLAGS+=" -Wstack-protector"
EXTRA_C_AND_CXX_FLAGS+=" -Wbuiltin-macro-redefined"
EXTRA_C_AND_CXX_FLAGS+=" -Wmudflap"
EXTRA_C_AND_CXX_FLAGS+=" -Wabi"
EXTRA_C_AND_CXX_FLAGS+=" -Wcpp"
EXTRA_C_AND_CXX_FLAGS+=" -Wendif-labels"
EXTRA_C_AND_CXX_FLAGS+=" -Wdeprecated"
EXTRA_C_AND_CXX_FLAGS+=" -Wdeprecated-declarations"
EXTRA_C_AND_CXX_FLAGS+=" -Wdiv-by-zero"
EXTRA_C_AND_CXX_FLAGS+=" -Wdouble-promotion"

EXTRA_C_FLAGS=""
EXTRA_C_FLAGS+=" -Wc++-compat"
EXTRA_C_FLAGS+=" -Wstrict-prototypes"
EXTRA_C_FLAGS+=" -Wmissing-prototypes"
EXTRA_C_FLAGS+=" -Wpointer-to-int-cast"


# Turn off warnings that occur too often in the libraries.
EXTRA_C_AND_CXX_FLAGS+=" -Wno-missing-field-initializers"

EXTRA_C_AND_CXX_FLAGS+=" -Wno-psabi"  # Suppresses warning "the mangling of 'va_list' has changed in GCC 4.4".
                                      # I do not know yet if this warning should be taken seriously.


# ---  Include paths.

# libsam

EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/sam/drivers/pmc"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/sam/drivers/pio"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/sam/utils"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/sam/utils/header_files"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/sam/utils/preprocessor"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/sam/boards"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/common/utils"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/common/services/clock"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/common/boards"

# CMSIS

CMSIS_BASE_DIR="$ASF_BASEDIR/sam/utils/cmsis/sam3x"

CMSIS_SRC_TEMPLATE_DIR="$CMSIS_BASE_DIR/source/templates"
AC_SUBST(CMSIS_SRC_TEMPLATE_DIR)

EXTRA_CPP_FLAGS+=" -I$CMSIS_BASE_DIR/include"
EXTRA_CPP_FLAGS+=" -I$CMSIS_SRC_TEMPLATE_DIR"
EXTRA_CPP_FLAGS+=" -I$ASF_BASEDIR/thirdparty/CMSIS/Include"

# --- Set the final variables.

CXXFLAGS+=" $ARCH_FLAGS $EXTRA_C_AND_CXX_FLAGS"
CFLAGS+=" $ARCH_FLAGS $EXTRA_C_AND_CXX_FLAGS $EXTRA_C_FLAGS"
CCASFLAGS+=" $ARCH_FLAGS"

CPPFLAGS+=" $EXTRA_CPP_FLAGS"

# ----------------------------------------

BARE_METAL_SUPPORT_DIR="$(readlink -f "$srcdir/BareMetalSupport")"
AC_SUBST(BARE_METAL_SUPPORT_DIR)

# If you update this line, please update SUBDIRS in Makefile.am too.
AC_CONFIG_FILES([Makefile BareMetalSupport/Makefile JtagFirmware/Makefile])

AC_OUTPUT