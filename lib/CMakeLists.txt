# Copyright 2011,2012,2016 Free Software Foundation, Inc.
#
# This file is part of GNU Radio
#
# GNU Radio is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3, or (at your option)
# any later version.
#
# GNU Radio is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with GNU Radio; see the file COPYING.  If not, write to
# the Free Software Foundation, Inc., 51 Franklin Street,
# Boston, MA 02110-1301, USA.

########################################################################
# Setup library
########################################################################
include(GrPlatform) #define LIB_SUFFIX

include_directories(${Boost_INCLUDE_DIR})
link_directories(${Boost_LIBRARY_DIRS})
list(APPEND ccsds_sources
    fec/ReedSolomon/ReedSolomon.cc
    fec/Scrambler/Scrambler.cc
    fec/rs/reedsolomon.cc
    fec/turbo/libconvcodes.c
    fec/turbo/libturbocodes.c
    fec/ldpc/variablenode.cpp
    fec/ldpc/checknode.cpp
    fec/ldpc/ldpc.cpp
    fec/frame_sync/argmax.cc
    convEncoder_impl.cc
    convDecoder_impl.cc
    generateCADU_impl.cc
    processCADU_impl.cc
    extractCADU_impl.cc
    synchronizeCADU_impl.cc
    generateCLTU_impl.cc
    genCADU_impl.cc
    simplifiedPLOP1_impl.cc
    cc_depuncture_impl.cc
    createCADU_impl.cc
    recoverCADU_impl.cc
    encodeRS_impl.cc
    decodeRS_impl.cc
    encodeTurbo_impl.cc
    synchronizeCADUSoft_impl.cc
    recoverCADUSoft_impl.cc
    decodeTurbo_impl.cc
    synchronizeCADUHard_impl.cc
    encodeLDPC_impl.cc
    decodeLDPC_impl.cc
    synchronizeCADUArgMax_impl.cc )

set(ccsds_sources "${ccsds_sources}" PARENT_SCOPE)
if(NOT ccsds_sources)
	MESSAGE(STATUS "No C++ sources... skipping lib/")
	return()
endif(NOT ccsds_sources)

add_library(gnuradio-ccsds SHARED ${ccsds_sources})
target_link_libraries(gnuradio-ccsds ${Boost_LIBRARIES} ${GNURADIO_ALL_LIBRARIES})
set_target_properties(gnuradio-ccsds PROPERTIES DEFINE_SYMBOL "gnuradio_ccsds_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-ccsds PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

########################################################################
# Install built library files
########################################################################
include(GrMiscUtils)
GR_LIBRARY_FOO(gnuradio-ccsds RUNTIME_COMPONENT "ccsds_runtime" DEVEL_COMPONENT "ccsds_devel")

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

include_directories(${CPPUNIT_INCLUDE_DIRS})

list(APPEND test_ccsds_sources
    ${CMAKE_CURRENT_SOURCE_DIR}/test_ccsds.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/qa_ccsds.cc
)

add_executable(test-ccsds ${test_ccsds_sources})

target_link_libraries(
  test-ccsds
  ${GNURADIO_RUNTIME_LIBRARIES}
  ${Boost_LIBRARIES}
  ${CPPUNIT_LIBRARIES}
  gnuradio-ccsds
)

GR_ADD_TEST(test_ccsds test-ccsds)

########################################################################
# Print summary
########################################################################
message(STATUS "Using install prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "Building for version: ${VERSION} / ${LIBVER}")

