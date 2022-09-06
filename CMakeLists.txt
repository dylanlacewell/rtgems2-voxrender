# ======================================================================== #
#                                                                          #
# Licensed under the Apache License, Version 2.0 (the "License");          #
# you may not use this file except in compliance with the License.         #
# You may obtain a copy of the License at                                  #
#                                                                          #
#     http://www.apache.org/licenses/LICENSE-2.0                           #
#                                                                          #
# Unless required by applicable law or agreed to in writing, software      #
# distributed under the License is distributed on an "AS IS" BASIS,        #
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. #
# See the License for the specific language governing permissions and      #
# limitations under the License.                                           #
# ======================================================================== #

if (POLICY CMP0048)
  cmake_policy(SET CMP0048 NEW)
endif()
project(RTGemsVoxRender LANGUAGES C CXX CUDA)

cmake_minimum_required(VERSION 3.12.0)

# Default to RELEASE build; OWL shader compilation depends on it
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "" FORCE)
endif()
set_property(CACHE CMAKE_BUILD_TYPE PROPERTY HELPSTRING "Choose the type of build")
# set the valid options for cmake-gui drop-down list
set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug;Release;RelWithDebInfo")


set(owl_dir ${PROJECT_SOURCE_DIR}/owl)
add_subdirectory(${owl_dir} EXCLUDE_FROM_ALL)

include(embed_ptx)

embed_ptx(
  OUTPUT_TARGET
    rtgems2-voxrender-ptx
  PTX_LINK_LIBRARIES
    owl::owl
  SOURCES
    deviceCode.cu
  )

add_executable(rtgems2-voxrender
  constants.h
  deviceCode.h
  hostCode.cpp
  readVoxOGT.cpp
  readVox.h
  resource.cpp
  )

target_link_libraries(rtgems2-voxrender PRIVATE rtgems2-voxrender-ptx owl::owl owl_viewer)

# set the default target in VS
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT rtgems2-voxrender)

