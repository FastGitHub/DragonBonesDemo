#/****************************************************************************
# Copyright (c) 2013-2014 cocos2d-x.org
# Copyright (c) 2015 Chukong Technologies Inc.
#
# http://www.cocos2d-x.org
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:

# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.
# ****************************************************************************/
cmake_policy(SET CMP0017 NEW)

cmake_minimum_required(VERSION 3.1)

set(APP_NAME ArcUnlim)
project (${APP_NAME})

set(COCOS2D_ROOT ${CMAKE_SOURCE_DIR}/cocos2d)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${COCOS2D_ROOT}/cmake/Modules/")
include(CocosBuildHelpers)

# libcocos2d
set(BUILD_CPP_EMPTY_TEST OFF CACHE BOOL "turn off build cpp-empty-test")
set(BUILD_CPP_TESTS OFF CACHE BOOL "turn off build cpp-tests")
set(BUILD_LUA_LIBS OFF CACHE BOOL "turn off build lua related targets")
set(BUILD_JS_LIBS OFF CACHE BOOL "turn off build js related targets")
add_subdirectory(${COCOS2D_ROOT})

# Some macro definitions
if(WINDOWS)
  if(BUILD_SHARED_LIBS)
    ADD_DEFINITIONS (-D_USRDLL -D_EXPORT_DLL_ -D_USEGUIDLL -D_USREXDLL -D_USRSTUDIODLL)
  else()
    ADD_DEFINITIONS (-DCC_STATIC)
  endif()

  ADD_DEFINITIONS (-DCOCOS2DXWIN32_EXPORTS -D_WINDOWS -DWIN32 -D_WIN32)
  set(PLATFORM_FOLDER win32)
elseif(MACOSX OR APPLE)
  ADD_DEFINITIONS (-DCC_TARGET_OS_MAC)
  ADD_DEFINITIONS (-DUSE_FILE32API)
  set(PLATFORM_FOLDER mac)
elseif(LINUX)
  ADD_DEFINITIONS(-DLINUX)
  set(PLATFORM_FOLDER linux)
elseif(ANDROID)
  ADD_DEFINITIONS (-DUSE_FILE32API)
  set(PLATFORM_FOLDER android)
else()
  message( FATAL_ERROR "Unsupported platform, CMake will exit" )
endif()


# Compiler options
if(MSVC)
  ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS -D_SCL_SECURE_NO_WARNINGS
                  -wd4251 -wd4244 -wd4334 -wd4005 -wd4820 -wd4710
                  -wd4514 -wd4056 -wd4996 -wd4099)
else()
  set(CMAKE_C_FLAGS_DEBUG "-g -Wall -DCOCOS2D_DEBUG=1")
  set(CMAKE_CXX_FLAGS_DEBUG ${CMAKE_C_FLAGS_DEBUG})
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wno-deprecated-declarations -Wno-reorder")
  if(CLANG)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
  endif()
endif(MSVC)


set(PLATFORM_SPECIFIC_SRC)
set(PLATFORM_SPECIFIC_HEADERS)

if(MACOSX OR APPLE)
    set(PLATFORM_SPECIFIC_SRC
      proj.ios_mac/mac/main.cpp
    )
elseif(LINUX)
    set(PLATFORM_SPECIFIC_SRC
      proj.linux/main.cpp
    )
elseif ( WIN32 )
    set(PLATFORM_SPECIFIC_SRC
      proj.win32/main.cpp
    )
    set(PLATFORM_SPECIFIC_HEADERS
      proj.win32/main.h
      proj.win32/resource.h
    )
elseif(ANDROID)
    set(PLATFORM_SPECIFIC_SRC
      proj.android-studio/app/jni/hellocpp/main.cpp
    )
endif()

include_directories(
  /usr/local/include/GLFW
  /usr/include/GLFW
  ${COCOS2D_ROOT}/cocos
  ${COCOS2D_ROOT}/cocos/platform
  ${COCOS2D_ROOT}/cocos/audio/include/
  Classes
        Classes/dragonBones
        Classes/rapidjson
        Classes/rapidxml
)
if ( WIN32 )
  include_directories(
  ${COCOS2D_ROOT}/external/glfw3/include/win32
  ${COCOS2D_ROOT}/external/win32-specific/gles/include/OGLES
)
endif( WIN32 )

