# Copyright (C) 2007-2012 LuaDist.
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( tiff C CXX )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )

macro ( add_prefix prefix rootlist )
set ( outlist )
foreach ( root ${${rootlist}} )
list ( APPEND outlist ${prefix}${root} )
endforeach ( root )
set ( ${rootlist} ${outlist} )
endmacro ( )

# generating config.h file:
message ( "Configure: libtiff - generating tif_config.h:" )
include ( CheckFunctionExists )
include ( CheckIncludeFiles )
include ( CheckLibraryExists )
include ( CheckSymbolExists )
include ( CheckTypeSize )



if ( MACOS )
  #2DO: set ( HAVE_APPLE_OPENGL_FRAMEWORK 1 )
endif ( )

check_include_files ( assert.h HAVE_ASSERT_H )
check_include_files ( dlfcn.h HAVE_DLFCN_H )
check_include_files ( fcntl.h HAVE_FCNTL_H )
check_include_files ( inttypes.h HAVE_INTTYPES_H )
check_include_files ( io.h HAVE_IO_H)
check_include_files ( limits.h HAVE_LIMITS_H )
check_include_files ( malloc.h HAVE_MALLOC_H )
check_include_files ( memory.h HAVE_MEMORY_H )

check_include_files ( search.h HAVE_SEARCH_H)
check_include_files ( stdint.h HAVE_STDINT_H )
check_include_files ( stdlib.h HAVE_STDLIB_H )
check_include_files ( strings.h HAVE_STRINGS_H )
check_include_files ( string.h HAVE_STRING_H )

check_include_files ( time.h HAVE_TIME_H)
check_include_files ( unistd.h HAVE_UNISTD_H )
check_include_files ( windows.h HAVE_WINDOWS_H )
check_include_files ( "sys/stat.h" HAVE_SYS_STAT_H )
check_include_files ( "sys/time.h" HAVE_SYS_TIME_H )
check_include_files ( "sys/types.h" HAVE_SYS_TYPES_H )
check_include_files ( ieeefp.h HAVE_IEEEFP_H)


check_function_exists ( floor HAVE_FLOOR )
check_function_exists ( getopt HAVE_GETOPT )
check_function_exists ( lfind HAVE_LFIND )
check_function_exists ( memmove HAVE_MEMMOVE )
check_function_exists ( memset HAVE_MEMSET )
check_function_exists ( mmap HAVE_MMAP )
check_function_exists ( pow HAVE_POW )
check_function_exists ( isascii HAVE_ISASCII)

check_function_exists ( sqrt HAVE_SQRT )
check_function_exists ( strcasecmp HAVE_STRCASECMP )
check_function_exists ( strchr HAVE_STRCHR )
check_function_exists ( strrchr HAVE_STRRCHR )
check_function_exists ( strstr HAVE_STRSTR )
check_function_exists ( strtol HAVE_STRTOL )
check_function_exists ( strtoul HAVE_STRTOUL )
check_function_exists ( strtoull HAVE_STRTOULL )

check_library_exists ( c "" "" HAVE_LIBC )
check_library_exists ( m "" "" HAVE_LIBM )


if( HAVE_TIME_H AND HAVE_SYS_TIME_H )
 set(TIME_WITH_SYS_TIME 1)
endif()

# Define if you have POSIX threads libraries and header files.
#undef HAVE_PTHREAD
if(HAVE_STDINT_H)
  message("have stdint.h")
  set(TIFF_INT8_T    int8_t)
  set(TIFF_INT16_T   int16_t)
  set(TIFF_INT32_T   int32_t)
  set(TIFF_INT64_T   int64_t)
  set(TIFF_UINT8_T   uint8_t)
  set(TIFF_UINT16_T  uint16_t)
  set(TIFF_UINT32_T  uint32_t)
  set(TIFF_UINT64_T  uint64_t)
  set(TIFF_PTRDIFF_T ptrdiff_t)
  if(NOT HAVE_SSIZE_T)
    if("${CMAKE_SIZEOF_VOID_P}" EQUAL 8)
      set(TIFF_SSIZE_T   int64_t)
    elseif("${CMAKE_SIZEOF_VOID_P}" EQUAL 4)
      set(TIFF_SSIZE_T   int32_t)
    else()
      message(FATAL_ERROR "unknown ssize_t")
    endif()
  else()
    set(TIFF_SSIZE_T   ssize_t)
  endif()
  set(TIFF_INT32_FORMAT "\"%d\"")
  set(TIFF_UINT32_FORMAT "\"%u\"")
  set(TIFF_INT64_FORMAT "\"%ld\"")
  set(TIFF_UINT64_FORMAT "\"%lu\"")
  set(TIFF_PTRDIFF_FORMAT "\"%ld\"")
  set(TIFF_SSIZE_FORMAT "\"%ld\"")
