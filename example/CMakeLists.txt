# Copyright (c) 2016-2018 Martin Moene.
#
# This code is licensed under the MIT License (MIT).

project( example )

include_directories  ( "${PROJECT_SOURCE_DIR}/../include/nonstd" )

set( SOURCES_CPP11
    02-required.cpp 
)

set( SOURCES_CPP14 
    01-basic.cpp 
)

# note: here variable must be quoted to create semicolon separated list:

string( REPLACE ".cpp" "" BASENAMES_CPP11 "${SOURCES_CPP11}" )
string( REPLACE ".cpp" "" BASENAMES_CPP14 "${SOURCES_CPP14}" )

set( TARGETS_CPP11 ${BASENAMES_CPP11} )
set( TARGETS_CPP14 ${BASENAMES_CPP14} )
set( TARGETS_ALL   ${TARGETS_CPP11} ${TARGETS_CPP14} )

# add targets:

foreach( name ${TARGETS_ALL} )
    add_executable( ${name} ${name}.cpp )
endforeach()

# set compiler options:

if( ${CMAKE_GENERATOR} MATCHES Visual )
    foreach( name ${TARGETS_ALL} )
        target_compile_options( ${name} PUBLIC -W3 -EHsc -wd4814 -Zc:implicitNoexcept- )
    endforeach()
else()
    foreach( name ${TARGETS_ALL} )
        target_compile_options( ${name} PUBLIC -Wall )
    endforeach()

    foreach( name ${TARGETS_CPP11} )
        target_compile_options( ${name} PUBLIC -std=c++11 )
    endforeach()

    foreach( name ${TARGETS_CPP14} )
        target_compile_options( ${name} PUBLIC -std=c++14 )
    endforeach()
endif()

# configure unit tests via CTest:

enable_testing()

foreach( name ${TARGETS_ALL} )
    add_test    ( NAME ${name} COMMAND ${name} )
    set_property( TEST ${name} PROPERTY LABELS example )
endforeach()

# end of file
