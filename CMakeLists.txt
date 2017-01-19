cmake_minimum_required(VERSION 3.2)

SET(LIBXML_MAJOR_VERSION 2)
SET(LIBXML_MINOR_VERSION 9)
SET(LIBXML_MICRO_VERSION 4)
SET(LIBXML_MICRO_VERSION_SUFFIX )
SET(VERSION ${LIBXML_MAJOR_VERSION}.${LIBXML_MINOR_VERSION}.${LIBXML_MICRO_VERSION}${LIBXML_MICRO_VERSION_SUFFIX})
MATH(EXPR LIBXML_VERSION_NUMBER "${LIBXML_MAJOR_VERSION}*10000+${LIBXML_MINOR_VERSION}*100+${LIBXML_MICRO_VERSION}")

project(libxml2 VERSION ${VERSION} LANGUAGES C)

option(BUILD_SHARED_LIBS "Build as shared lib instead of static" ON)
option(WITH_TRIO "Enable TRIO string manipulator" ON)
option(WITH_THREADS:STRING "Enable thread safety [no|ctls|native|posix" "native")
option(WITH_FTP "Enable FTP client" OFF)
option(WITH_HTTP "Enable HTTP client" OFF)
option(WITH_HTML "Enable HTML processor" ON)
option(WITH_C14N "Enable C14N support" ON)
option(WITH_CATALOG "Enable catalog support" ON)
option(WITH_DOCB "Enable DocBook support" ON)
option(WITH_XPATH "Enable XPath support" ON)
option(WITH_XPTR "Enable XPointer support" ON)
option(WITH_XINCLUDE "Enable XInclude support" ON)
option(WITH_ICONV "Enable iconv support" ON)
option(WITH_ICU "Enable icu support" OFF)
option(WITH_ISO8859X "Enable ISO8859X support" OFF)
option(WITH_ZLIB "Enable zlib support" OFF)
option(WITH_LZMA "Enable lzma support" OFF)
option(WITH_DEBUG "Enable XML debugging module" ON)
option(WITH_MEM_DEBUG "Enable memory debugger" OFF)
option(WITH_RUN_DEBUG "Enable memory debugger" OFF)
option(WITH_SCHEMAS "Enable XML schema support" ON)
option(WITH_SCHEMATRON "Enable schematron support" ON)
option(WITH_REGEXPS "Enable regular expressions" ON)
option(WITH_MODULES "Enable module support" ON)
option(WITH_TREE "Enable tree api" ON)
option(WITH_READER "Enable xmlReader api" ON)
option(WITH_WRITER "Enable xmlWriter api" ON)
option(WITH_WALKER "Enable xmlDocWalker api" ON)
option(WITH_PATTERN "Enable xmlPattern api" ON)
option(WITH_PUSH "Enable push api" ON)
option(WITH_VALID "Enable DTD validation support" ON)
option(WITH_SAX1 "Enable SAX1 api" ON)
option(WITH_LEGACY "Enable deprecated api's" OFF)
option(WITH_OUTPUT "Enable serialization support" ON)
option(WITH_PYTHON "Enable Python bindings" OFF)


include(CheckIncludeFile)
include(CheckFunctionExists)

CHECK_INCLUDE_FILE(ctype.h HAVE_CTYPE_H)
CHECK_INCLUDE_FILE(dirent.h HAVE_DIRENT_H)
CHECK_INCLUDE_FILE(dlfcn.h HAVEN_DLFCN_H)
CHECK_INCLUDE_FILE(dl.h HAVE_DL_H)
CHECK_INCLUDE_FILE(errno.h HAVE_ERRNO_H)
CHECK_INCLUDE_FILE(fcntl.h HAVE_FCNTL_H)
CHECK_INCLUDE_FILE(ieeefp.h HAVE_IEEEP_H)
CHECK_INCLUDE_FILE(inttypes.h HAVE_INTTYPES_H)
CHECK_INCLUDE_FILE(limits.h HAVE_LIMITS_H)
CHECK_INCLUDE_FILE(malloc.h HAVE_MALLOC_H)
CHECK_INCLUDE_FILE(math.h HAVE_MATH_H)
CHECK_INCLUDE_FILE(memory.h HAVE_MEMORY_H)


if(WIN32)
  set(CMAKE_DEBUG_POSTFIX "d")
endif()

configure_file(
  config.h.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/config.h
)
add_library(${PROJECT_NAME} 
  xzlib.h
  buf.h
  config.h
  elfgcchack.h
  enc.h
  libxml.h
  save.h
  timsort.h
  trio.h
  triodef.h
  trionan.h
  triop.h
  triostr.h
  xzlib.c
  buf.c
  c14n.c
  catalog.c
  chvalid.c
  debugXML.c
  dict.c
  DOCBparser.c
  encoding.c
  entities.c
  error.c
  globals.c
  hash.c
  HTMLparser.c
  HTMLtree.c
  legacy.c
  list.c
  nanoftp.c
  nanohttp.c
  parser.c
  parserInternals.c
  pattern.c
  relaxng.c
  rngparser.c
  SAX.c
  SAX2.c
  schematron.c
  threads.c
  tree.c
  trio.c
  trionan.c
  triostr.c
  uri.c
  valid.c
  xinclude.c
  xlink.c
  xmlIO.c
  xmlmemory.c
  xmlmodule.c
  xmlreader.c
  xmlregexp.c
  xmlsave.c
  xmlschemas.c
  xmlschemastypes.c
  xmlstring.c
  xmlunicode.c
  xmlwriter.c
  xpath.c
  xpointer.c
)
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})
target_include_directories(
  libxml2 PRIVATE
  $<BUILD_INTERFACE:.;include;include/libxml>
  INTERFACE
  $<INSTALL_INTERFACE:include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/libxml>
)
target_compile_definitions(libxml2 
  PRIVATE 
  _CRT_SECURE_NO_WARNINGS
  _CRT_STDC_NO_DEPRECATE)

if(BUILD_SHARED_LIBS AND MSVC)
  target_link_libraries(libxml2 PRIVATE ws2_32.lib)
endif()

if(WITH_ICONV)
  if(!UNIFIED_BUILD)
    find_package(iconv 1.14 REQUIRED NO_MODULE)
  endif()
  target_link_libraries(libxml2 PRIVATE iconv::iconv)
endif()

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-version.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)

install(TARGETS libxml2 EXPORT libxml2
  RUNTIME DESTINATION bin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib)


install(DIRECTORY
  ${CMAKE_CURRENT_SOURCE_DIR}/include/libxml
  DESTINATION include
  FILES_MATCHING PATTERN "*.h")

install(EXPORT libxml2
  FILE
    libxml2.cmake
  NAMESPACE
    libxml2::
  DESTINATION
    lib/cmake/libxml2
)
install(
  FILES
    cmake/libxml2-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-version.cmake
  DESTINATION
    lib/cmake/libxml2
)

export(TARGETS libxml2
  FILE libxml2.cmake
  NAMESPACE libxml2::
)

if(MSVC)
  install(FILES
    $<TARGET_PDB_FILE:libxml2>
    DESTINATION lib
  )
endif()