else()
  set(TIFF_INT8_T    "signed __int8")
  set(TIFF_INT16_T   "signed __int16")
  set(TIFF_INT32_T   "signed __int32")
  set(TIFF_INT64_T   "signed __int64")
  set(TIFF_UINT8_T   "unsigned __int8")
  set(TIFF_UINT16_T  "unsigned __int16")
  set(TIFF_UINT32_T  "unsigned __int32")
  set(TIFF_UINT64_T  "unsigned __int64")
  set(TIFF_PTRDIFF_T ptrdiff_t)
  if(NOT HAVE_SSIZE_T)
    if("${CMAKE_SIZEOF_VOID_P}" EQUAL 8)
      set(TIFF_SSIZE_T   "signed __int64")
    elseif("${CMAKE_SIZEOF_VOID_P}" EQUAL 4)
      set(TIFF_SSIZE_T   "signed __int32")
    else()
      message(FATAL_ERROR "unknown ssize_t")
    endif()
  else()
    set(TIFF_SSIZE_T   ssize_t)
  endif()
  set(TIFF_INT32_FORMAT "\"%d\"")
  set(TIFF_UINT32_FORMAT "\"%u\"")
  set(TIFF_INT64_FORMAT "\"%ld\"")
  set(TIFF_UINT64_FORMAT "\"%lu\"")
  set(TIFF_PTRDIFF_FORMAT "\"%ld\"")
  set(TIFF_SSIZE_FORMAT "\"%ld\"")
endif()


# Define to 1 if your C compiler doesn't accept -c and -o together.
#undef NO_MINUS_C_MINUS_O

# Support Old JPEG compresson (read contrib/ojpeg/README first!
# Compilation fails with unpatched IJG JPEG library)
#undef OJPEG_SUPPORT



#Define to necessary symbol if this constant uses a non-standard name onyour system.
#undef PTHREAD_CREATE_JOINABLE

check_type_size ( "int" SIZEOF_INT )
check_type_size ( "long" SIZEOF_LONG )
check_type_size ( "signed int"         SIZEOF_SIGNED_INT)
check_type_size ( "signed long"        SIZEOF_SIGNED_LONG)
check_type_size ( "signed long long"   SIZEOF_SIGNED_LONG_LONG)
check_type_size ( "signed short"       SIZEOF_SIGNED_SHORT)
check_type_size ( "unsigned int"       SIZEOF_UNSIGNED_INT)
check_type_size ( "unsigned long"      SIZEOF_UNSIGNED_LONG)
check_type_size ( "unsigned long long" SIZEOF_UNSIGNED_LONG_LONG)
check_type_size ( "unsigned short"     SIZEOF_UNSIGNED_SHORT)
check_type_size ( "unsigned char*"     SIZEOF_UNSIGNED_CHAR_P)

set ( PACKAGE "tiff" )
set ( PACKAGE_BUGREPORT "tiff@lists.maptools.org" )
set ( PACKAGE_NAME "LibTIFF Software" )
set ( PACKAGE_STRING "LibTIFF Software 4.0.3" )
set ( PACKAGE_TARNAME "tiff" )
set ( PACKAGE_VERSION "4.0.3" )
set ( VERSION "4.0.3" )
# Native cpu byte order: 1 if big-endian (Motorola) or 0 if little-endian
# (Intel)
set ( HOST_BIGENDIAN 0 )

# Set the native cpu bit order (FILLORDER_LSB2MSB or FILLORDER_MSB2LSB)
set ( HOST_FILLORDER "FILLORDER_LSB2MSB" )

if ( HAVE_JPEG )
  set ( JPEG_SUPPORT 1 )
endif ( )
set ( LOGLUV_SUPPORT 1 )

