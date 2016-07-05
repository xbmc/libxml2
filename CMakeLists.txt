cmake_minimum_required(VERSION 3.2)

project(libxml2 LANGUAGES C)

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

if(WITH_ICONV)
  find_package(iconv REQUIRED NO_MODULE)
endif()

set(libxml2_VERSION 2.9.4)

if(MSVC)
  set(CMAKE_DEBUG_POSTFIX "d")
endif()


add_library(libxml2 
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
  runsuite.c
  runtest.c
  runxmlconf.c
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
  xmlcatalog.c
  xmlIO.c
  xmllint.c
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
target_include_directories(
  libxml2 PRIVATE
  $<BUILD_INTERFACE:.;include;include/libxml2>
  INTERFACE
  $<INSTALL_INTERFACE:include/libxml2>
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-version.cmake
  VERSION ${libxml2_VERSION}
  COMPATIBILITY AnyNewerVersion
)

install(TARGETS libxml2 EXPORT libxml2
  RUNTIME DESTINATION bin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib)


install(DIRECTORY
  ${CMAKE_CURRENT_SOURCE_DIR}/include/libxml2
  DESTINATION include/libxml2)

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