#===================================
# Build script for TheGoban ========
#===================================

project(goban C CXX)

cmake_minimum_required(VERSION 3.0)

# paths
include(GNUInstallDirs)

set(GOBAN_VERSION_MAJOR 0)
set(GOBAN_VERSION_MINOR 1)
set(GOBAN_VERSION_PATCH 0)
set(GOBAN_VERSION_TWEAK 0)
set(PROJECT_VERSION ${GOBAN_VERSION_MAJOR}.${GOBAN_VERSION_MINOR}.${GOBAN_VERSION_PATCH}.${GOBAN_VERSION_TWEAK})

# Search in the 'cmake' directory for additional CMake modules.
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

#===================================
# Build options ====================
#===================================

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING
        "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
        FORCE)
endif()

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -O0 -g")
SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -s")

set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)

find_package(LibRocket REQUIRED)
#find_package(GnuGo REQUIRED)
find_package(OpenGL REQUIRED)
find_package(GLUT REQUIRED)
find_package(GLEW REQUIRED)
find_package(Portaudio REQUIRED)
find_package(LibSndFile REQUIRED)
find_package(Boost COMPONENTS thread system iostreams filesystem)
find_package(Curses REQUIRED)
find_package(X11 REQUIRED)
find_package(LibGlyphy REQUIRED)
find_package(Freetype REQUIRED)

#===================================
# Setup paths ======================
#===================================

get_cmake_property(_variableNames VARIABLES)
foreach (_variableName ${_variableNames})
    message(STATUS "${_variableName}=${${_variableName}}")
endforeach()

set(PROJECT_SOURCE_DIR ${PROJECT_SOURCE_DIR})

include_directories(
    ${PROJECT_SOURCE_DIR}/src
    ${PROJECT_SOURCE_DIR}/src/shell/include
    ${PROJECT_SOURCE_DIR}/src/glyphy/
    ${PROJECT_SOURCE_DIR}/src/sgf/include
    ${OPENGL_INCLUDE_DIRS}
    ${GLUT_INCLUDE_DIRS}
    ${GLEW_INCLUDE_DIRS}
    ${PORTAUDIO_INCLUDE_DIRS}
    ${LIBSNDFILE_INCLUDE_DIRS}
    ${BOOST_INCLUDE_DIRS}
    ${CURSES_INCLUDE_DIR}
    ${LIBROCKET_INCLUDE_DIR}
    ${LIBGLYPHY_INCLUDE_DIRS}
    ${FREETYPE_INCLUDE_DIRS}
)

# Include list of source files
#include(FileList)

set(SHELL_SRC_FILES
    ${PROJECT_SOURCE_DIR}/src/shell/src/Input.cpp
    ${PROJECT_SOURCE_DIR}/src/shell/src/Shell.cpp
    ${PROJECT_SOURCE_DIR}/src/shell/src/ShellFileInterface.cpp
    ${PROJECT_SOURCE_DIR}/src/shell/src/ShellRenderInterfaceOpenGL.cpp
    ${PROJECT_SOURCE_DIR}/src/shell/src/ShellSystemInterface.cpp
    ${PROJECT_SOURCE_DIR}/src/shell/src/x11
    ${PROJECT_SOURCE_DIR}/src/shell/src/x11/ShellRenderInterfaceExtensionsOpenGL_X11.cpp
    ${PROJECT_SOURCE_DIR}/src/shell/src/x11/InputX11.cpp
    ${PROJECT_SOURCE_DIR}/src/shell/src/x11/ShellX11.cpp
)

set(GLYPHY_SRC_FILES
    ${PROJECT_SOURCE_DIR}/src/glyphy/demo-atlas.cc
    ${PROJECT_SOURCE_DIR}/src/glyphy/demo-buffer.cc
    ${PROJECT_SOURCE_DIR}/src/glyphy/demo-font.cc
    ${PROJECT_SOURCE_DIR}/src/glyphy/demo-glstate.cc
    ${PROJECT_SOURCE_DIR}/src/glyphy/demo-shader.cc
    ${PROJECT_SOURCE_DIR}/src/glyphy/matrix4x4.c
)

