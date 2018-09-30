cmake_minimum_required(VERSION 3.1)

project(tensorflow-lite)

set(CMAKE_CXX_STANDARD 11)

file(GLOB PROFILER_SRCS profiling/time.cc)
file(GLOB PROFILE_SUMMARIZER_SRCS profiling/profile_summarizer.cc util/stats_calculator.cc)

file(GLOB CORE_CC_ALL_SRCS
  *.c
  *.cc
  c/*.c
  core/api/*.c
  core/api/*.cc
  kernels/*.c
  kernels/*.cc
  kernels/internal/*.c
  kernels/internal/*.cc
  kernels/internal/optimized/*.c
  kernels/internal/optimized/*.cc
  kernels/internal/reference/*.c
  kernels/internal/reference/*.cc
  dependencies/farmhash/src/farmhash.cc
  dependencies/fft2d/fftsg.c
  )

foreach(item ${CORE_CC_ALL_SRCS})
  if(${item} MATCHES "_test")
    list(REMOVE_ITEM CORE_CC_ALL_SRCS ${item})
  endif(${item} MATCHES "_test")
  if(${item} MATCHES "test_")
    list(REMOVE_ITEM CORE_CC_ALL_SRCS ${item})
  endif(${item} MATCHES "test_")
endforeach(item)

list(REMOVE_ITEM CORE_CC_ALL_SRCS model.cc)
list(REMOVE_ITEM CORE_CC_ALL_SRCS nnapi_delegate.cc)

set(TENSORFLOW_LITE_SOURCES
  ${PROFILER_SRCS}
  ${PROFILE_SUMMARIZER_SRCS}
  ${CORE_CC_ALL_SRCS}
)

add_library(tensorflow-lite STATIC ${TENSORFLOW_LITE_SOURCES})

target_include_directories(tensorflow-lite
  PUBLIC ../../../
  PUBLIC  dependencies
  PUBLIC  dependencies/absl
  PUBLIC  dependencies/eigen
  PUBLIC  dependencies/gemmlowp
  PUBLIC  dependencies/neon_2_sse
  PUBLIC  dependencies/farmhash/src
  PUBLIC  dependencies/flatbuffers/include
)

add_subdirectory(examples/label_image)
