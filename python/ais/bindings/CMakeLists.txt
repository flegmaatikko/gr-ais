# Copyright 2020 Free Software Foundation, Inc.
#
# This file is part of GNU Radio
#
# SPDX-License-Identifier: GPL-3.0-or-later
#

########################################################################
# Check if there is C++ code at all
########################################################################
if(NOT ais_sources)
    MESSAGE(STATUS "No C++ sources... skipping python bindings")
    return()
endif(NOT ais_sources)

########################################################################
# Check for pygccxml
########################################################################
GR_PYTHON_CHECK_MODULE_RAW(
    "pygccxml"
    "import pygccxml"
    PYGCCXML_FOUND
    )

include(GrPybind)

########################################################################
# Python Bindings
########################################################################

list(APPEND ais_python_files
    freqest_python.cc
    corr_est_cc_python.cc
    msk_timing_recovery_cc_python.cc
    pdu_to_nmea_python.cc
    invert_python.cc
    python_bindings.cc)

GR_PYBIND_MAKE_OOT(ais
   ../../..
   gr::ais
   "${ais_python_files}")

# copy bindings extension for use in QA test module
add_custom_command(TARGET ais_python POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:ais_python>
    ${PROJECT_BINARY_DIR}/test_modules/gnuradio/ais/
)

install(TARGETS ais_python DESTINATION ${GR_PYTHON_DIR}/gnuradio/ais COMPONENT pythonapi)