set(GOBAN_HDR_FILES
    ${PROJECT_SOURCE_DIR}/src/Board.h
    ${PROJECT_SOURCE_DIR}/src/Camera.h
    ${PROJECT_SOURCE_DIR}/src/Complex.h
    ${PROJECT_SOURCE_DIR}/src/ElementGame.h
    ${PROJECT_SOURCE_DIR}/src/Event.h
    ${PROJECT_SOURCE_DIR}/src/EventHandler.h
    ${PROJECT_SOURCE_DIR}/src/EventHandlerNewGame.h
    ${PROJECT_SOURCE_DIR}/src/EventInstancer.h
    ${PROJECT_SOURCE_DIR}/src/EventManager.h
    ${PROJECT_SOURCE_DIR}/src/GameThread.h
    ${PROJECT_SOURCE_DIR}/src/gtpclient.h
    ${PROJECT_SOURCE_DIR}/src/opengl.h
    ${PROJECT_SOURCE_DIR}/src/player.h
    ${PROJECT_SOURCE_DIR}/src/Quaternion.h
    ${PROJECT_SOURCE_DIR}/src/Vector.h
    ${PROJECT_SOURCE_DIR}/src/GobanView.h
    ${PROJECT_SOURCE_DIR}/src/GobanModel.h
    ${PROJECT_SOURCE_DIR}/src/GobanControl.h
    ${PROJECT_SOURCE_DIR}/src/GobanShader.h
        ../src/Metrics.cpp ../src/Metrics.h ../src/GobanOverlay.cpp ../src/GobanOverlay.h ../src/GameState.cpp ../src/GameState.h)

set(GOBAN_SRC_FILES
    ${PROJECT_SOURCE_DIR}/src/Board.cpp
    ${PROJECT_SOURCE_DIR}/src/Camera.cpp
    ${PROJECT_SOURCE_DIR}/src/Complex.cpp
    ${PROJECT_SOURCE_DIR}/src/ElementGame.cpp
    ${PROJECT_SOURCE_DIR}/src/Event.cpp
    ${PROJECT_SOURCE_DIR}/src/EventHandler.cpp
    ${PROJECT_SOURCE_DIR}/src/EventHandlerNewGame.cpp
    ${PROJECT_SOURCE_DIR}/src/EventInstancer.cpp
    ${PROJECT_SOURCE_DIR}/src/EventManager.cpp
    ${PROJECT_SOURCE_DIR}/src/GameThread.cpp
    ${PROJECT_SOURCE_DIR}/src/gtpclient.cpp
    ${PROJECT_SOURCE_DIR}/src/main.cpp
    ${PROJECT_SOURCE_DIR}/src/player.cpp
    ${PROJECT_SOURCE_DIR}/src/Quaternion.cpp
    ${PROJECT_SOURCE_DIR}/src/Vector.cpp
    ${PROJECT_SOURCE_DIR}/src/GobanView.cpp
    ${PROJECT_SOURCE_DIR}/src/GobanModel.cpp
    ${PROJECT_SOURCE_DIR}/src/GobanControl.cpp
    ${PROJECT_SOURCE_DIR}/src/GobanShader.cpp
        ../src/Metrics.cpp ../src/Metrics.h ../src/GobanOverlay.cpp ../src/GobanOverlay.h ../src/GameState.cpp ../src/GameState.h)

if (WIN32)
    add_executable(goban WIN32 ${GOBAN_SRC_FILES})
else()
    add_executable(goban ${GOBAN_SRC_FILES} ${GOBAN_HDR_FILES} ${SHELL_SRC_FILES} ${GLYPHY_SRC_FILES})
endif()

ADD_DEFINITIONS(-DHAVE_CONFIG_H)

target_link_libraries(goban
    ${LIBROCKET_LIBRARIES}
#    ${GNUGO_LIBRARIES}
    ${OPENGL_LIBRARIES}
    ${GLUT_LIBRARY}
    ${GLEW_LIBRARY}
    ${PORTAUDIO_LIBRARIES}
#    ${LIBSNDFILE_LIBRARY}
    ${CMAKE_THREAD_LIBS_INIT}
    ${Boost_LIBRARIES}
    ${CURSES_LIBRARY}
    ${X11_LIBRARIES}
    ${LIBGLYPHY_LIBRARIES}
    ${FREETYPE_LIBRARIES}
)
set(RELEASE_DIR ${PROJECT_SOURCE_DIR}/Release)

file(COPY ${PROJECT_SOURCE_DIR}/data DESTINATION ${RELEASE_DIR})

install(DIRECTORY DESTINATION ${RELEASE_DIR})
install(TARGETS goban
    RUNTIME DESTINATION ${RELEASE_DIR}
    BUNDLE DESTINATION ${RELEASE_DIR})

