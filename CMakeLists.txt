#
# Oldscool Intro by th.kattanek
#
###################################################
# Options
# -CMAKE_INSTALL_PREFIX=[INSTALL_PATH] --- Setzt den Installationspfad
# -DWIN32_STATIC_BUILD (TRUE or FLASE) --- Wenn TRUE wird ein Windows Static Binarie erstellt
# -DCMAKE_BUILD_TYPE (Release or Debug) --- Build Typ welches erzeugt werden soll (Default is Release)

cmake_minimum_required(VERSION 2.8)

##################### PROJEKT #####################

project("sdl2_app")
message(STATUS "Project Name: " ${PROJECT_NAME})

################## CONFIGURATION ##################

# Standard Build Typ = Release
if(NOT CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build (Debug or Release)" FORCE)
        message(STATUS "CMAKE_BUILD_TYPE NOT FOUND -- Default setting to Release")
endif()

# Standard WIN32_STATIC_BUILD = FALSE
if(NOT WIN32_STATIC_BUILD)
        set(WIN32_STATIC_BUILD FALSE CACHE STRING "Choose a static or shared win32 build (TRUE or FALSE)" FORCE)
        message(STATUS "WIN32_STATIC_BUILD NOT FOUND -- Default setting to FALSE")
endif()

if(WIN32)
    if(WIN32_STATIC_BUILD)
        message(STATUS "BUILD A W32 STATIC BINARY")
    endif()
endif()

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

############### COMPILER OPTIONEN #################

if(COMPILER_SUPPORTS_CXX11)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

if(WIN32)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -s -O2 -g2 -Wall")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -s -O2 -g2 -Wall")
endif()

############ VERSIONSNUMMER via GIT ###############

# Holen des aktuellen Arbeitsbranches
execute_process(
  COMMAND git rev-parse --abbrev-ref HEAD
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_BRANCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Holen des Hashecodes vom letzten Commits
execute_process(
  COMMAND git log -1 --format=%h
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_COMMIT_HASH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Version aus dem Tag ermitteln
execute_process(
  COMMAND git describe --always --tags
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE VERSION_STRING
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

add_definitions("-DVERSION_STRING=\"${VERSION_STRING}\"")

message(STATUS "Aktueller Git Hashcode : ${GIT_COMMIT_HASH}")
message(STATUS "Aktueller Arbeitsbranch: ${GIT_BRANCH}")
message(STATUS "Aktuelle ${PROJECT_NAME} Version : ${VERSION_STRING}")

############## ARCHITEKTUR ERMITTELN ##############

if ( "${CMAKE_SIZEOF_VOID_P}" EQUAL 4)
        set(ARCHITECTURE "32Bit")
elseif ( "${CMAKE_SIZEOF_VOID_P}" EQUAL 8)
        set(ARCHITECTURE "64Bit")
endif()

add_definitions("-DARCHITECTURE_STRING=\"${ARCHITECTURE}\"")

message(STATUS "Architektur: ${ARCHITECTURE}")

##################### SOURCES #####################

set(SOURCES
    src/main.cpp
)

################### RESOURCEN #####################

if(WIN32)
    set(WIN_RC ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}.rc)
endif()

##################### LIBRARY #####################

# X11
if (UNIX)
    find_package(X11 REQUIRED)
    if(X11_FOUND)
        include_directories(${X11_INCLUDE_DIR})
    else()
        message(FATAL_ERROR "X11 wurde nicht gefunden!")
    endif()
endif()

# SDL2
find_package(SDL2 REQUIRED)
if(SDL2_FOUND)
    include_directories(${SDL2_INCLUDE_DIR})
else()
    message(FATAL_ERROR "SDL2 wurde nicht gefunden!")
endif()

# SDL2 Image
find_package(SDL2_image REQUIRED)
if(SDL2_IMAGE_FOUND)
    include_directories(${SDL2_IMAGE_INCLUDE_DIR})
else()
    message(FATAL_ERROR "SDL2_IMAGE wurde nicht gefunden.")
endif()

# SDL2 Gfx
find_package(SDL2_gfx REQUIRED)
if(SDL2_GFX_FOUND)
    include_directories(${SDL2_GFX_INCLUDE_DIR})
else()
    message(FATAL_ERROR "SDL2_GFX wurde nicht gefunden.")
endif()

# SDL2 Mixer
find_package(SDL2_mixer REQUIRED)
if(SDL2_MIXER_FOUND)
    include_directories(${SDL2_MIXER_INCLUDE_DIR})
else()
    message(FATAL_ERROR "SDL2_MIXER wurde nicht gefunden.")
endif()

################### EXECUTABLE ####################

if(NOT WIN32)
    add_executable(${PROJECT_NAME} ${SOURCES})
else()
    add_executable(${PROJECT_NAME} ${SOURCES})
endif()

target_link_libraries(${PROJECT_NAME} ${SDL2_LIBRARY})
target_link_libraries(${PROJECT_NAME} ${SDL2_IMAGE_LIBRARY})
target_link_libraries(${PROJECT_NAME} ${SDL2_GFX_LIBRARY})
target_link_libraries(${PROJECT_NAME} ${SDL2_MIXER_LIBRARY})

if(WIN32)
    target_link_libraries(${PROJECT_NAME} ${WIN32_LIBS})
endif()
    
################### INSTALL #######################

# Set DataPath
if(WIN32)
    add_definitions("-DDATA_PATH=\"./data/\"")
else()
    add_definitions("-DDATA_PATH=\"${CMAKE_INSTALL_PREFIX}/share/${PROJECT_NAME}/data/\"")
endif()

############ Install Binary und Daten #############
if(WIN32)

    ################## Windows #################
    # Install Binary
    install(TARGETS ${PROJECT_NAME} DESTINATION ./)

    # Install all Datas
    install(FILES "data/sdl2_app.png" DESTINATION ./data)

    # Sonstiges
    install(FILES "LICENSE" DESTINATION ./)

else()
    ################## Linux #################
    # Install Binary
    install(TARGETS ${PROJECT_NAME} DESTINATION bin)

    # Install all Datas
    install(FILES "data/sdl2_app.png" DESTINATION ./data)

    # Install Startmenü Eintrag
    install(FILES "src/${PROJECT_NAME}.desktop" DESTINATION share/applications)
    install(FILES "data/${PROJECT_NAME}.png" DESTINATION share/icons/hicolor/64x64/apps)

    # Sonstiges
    install(FILES "LICENSE" DESTINATION share/${PROJECT_NAME})
endif()
