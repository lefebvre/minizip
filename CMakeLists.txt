#***************************************************************************
# Copyright:  Matthias Schmieder,
# E-Mail:     schmieder.matthias@gmail.com
# Year:       2016
#***************************************************************************

# declare as a TriBITS project for reuse in downstream projects

# Set a consistent MACOSX_RPATH default across all CMake versions.
# When CMake 2.8.12 is required, change this default to 1.
# When CMake 3.0.0 is required, remove this block (see CMP0042).
if(NOT DEFINED CMAKE_MACOSX_RPATH)
  set(CMAKE_MACOSX_RPATH 0)
endif()

TRIBITS_PACKAGE(minizip DISABLE_STRONG_WARNINGS)

set(MINIZIP_SRC ioapi.c
  ioapi_buf.c
  ioapi_mem.c
  unzip.c
  zip.c)

set(MINIZIP_PUBLIC_HEADERS crypt.h
  ioapi.h
  ioapi_buf.h
  ioapi_mem.h
  unzip.h
  zip.h)

if(WIN32)
  list(APPEND MINIZIP_SRC iowin32.c)
  list(APPEND MINIZIP_PUBLIC_HEADERS iowin32.h)
  add_definitions(-D_CRT_SECURE_NO_DEPRECATE)
endif()

# create minizip library
TRIBITS_ADD_LIBRARY(miniziplib
    SOURCES ${MINIZIP_SRC}
    NOINSTALLHEADERS ${MINIZIP_PUBLIC_HEADERS}
)


option(MINIZIP_USE_AES "enables building of aes library" ON)
if(MINIZIP_USE_AES)
  set(AES_SRC
    aes/aescrypt.c
    aes/aeskey.c
    aes/aestab.c
    aes/entropy.c
    aes/fileenc.c
    aes/hmac.c
    aes/prng.c
    aes/pwd2key.c
    aes/sha1.c)

  set(AES_PUBLIC_HEADERS
    aes/aes.h
    aes/aes_via_ace.h
    aes/aesopt.h
    aes/aestab.h
    aes/brg_endian.h
    aes/brg_types.h
    aes/entropy.h
    aes/fileenc.h
    aes/hmac.h
    aes/prng.h
    aes/pwd2key.h
    aes/sha1.h)

  TRIBITS_ADD_LIBRARY(minizip_aes
    SOURCES ${AES_SRC}
    NOINSTALLHEADERS ${AES_PUBLIC_HEADERS})

  add_definitions(-DHAVE_AES)

  install(FILES ${AES_PUBLIC_HEADERS}
    DESTINATION "include/minizip/aes")
endif()

install(FILES ${MINIZIP_PUBLIC_HEADERS}
  DESTINATION "include/minizip")

TRIBITS_ADD_EXECUTABLE(miniunz_exec NOEXEPREFIX SOURCES miniunz.c)

TRIBITS_ADD_EXECUTABLE(minizip_exec NOEXEPREFIX SOURCES minizip.c)

install(TARGETS miniunz_exec minizip_exec
        RUNTIME DESTINATION "bin")

TRIBITS_PACKAGE_POSTPROCESS()
