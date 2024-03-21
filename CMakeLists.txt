cmake_minimum_required(VERSION 3.8)
project(ftxui_vendor)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
# TODO rolling
# find_package(ament_cmake_vendor_package REQUIRED)

set(ftxui_package_version 5.0.0)
find_package(ftxui ${ftxui_package_version} QUIET)

# TODO rolling
# ament_vendor(ftxui_vendor
#   SATISFIED ${ftxui_FOUND}
#   VCS_URL https://github.com/ArthurSonzogni/FTXUI.git
#   VCS_VERSION v${ftxui_package_version}
# )

if(NOT ftxui_FOUND OR "${ftxui_VERSION}" VERSION_LESS "${ftxui_package_version}")
  set(extra_cmake_args)
  set(ftxui_install_dir "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-install")

  list(APPEND extra_cmake_args "-D CMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}")
  list(APPEND extra_cmake_args "-D CMAKE_INSTALL_PREFIX=${ftxui_install_dir}")

  if(DEFINED CMAKE_BUILD_TYPE)
    list(APPEND extra_cmake_args "-D CMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}")
  endif()

  if(DEFINED CMAKE_TOOLCHAIN_FILE)
    message(FATAL_ERROR "Not support CMAKE_TOOLCHAIN_FILE: ${CMAKE_TOOLCHAIN_FILE}")
  else()
    list(APPEND extra_cmake_args "-D CMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}")
  endif()

  include(ExternalProject)

  externalproject_add(ftxui_${ftxui_package_version}
    GIT_REPOSITORY https://github.com/ArthurSonzogni/FTXUI.git
    GIT_TAG v${ftxui_package_version}
    CMAKE_ARGS ${extra_cmake_args}
  )
  install(
    DIRECTORY ${ftxui_install_dir}/
    DESTINATION ${CMAKE_INSTALL_PREFIX}
    USE_SOURCE_PERMISSIONS
  )
endif()

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
