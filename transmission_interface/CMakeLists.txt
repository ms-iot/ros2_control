cmake_minimum_required(VERSION 3.5)
project(transmission_interface)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(hardware_interface REQUIRED)
find_package(tinyxml2_vendor REQUIRED)
find_package(TinyXML2 REQUIRED)

add_library(transmission_parser SHARED src/transmission_parser.cpp)
target_include_directories(transmission_parser PUBLIC include)
ament_target_dependencies(transmission_parser hardware_interface tinyxml2_vendor TinyXML2)
target_compile_definitions(transmission_parser PRIVATE "TRANSMISSION_INTERFACE_BUILDING_DLL")

install(
  DIRECTORY include/
  DESTINATION include
)

if(BUILD_TESTING)
  find_package(ament_cmake_gmock REQUIRED)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()

  ament_add_gmock(
    test_transmission_parser
    test/test_transmission_parser.cpp
  )
  target_include_directories(test_transmission_parser PUBLIC include)
  target_link_libraries(test_transmission_parser transmission_parser)

  ament_add_gmock(
    test_simple_transmission
    test/simple_transmission_test.cpp
  )
  target_include_directories(test_simple_transmission PUBLIC include hardware_interface)
  ament_target_dependencies(test_simple_transmission hardware_interface)

  ament_add_gmock(
    test_differential_transmission
    test/differential_transmission_test.cpp
  )
  target_include_directories(test_differential_transmission PUBLIC include hardware_interface)
  ament_target_dependencies(test_differential_transmission hardware_interface)

  ament_add_gmock(
    test_four_bar_linkage_transmission
    test/four_bar_linkage_transmission_test.cpp
  )
  target_include_directories(test_four_bar_linkage_transmission PUBLIC include hardware_interface)
  ament_target_dependencies(test_four_bar_linkage_transmission hardware_interface)
endif()

ament_export_include_directories(
  include
)
ament_export_dependencies(
  hardware_interface
  tinyxml2_vendor
  TinyXML2
)
ament_package()