set(GAME_SRC
        Classes/PerformanceTest.cpp
        Classes/HelloWorldScene.cpp
        Classes/HelloDragonBones.cpp
        Classes/AppDelegate.cpp
        Classes/AnimationBaseTest.cpp
        Classes/CoreElement.cpp
        Classes/Scenes/Battle.cpp

        Classes/dragonBones/animation/Animation.cpp
        Classes/dragonBones/animation/AnimationState.cpp
        Classes/dragonBones/animation/TimelineState.cpp
        Classes/dragonBones/animation/WorldClock.cpp

        Classes/dragonBones/armature/Armature.cpp
        Classes/dragonBones/armature/Bone.cpp
        Classes/dragonBones/armature/Slot.cpp

        Classes/dragonBones/cocos2dx/CCArmatureDisplay.cpp
        Classes/dragonBones/cocos2dx/CCFactory.cpp
        Classes/dragonBones/cocos2dx/CCSlot.cpp
        Classes/dragonBones/cocos2dx/CCTextureData.cpp

        Classes/dragonBones/core/BaseObject.cpp

        Classes/dragonBones/events/EventObject.cpp

        Classes/dragonBones/factories/BaseFactory.cpp

        Classes/dragonBones/model/AnimationData.cpp
        Classes/dragonBones/model/ArmatureData.cpp
        Classes/dragonBones/model/DragonBonesData.cpp
        Classes/dragonBones/model/FrameData.cpp
        Classes/dragonBones/model/TimelineData.cpp

        Classes/dragonBones/parsers/DataParser.cpp
        Classes/dragonBones/parsers/JSONDataParser.cpp

        Classes/dragonBones/textures/TextureData.cpp

  ${PLATFORM_SPECIFIC_SRC}
)

set(GAME_HEADERS
        Classes/AnimationBaseTest.h
        Classes/AppDelegate.h
        Classes/CoreElement.h
        Classes/HelloDragonBones.h
        Classes/HelloWorldScene.h
        Classes/Scenes/Battle.h
        Classes/PerformanceTest.h

        Classes/dragonBones/animation/Animation.h
        Classes/dragonBones/animation/AnimationState.h
        Classes/dragonBones/animation/BaseTimelineState.h
        Classes/dragonBones/animation/IAnimateble.h
        Classes/dragonBones/animation/TimelineState.h
        Classes/dragonBones/animation/WorldClock.h

        Classes/dragonBones/armature/Armature.h
        Classes/dragonBones/armature/Bone.h
        Classes/dragonBones/armature/IArmatureDisplay.h
        Classes/dragonBones/armature/Slot.h
        Classes/dragonBones/armature/TransformObject.h

        Classes/dragonBones/cocos2dx/CCArmatureDisplay.h
        Classes/dragonBones/cocos2dx/CCDragonBonesHeaders.h
        Classes/dragonBones/cocos2dx/CCFactory.h
        Classes/dragonBones/cocos2dx/CCSlot.h
        Classes/dragonBones/cocos2dx/CCTextureData.h

        Classes/dragonBones/core/BaseObject.h
        Classes/dragonBones/core/DragonBones.h

        Classes/dragonBones/events/EventObject.h

        Classes/dragonBones/factories/BaseFactory.h

        Classes/dragonBones/geom/Matrix.h
        Classes/dragonBones/geom/ColorTransform.h
        Classes/dragonBones/geom/Rectangle.h
        Classes/dragonBones/geom/Point.h
        Classes/dragonBones/geom/Transform.h

        Classes/dragonBones/model/AnimationData.h
        Classes/dragonBones/model/ArmatureData.h
        Classes/dragonBones/model/DragonBonesData.h
        Classes/dragonBones/model/FrameData.h
        Classes/dragonBones/model/TimelineData.h

        Classes/dragonBones/parsers/DataParser.h
        Classes/dragonBones/parsers/JSONDataParser.h

        Classes/dragonBones/textures/TextureData.h

        Classes/dragonBones/DragonBonesHeaders.h

        Classes/rapidjson/allocators.h
        Classes/rapidjson/document.h
        Classes/rapidjson/encodedstream.h
        Classes/rapidjson/encodings.h
        Classes/rapidjson/filereadstream.h
        Classes/rapidjson/filewritestream.h
        Classes/rapidjson/fwd.h
        Classes/rapidjson/istreamwrapper.h
        Classes/rapidjson/memorybuffer.h
        Classes/rapidjson/memorystream.h
        Classes/rapidjson/ostreamwrapper.h
        Classes/rapidjson/pointer.h
        Classes/rapidjson/prettywriter.h
        Classes/rapidjson/rapidjson.h
        Classes/rapidjson/reader.h
        Classes/rapidjson/schema.h
        Classes/rapidjson/stream.h
        Classes/rapidjson/stringbuffer.h
        Classes/rapidjson/writer.h

        Classes/rapidjson/error/en.h
        Classes/rapidjson/error/error.h

        Classes/rapidjson/internal/biginteger.h
        Classes/rapidjson/internal/diyfp.h
        Classes/rapidjson/internal/dtoa.h
        Classes/rapidjson/internal/ieee754.h
        Classes/rapidjson/internal/itoa.h
        Classes/rapidjson/internal/meta.h
        Classes/rapidjson/internal/pow10.h
        Classes/rapidjson/internal/regex.h
        Classes/rapidjson/internal/stack.h
        Classes/rapidjson/internal/strfunc.h
        Classes/rapidjson/internal/strtod.h
        Classes/rapidjson/internal/swap.h

        Classes/rapidjson/msinttypes/inttypes.h
        Classes/rapidjson/msinttypes/stdint.h


  ${PLATFORM_SPECIFIC_HEADERS}
        Classes/GameEngine/Objects/Stickman.cpp
        Classes/GameEngine/Objects/Stickman.h
        Classes/GameEngine/Objects/Gate.cpp
        Classes/GameEngine/Objects/Gate.h
        Classes/GameEngine/Objects/Arrow.cpp
        Classes/GameEngine/Objects/Arrow.h
        Classes/GameEngine/Objects/Bow.cpp
        Classes/GameEngine/Objects/Bow.h
        Classes/GameEngine/Objects/Hero.cpp
        Classes/GameEngine/Objects/Hero.h
        Classes/GameEngine/Objects/Aim.cpp
        Classes/GameEngine/Objects/Aim.h
        Classes/GameEngine/Brain.cpp
        Classes/GameEngine/Brain.h
        Classes/GameEngine/WeaponSelector.cpp
        Classes/GameEngine/WeaponSelector.h
        Classes/GameEngine/Objects/Target.cpp
        Classes/GameEngine/Objects/Target.h
        )

