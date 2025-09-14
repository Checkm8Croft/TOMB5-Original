cmake_minimum_required(VERSION 3.20)
project(TOMB5)

# Macro originali
macro(OPTIONAL_DEFINE DEFNAME DESCRIPTION VALUE)
    set(${DEFNAME} ${VALUE})
    list(APPEND OPT_DEFINITIONS ${DEFNAME})
endmacro()

macro(BuildPreProcessorDefinitions)
    foreach(DEF ${OPT_DEFINITIONS})
        if(${${DEF}} STREQUAL "ON")
            target_compile_definitions(${PROJECT_NAME} PRIVATE ${DEF}=1)
        else()
            target_compile_definitions(${PROJECT_NAME} PRIVATE ${DEF}=0)
        endif()
        list(REMOVE_ITEM OPT_DEFINITIONS ${DEF})
    endforeach()
endmacro()

# Opzioni di build personalizzate
option(PC "Build PC version (SPEC_PC_N)" OFF)
option(EMULATOR "Build Emulator" OFF)

# Path base
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/CMAKE")
set(ROOT_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(EXTERN_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/EXTERNAL)
set(GAME_CODE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/GAME)

# Sorgenti gioco
file(GLOB_RECURSE GAME_SRCS ${GAME_CODE_DIR}/*.C ${GAME_CODE_DIR}/*.H)

# Architettura MSVC
if (MSVC)
    if (CMAKE_CL_64)
        set(TARGET_ARCH "Win64")
    else()
        set(TARGET_ARCH "Win32")
    endif()
endif()

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Logica di build
if(PC)
    message(STATUS ">>> Building SPEC_PC_N (PC version)")
    add_subdirectory(SPEC_PC_N)
elseif(EMULATOR)
    message(STATUS ">>> Building Emulator (UNIX-like build)")
    add_subdirectory(EMULATOR)
else()
    message(FATAL_ERROR "Please select a build target with -DPC=ON or -DEMULATOR=ON")
endif()
