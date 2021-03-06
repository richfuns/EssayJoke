# Sets the minimum version of CMake required to build the native
# library. You should either keep the default value or only pass a
# value of 3.4.0 or lower.

cmake_minimum_required(VERSION 3.4.1)

#设置生成的so动态库最后输出的路径
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/libs/${ANDROID_ABI})

#设置头文件搜索路径（和此txt同个路径的头文件无需设置），可选
#INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/common)

#指定用到的系统库或者NDK库或者第三方库的搜索路径，可选。
#LINK_DIRECTORIES(/usr/local/lib)

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds it for you.
# Gradle automatically packages shared libraries with your APK.

#配置头文件路径
include_directories(${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/)

#添加源文件路径
aux_source_directory(${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/ CORE_SRC_LIST)
list(APPEND SRC_LIST ${CORE_SRC_LIST})

#添加libbspatch 库
add_library( # Sets the name of the library.
             bspatch
             SHARED
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/huffman.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/randtable.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/crctable.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/blocksort.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/bzip2.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/compress.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/decompress.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/bzlib.c
             ${PROJECT_SOURCE_DIR}/src/main/cpp/bsdiff/bspatch.c)

#***************************************************************************************************

add_library( # Sets the name of the library.
             diffpatch
             SHARED
             ${PROJECT_SOURCE_DIR}/src/main/cpp/diffpatch.cpp)

target_link_libraries( # Specifies the target library.
                       diffpatch
                       jnigraphics
                       bspatch
                       # Links the target library to the log library
                       # included in the NDK.
                       ${log-lib} )

#***************************************************************************************************
add_library( # Sets the name of the library.
             compressimg
             # Sets the library as a shared library.
             SHARED
             # Provides a relative path to your source file(s).
             # Associated headers in the same location as their source
             # file are automatically included.
             src/main/cpp/compress_image.cpp )

# 添加libjpeg.so依赖库
add_library(jpeg SHARED IMPORTED)

#设置libjpeg.so依赖库路径
set_target_properties(
  jpeg
  PROPERTIES IMPORTED_LOCATION
  ${PROJECT_SOURCE_DIR}/libs/${ANDROID_ABI}/libjpeg.so)

# Searches for a specified prebuilt library and stores the path as a
# variable. Because system libraries are included in the search path by
# default, you only need to specify the name of the public NDK library
# you want to add. CMake verifies that the library exists before
# completing its build.

find_library( # Sets the name of the path variable.
              log-lib

              # Specifies the name of the NDK library that
              # you want CMake to locate.
              log )

# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in the
# build script, prebuilt third-party libraries, or system libraries.

target_link_libraries( # Specifies the target library.
                       compressimg
                       jnigraphics
                       jpeg
                       # Links the target library to the log library
                       # included in the NDK.
                       ${log-lib} )
