# rx-charts: charts for ReactX apps (browser: SVG, terminal: characters).
# In a ReactX project:  reactx add rx-charts <git-url> --rev v0.1.0 --target rx::charts
# On its own (tests):   cmake -S . -B build -G Ninja && cmake --build build && ctest --test-dir build
cmake_minimum_required(VERSION 3.28)
project(rx_charts VERSION 0.1.0 LANGUAGES CXX)

# A ReactX project brings ReactX first; on its own, fetch the release it is tested with.
# -DFETCHCONTENT_SOURCE_DIR_REACTX=/path/to/ReactX uses a local checkout instead.
if(NOT TARGET reactx::reactx)
  set(CMAKE_CXX_STANDARD 26)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
  include(FetchContent)
  FetchContent_Declare(reactx GIT_REPOSITORY https://github.com/sargisis/ReactX.git GIT_TAG v1.1.0 GIT_SHALLOW TRUE)
  FetchContent_MakeAvailable(reactx)
endif()

add_library(rx_charts STATIC src/charts.cpp)
add_library(rx::charts ALIAS rx_charts)
target_include_directories(rx_charts PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_link_libraries(rx_charts PUBLIC reactx::reactx)
target_compile_features(rx_charts PUBLIC cxx_std_26)
if(PROJECT_IS_TOP_LEVEL)
  target_compile_options(rx_charts PRIVATE -Wall -Wextra -Wpedantic)
endif()

if(PROJECT_IS_TOP_LEVEL AND NOT EMSCRIPTEN)
  enable_testing()
  add_executable(rx_charts_tests tests/charts_tests.cpp)
  target_link_libraries(rx_charts_tests PRIVATE rx::charts reactx::testing)
  target_compile_options(rx_charts_tests PRIVATE -Wall -Wextra -Wpedantic)
  add_test(NAME rx_charts_tests COMMAND rx_charts_tests)
endif()
