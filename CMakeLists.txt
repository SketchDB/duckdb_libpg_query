cmake_minimum_required(VERSION 3.5)

project(duckdb_pg_query CXX C)
if(POLICY CMP0063)
    cmake_policy(SET CMP0063 NEW)
endif()
set(CMAKE_CXX_VISIBILITY_PRESET hidden)

add_library(duckdb_pg_query STATIC
            postgres_parser.cpp
            pg_functions.cpp
            src_backend_parser_parser.cpp
            src_backend_parser_gram.cpp
            src_backend_nodes_list.cpp
            src_backend_parser_scansup.cpp
            src_backend_nodes_makefuncs.cpp
            src_backend_nodes_value.cpp
            src_backend_parser_scan.cpp
            src_common_keywords.cpp)

target_include_directories(
  duckdb_pg_query
  PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/includes_from_duckdb>
  )
set_target_properties(duckdb_pg_query PROPERTIES EXPORT_NAME duckdb_duckdb_pg_query)

install(TARGETS duckdb_pg_query
        EXPORT "${DUCKDB_EXPORT_SET}"
        LIBRARY DESTINATION "${INSTALL_LIB_DIR}"
        ARCHIVE DESTINATION "${INSTALL_LIB_DIR}")

function(disable_target_warnings NAME)
  if(MSVC)
    target_compile_options(${NAME} PRIVATE "/W0")
  elseif("${CMAKE_CXX_COMPILER_ID}" MATCHES "Clang$"
         OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    target_compile_options(${NAME} PRIVATE "-w")
  endif()
endfunction()

disable_target_warnings(duckdb_pg_query)

set_target_properties(duckdb_pg_query PROPERTIES POSITION_INDEPENDENT_CODE ON)

# add alias
add_library(contrib::duckdb_libpg_query ALIAS duckdb_pg_query)