set ( CCITT_SUPPORT 1 )
set ( CXX_SUPPORT 1 )
set ( PACKBITS_SUPPORT 1 )
set ( PIXARLOG_SUPPORT 1 )
set ( LZW_SUPPORT 1 )
set ( MDI_SUPPORT 1 )
set ( NEXT_SUPPORT 1 )
set ( STDC_HEADERS 1 )
set ( STRIPCHOP_DEFAULT "TIFF_STRIPCHOP" )
set ( STRIP_SIZE_DEFAULT 8192 )
set ( SUBIFD_SUPPORT 1 )
set ( THUNDER_SUPPORT 1 )
set ( TIME_WITH_SYS_TIME 1 )
set( LZMA_SUPPORT 0) # ?
set( MDI_SUPPORT 1) # ?
set( DEFAULT_EXTRASAMPLE_AS_ALPHA 1)
set( CHECK_JPEG_YCBCR_SUBSAMPLING 1)
set( DEFER_STRILE_LOAD 0)
set( HAVE_JBG_NEWLEN 0) # FIXME: jbigkit stuff
set( STRIPCHOP_DEFAULT "TIFF_STRIPCHOP")
set ( X_DISPLAY_MISSING 1 )
set ( ZIP_SUPPORT 1 )
set ( const const )
check_symbol_exists ( "struct tm" "sys/time.h" TM_IN_SYS_TIME )

# all is defined, so generate config.h
configure_file ( ${CMAKE_CURRENT_SOURCE_DIR}/libtiff/tif_config.h.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/tif_config.h )
configure_file ( ${CMAKE_CURRENT_SOURCE_DIR}/libtiff/tiffconf.h.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/tiffconf.h )
message ( "Configure: libtiff - done." )
add_definitions ( -DHAVE_CONFIG_H )

find_package ( ZLIB REQUIRED )
find_package ( JPEG )

include_directories ( ${ZLIB_INCLUDE_DIR} ${CMAKE_CURRENT_BINARY_DIR} )
if ( JPEG_FOUND )
  include_directories ( ${JPEG_INCLUDE_DIR} )
endif ( )

set ( TIFF_SRCS 
  t4.h
  tiffiop.h
  tif_aux.c
  tif_close.c
  tif_codec.c
  tif_color.c
  tif_compress.c
  tif_dir.c
  tif_dir.h
  tif_dirinfo.c
  tif_dirread.c
  tif_dirwrite.c
  tif_dumpmode.c
  tif_error.c
  tif_extension.c
  tif_fax3.c
  tif_fax3.h
  tif_fax3sm.c
  tif_flush.c
  tif_getimage.c
  tif_jbig.c
  tif_jpeg.c
  tif_luv.c
  tif_lzw.c
  tif_next.c
  tif_ojpeg.c
  tif_open.c
  tif_packbits.c
  tif_pixarlog.c
  tif_predict.c
  tif_predict.h
  tif_print.c
  tif_read.c
  tif_strip.c
  tif_swab.c
  tif_thunder.c
  tif_tile.c
  tif_version.c
  tif_warning.c
  tif_write.c
  tif_zip.c
  tif_jpeg_12.c
  tif_lzma.c
  uvcode.h )
if ( UNIX )
  list ( APPEND TIFF_SRCS tif_unix.c )
elseif ( WIN32 )
list ( APPEND TIFF_SRCS tif_win32.c )
endif ( )
add_prefix ( libtiff/ TIFF_SRCS )

if ( NOT WIN32 )
  find_library ( M_LIBRARY NAMES m PATHS /usr/lib /usr/local/lib )
  if ( NOT M_LIBRARY )
    message ( STATUS "math library 'libm' not found - floating point support disabled" )
  endif ( )
else ( )
  set ( M_LIBRARY )
endif ( )

if ( WIN32 )
  add_definitions ( -DUSE_WIN32_FILEIO )
endif ( )

add_library ( tiff ${TIFF_SRCS} libtiff/libtiff.def )
target_link_libraries ( tiff ${ZLIB_LIBRARY} ${M_LIBRARY} )
if ( JPEG_FOUND )
  target_link_libraries ( tiff ${JPEG_LIBRARY} )
endif ( )

#add_library ( tiffxx libtiff/tif_stream.cxx )
#target_link_libraries ( tiffxx tiff )

install_library ( tiff)
install_header ( libtiff/tiff.h ${CMAKE_CURRENT_BINARY_DIR}/tiffconf.h libtiff/tiffio.h 
  libtiff/tiffvers.h ${CMAKE_CURRENT_BINARY_DIR}/tif_config.h )
install_data ( COPYRIGHT ChangeLog README RELEASE-DATE TODO )
install_doc ( html/ )