if( ANDROID )
    add_library(${APP_NAME} SHARED ${GAME_SRC} ${GAME_HEADERS})
    IF(CMAKE_BUILD_TYPE MATCHES RELEASE)
        ADD_CUSTOM_COMMAND(TARGET ${APP_NAME} POST_BUILD COMMAND ${CMAKE_STRIP} lib${APP_NAME}.so)
    ENDIF()
else()
    add_executable(${APP_NAME} ${GAME_SRC} ${GAME_HEADERS})
endif()

target_link_libraries(${APP_NAME} cocos2d)

set(APP_BIN_DIR "${CMAKE_BINARY_DIR}/bin")

set_target_properties(${APP_NAME} PROPERTIES
     RUNTIME_OUTPUT_DIRECTORY  "${APP_BIN_DIR}")

if ( WIN32 )
  #also copying dlls to binary directory for the executable to run
  pre_build(${APP_NAME}
    COMMAND ${CMAKE_COMMAND} -E remove_directory ${APP_BIN_DIR}/Resources
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/Resources ${APP_BIN_DIR}/Resources
    COMMAND ${CMAKE_COMMAND} -E copy ${COCOS2D_ROOT}/external/win32-specific/gles/prebuilt/glew32.dll ${APP_BIN_DIR}/${CMAKE_BUILD_TYPE}
	COMMAND ${CMAKE_COMMAND} -E copy ${COCOS2D_ROOT}/external/win32-specific/zlib/prebuilt/zlib1.dll ${APP_BIN_DIR}/${CMAKE_BUILD_TYPE}
	)
elseif( ANDROID )

else()
  pre_build(${APP_NAME}
    COMMAND ${CMAKE_COMMAND} -E remove_directory ${APP_BIN_DIR}/Resources
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/Resources ${APP_BIN_DIR}/Resources
    )

endif()
