cmake_minimum_required(VERSION 3.21)
project(protobuf3_vendor)

find_package(ament_cmake REQUIRED)

include(ExternalProject)

# set(VERSION 3.21.7)
# file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/include)

ExternalProject_Add(protobuf3_src
  GIT_REPOSITORY https://github.91chi.fun//https://github.com/tsungchent/protobuf.git
  GIT_TAG test
  # UPDATE_COMMAND "cd  ../protobuf3_src && git submodule update --recursive --init"
  CONFIGURE_COMMAND cd ../protobuf3_src-build && cmake ../protobuf3_src -Dprotobuf_BUILD_TESTS=OFF -Dprotobuf_MSVC_STATIC_RUNTIME=OFF -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/install -G Ninja -DCMAKE_BUILD_TYPE=Release
  BUILD_COMMAND cd ../protobuf3_src-build && cmake --build . --config Release
  INSTALL_COMMAND cd ../protobuf3_src-build && cmake --install .
)

add_library(${PROJECT_NAME} src/dependency_tracker.cc)
# add_dependencies(${PROJECT_NAME} protobuf3_src)

target_link_libraries(${PROJECT_NAME}
  ${CMAKE_CURRENT_BINARY_DIR}/install/lib/libprotobuf-lite${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CMAKE_CURRENT_BINARY_DIR}/install/lib/libprotoc${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CMAKE_CURRENT_BINARY_DIR}/install/lib/libprotobuf${CMAKE_SHARED_LIBRARY_SUFFIX}
)

install(TARGETS ${PROJECT_NAME} EXPORT export_${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)


install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/install/
  DESTINATION ${CMAKE_INSTALL_PREFIX}/
)

ament_package(CONFIG_EXTRAS "cmake/protobuf3-extras.cmake.in" "cmake/protobuf3-generate-cpp.cmake.in")
