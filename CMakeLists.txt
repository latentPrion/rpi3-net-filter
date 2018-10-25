cmake_minimum_required(VERSION 3.8.2)

project(Rpi3NetFilter C)

set(PLATFORM "rpi3" CACHE STRING "Platform to use; forced to rpi3." FORCE)
if (NOT RELEASE)
	set(RELEASE OFF CACHE BOOL "")
endif()
if (NOT VERIFICATION)
	set(VERIFICATION OFF CACHE BOOL "")
endif()

set(KernelArch arm CACHE STRING "" FORCE)
set(KernelArmSel4Arch aarch32 CACHE STRING "" FORCE)
set(KernelARMPlatform rpi3 CACHE STRING "" FORCE)
if ((NOT ARM) AND (NOT AARCH32) AND ("${CROSS_COMPILER_PREFIX}" STREQUAL ""))
    message(WARNING "Looks like you building for ARM without using a cross compiler")
endif()
ApplyData61ElfLoaderSettings(${KernelARMPlatform} ${KernelArmSel4Arch})

find_file(GLOBAL_COMPONENTS_PATH global-components.cmake PATHS ${CMAKE_SOURCE_DIR}/projects/global-components/ CMAKE_FIND_ROOT_PATH_BOTH)
mark_as_advanced(FORCE GLOBAL_COMPONENTS_PATH)
if("${GLOBAL_COMPONENTS_PATH}" STREQUAL "GLOBAL_COMPONENTS_PATH-NOTFOUND")
    message(FATAL_ERROR "Failed to find global-components.cmake. Consider cmake -DGLOBAL_COMPONENTS_PATH=/path/to/global-components.cmake")
endif()

function(includeGlobalComponents)
include(${GLOBAL_COMPONENTS_PATH})
endfunction()

CAmkESAddImportPath(interfaces)
CAmkESAddImportPath(templates)
CAmkESAddImportPath(components)

add_subdirectory(components/rpi3-ip-stack)
add_subdirectory(apps/echotest)

if (SIMULATION)
    ApplyCommonSimulationSettings(${KernelArch})
endif()

ApplyCommonReleaseVerificationSettings(${RELEASE} FALSE)
