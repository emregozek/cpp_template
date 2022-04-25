# Generic test that uses conan libs
file(GLOB source_files_intro "*.cpp")
add_executable(intro ${source_files_intro})
target_link_libraries(intro PRIVATE project_options project_warnings)

target_include_directories(intro PRIVATE "${CMAKE_BINARY_DIR}/configured_files/include")
