cmake_minimum_required(VERSION 3.12)
project(gfpgan-ncnn-vulkan)

set(CMAKE_BUILD_TYPE Release)

# -------------- Support C++17 for using filesystem  ------------------#
# https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD.html
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS ON)

# set(CMAKE_CXX_CLANG_TIDY
#         clang-tidy;
#         -header-filter=.;
#         -checks=*;
#         -warnings-as-errors=*;)

# set_target_properties(${target} PROPERTIES CXX_CLANG_TIDY "${CLANG_TIDY_PROGRAM}")

option(USE_SYSTEM_NCNN "build with system libncnn" OFF)
option(USE_SYSTEM_WEBP "build with system libwebp" OFF)


if(APPLE)
    find_program(BREW_EXECUTABLE NAMES brew)
    if(NOT BREW_EXECUTABLE)
        message(FATAL_ERROR "Brew not found!")
    endif()
    execute_process(COMMAND brew --prefix libomp
    OUTPUT_VARIABLE HOMEBREW_LIBOMP_PREFIX
    OUTPUT_STRIP_TRAILING_WHITESPACE)
    set(OpenMP_C_FLAGS
    "-Xpreprocessor -fopenmp -I${HOMEBREW_LIBOMP_PREFIX}/include")
    set(OpenMP_CXX_FLAGS
    "-Xpreprocessor -fopenmp -I${HOMEBREW_LIBOMP_PREFIX}/include")
    set(OpenMP_C_LIB_NAMES omp)
    set(OpenMP_CXX_LIB_NAMES omp)
    set(OpenMP_omp_LIBRARY ${HOMEBREW_LIBOMP_PREFIX}/lib/libomp.dylib)
endif()

find_package(OpenMP REQUIRED)
find_package(Threads)
find_package(Vulkan REQUIRED)

if (Vulkan_FOUND)
    message(STATUS "Vulkan_INCLUDE_DIRS: ${Vulkan_INCLUDE_DIRS}")
    message(STATUS "Vulkan_LIBRARIES: ${Vulkan_LIBRARIES}")
    message(STATUS "Vulkan_LIBRARY: ${Vulkan_LIBRARY}")
    message(STATUS "Vulkan_FOUND: ${Vulkan_FOUND}")
else ()
    message(FATAL_ERROR "Vulkan Not Found!")
endif ()

find_program(GLSLANGVALIDATOR_EXECUTABLE NAMES glslangValidator PATHS $ENV{VULKAN_SDK}/bin NO_CMAKE_FIND_ROOT_PATH)
message(STATUS "Found glslangValidator: ${GLSLANGVALIDATOR_EXECUTABLE}")

find_package(OpenCV REQUIRED)

if (OpenCV_FOUND)
    message(STATUS "OpenCV_LIBS: ${OpenCV_LIBS}")
    message(STATUS "OpenCV_INCLUDE_DIRS: ${OpenCV_INCLUDE_DIRS}")
else ()
    message(FATAL_ERROR "OpenCV Not Found!")
endif ()

find_package(OpenMP REQUIRED)
if (OPENMP_FOUND)
    message("OPENMP FOUND")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
else ()
    message(FATAL_ERROR "OpenMP Not Found!")
endif ()


if(USE_SYSTEM_NCNN)
    find_package(ncnn)
    if(NOT TARGET ncnn)
        message(WARNING "ncnn target not found! USE_SYSTEM_NCNN will be turned off.")
        set(USE_SYSTEM_NCNN OFF)
    endif()
endif()

if(NOT USE_SYSTEM_NCNN)
    # build ncnn library
    if(NOT EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/external/ncnn/CMakeLists.txt")
        message(FATAL_ERROR "The submodules were not downloaded! Please update submodules with \"git submodule update --init --recursive\" and try again.")
    endif()

    option(NCNN_VULKAN "vulkan compute support" ON)
    option(NCNN_THREADS "" ON)
    option(NCNN_OPENMP "" ON)
    option(NCNN_VULKAN_ONLINE_SPIRV "" ON)

    option(NCNN_PIXEL "convert and resize from/to image pixel" ON)
    option(NCNN_PIXEL_ROTATE "rotate image pixel orientation" ON)
    option(NCNN_PIXEL_AFFINE "warp affine image pixel" ON)
    option(NCNN_PIXEL_DRAWING "draw basic figure and text" ON)
    option(NCNN_INT8 "int8 inference" ON)
    option(NCNN_BF16 "bf16 inference" ON)


    option(NCNN_INSTALL_SDK "" OFF)
    option(NCNN_PIXEL_ROTATE "" OFF)
    option(NCNN_BUILD_BENCHMARK "" OFF)
    option(NCNN_BUILD_TESTS "" OFF)
    option(NCNN_BUILD_TOOLS "" OFF)
    option(NCNN_BUILD_EXAMPLES "" OFF)
    option(NCNN_DISABLE_RTTI "" OFF)
    option(NCNN_DISABLE_EXCEPTION "" OFF)
    option(NCNN_SYSTEM_GLSLANG "" OFF)
    option(NCNN_SHARED_LIB "" OFF)



    add_subdirectory(external/ncnn)
endif()

if(USE_SYSTEM_WEBP)
    set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}")
    find_package(WebP)
    if(NOT TARGET webp)
        message(WARNING "webp target not found! USE_SYSTEM_WEBP will be turned off.")
        set(USE_SYSTEM_WEBP OFF)
    endif()
endif()

if(NOT USE_SYSTEM_WEBP)
    # build libwebp library
    if(NOT EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/external/libwebp/CMakeLists.txt")
        message(FATAL_ERROR "The submodules were not downloaded! Please update submodules with \"git submodule update --init --recursive\" and try again.")
    endif()

    option(WEBP_ENABLE_SIMD "" ON)
    option(WEBP_BUILD_ANIM_UTILS "" OFF)
    option(WEBP_BUILD_CWEBP "" OFF)
    option(WEBP_BUILD_DWEBP "" OFF)
    option(WEBP_BUILD_GIF2WEBP "" OFF)
    option(WEBP_BUILD_IMG2WEBP "" OFF)
    option(WEBP_BUILD_VWEBP "" OFF)
    option(WEBP_BUILD_WEBPINFO "" OFF)
    option(WEBP_BUILD_WEBPMUX "" OFF)
    option(WEBP_BUILD_EXTRAS "" OFF)
    option(WEBP_BUILD_WEBP_JS "" OFF)
    option(WEBP_NEAR_LOSSLESS "" OFF)
    option(WEBP_ENABLE_SWAP_16BIT_CSP "" OFF)

    add_subdirectory(external/libwebp)

    include_directories(${CMAKE_CURRENT_SOURCE_DIR}/external/libwebp/src)
endif()

set(NCNN_GFPGAN_LINK_LIBRARIES ncnn webp ${Vulkan_LIBRARY})

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)


add_executable(gfpgan-ncnn-vulkan gfpgan-ncnn-vulkan-demo.cpp gfpgan.cpp face.cpp realesrgan.cpp)
target_link_libraries(gfpgan-ncnn-vulkan ${NCNN_GFPGAN_LINK_LIBRARIES} ${OpenCV_LIBS})
