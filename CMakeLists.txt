cmake_minimum_required(VERSION 3.15)
project(ida-wii-loaders)

# Find the IDA SDK
set(IDA_SDK_DIR $ENV{IDASDK})
if(NOT IDA_SDK_DIR)
    message(FATAL_ERROR "IDASDK environment variable is not set! Please set it to your IDA SDK path.")
endif()

# Include IDA's official CMake helpers if they exist
#include("${IDA_SDK_DIR}/plugins/CMakeLists.txt" OPTIONAL)

# ---------------------------------------------------------
# Build the DOL Loader
# ---------------------------------------------------------
set(DOL_SOURCES 
    dol/dol.cpp 
    dol/dol_track.cpp
)

add_library(dol_loader SHARED ${DOL_SOURCES})

# Tell CMake where to find the SDK headers AND the local "loader" folder
target_include_directories(dol_loader PRIVATE 
    "${IDA_SDK_DIR}/include" 
    "${CMAKE_CURRENT_SOURCE_DIR}/loader"
    "${CMAKE_CURRENT_SOURCE_DIR}/dol"
)

# IDA 9.x 64-bit definitions
target_compile_definitions(dol_loader PRIVATE __EA64__ __X64__ __NT__)

# Link against IDA
if(WIN32)
    target_link_libraries(dol_loader "${IDA_SDK_DIR}/lib/x64_win_64/ida.lib")
endif()

# Rename the output file to something clean
set_target_properties(dol_loader PROPERTIES 
    PREFIX "" 
    OUTPUT_NAME "wii_dol_loader"
    LINK_FLAGS "/EXPORT:LDSC"
)
