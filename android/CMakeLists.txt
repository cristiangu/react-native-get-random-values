cmake_minimum_required(VERSION 3.9.0)

project(RNSodium)
set(PACKAGE_NAME RNSodium)

set(CMAKE_VERBOSE_MAKEFILE ON)
set(CMAKE_CXX_STANDARD 20)

# Define C++ library and add all sources
add_library(
  ${PACKAGE_NAME} SHARED
  src/main/cpp-adapter.cpp
  ../cpp/Sodium.cpp
)


include_directories(
  ../cpp
  "${CMAKE_SOURCE_DIR}/../libsodium-android/${CMAKE_ANDROID_ARCH_ABI}/include"
)

include_directories(
  ../cpp
)
find_package(fbjni REQUIRED) # <-- Used for communication between Java <-> C++
find_package(ReactAndroid REQUIRED) # <-- Used to set up React Native bindings (e.g. CallInvoker/TurboModule)
find_package(react-native-nitro-modules REQUIRED) # <-- Used to create all HybridObjects and use the Nitro core library

# Third party libraries (Prefabs)
find_library(LOG_LIB log)

add_library(sodiumcpp SHARED IMPORTED)

set_target_properties(
  sodiumcpp
  PROPERTIES
  IMPORTED_LOCATION
  ${CMAKE_SOURCE_DIR}/../libsodium-android/${CMAKE_ANDROID_ARCH_ABI}/lib/libsodium.so
)

# Link all libraries together
target_link_libraries(
  ${PACKAGE_NAME}
  ${LOG_LIB}           
  android
  sodiumcpp
  fbjni::fbjni
  ReactAndroid::jsi
  ReactAndroid::reactnative
  react-native-nitro-modules::NitroModules  # <-- NitroModules Core :)
  )
