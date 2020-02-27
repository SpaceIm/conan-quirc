cmake_minimum_required(VERSION 3.0.2)
project(quirc C)

if(EXISTS "${CMAKE_BINARY_DIR}/conanbuildinfo.cmake")
  include("${CMAKE_BINARY_DIR}/conanbuildinfo.cmake")
else()
  include(conanbuildinfo.cmake)
endif()
conan_basic_setup()

set(QUIRC_MAX_REGIONS 254 CACHE STRING "Maximum number of regions")

set(QUIRC_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/source_subfolder/lib)

add_library(quirc
  ${QUIRC_SOURCE_DIR}/decode.c
  ${QUIRC_SOURCE_DIR}/identify.c
  ${QUIRC_SOURCE_DIR}/quirc.c
  ${QUIRC_SOURCE_DIR}/version_db.c
)

target_compile_definitions(quirc PRIVATE QUIRC_MAX_REGIONS=${QUIRC_MAX_REGIONS})

set_target_properties(quirc PROPERTIES VERSION ${QUIRC_VERSION})

target_include_directories(quirc PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
  $<INSTALL_INTERFACE:include>
)

set_target_properties(quirc PROPERTIES C_VISIBILITY_PRESET hidden)
include(GenerateExportHeader)
generate_export_header(quirc)

install(
  TARGETS quirc
  EXPORT quirc
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

set(PUBLIC_HEADERS ${QUIRC_SOURCE_DIR}/quirc.h ${CMAKE_CURRENT_BINARY_DIR}/quirc_export.h)
install(FILES ${PUBLIC_HEADERS} DESTINATION include)
