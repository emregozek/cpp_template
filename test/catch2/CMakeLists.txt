find_package(Catch2 3 REQUIRED)

include(CTest)
include(Catch)

add_library(catch_main OBJECT catch2_main.cpp)
target_link_libraries(catch_main PUBLIC Catch2::Catch2WithMain)
target_link_libraries(catch_main PRIVATE project_options)

# Provide a simple smoke test to make sure that the CLI works and can display a --help message
add_test(NAME cli.has_help COMMAND intro --help)

# Provide a test to verify that the version being reported from the application
# matches the version given to CMake. This will be important once you package
# your program. Real world shows that this is the kind of simple mistake that is easy
# to make, but also easy to test for.
add_test(NAME cli.version_matches COMMAND intro --version)
set_tests_properties(cli.version_matches PROPERTIES PASS_REGULAR_EXPRESSION "${PROJECT_VERSION}")

foreach(child ${children_dirs})
    get_filename_component(module ${child} NAME)
    message("module: ${module}")

    file(GLOB source_files_${module} "${CMAKE_SOURCE_DIR}/src/${module}/*.cpp")
    if (NOT "${source_files_${module}}" STREQUAL "")

      add_library(${module}_lib SHARED ${source_files_${module}})

      file(GLOB test_files_${module} "${child}/catch2_test*.cpp")
      add_executable(catch2_test_${module} test_files_${module})
      target_link_libraries(catch2_test_${module} PRIVATE project_warnings project_options catch_main)
      catch_discover_tests(
        catch2_test_${module}
        TEST_PREFIX
        "catch2test."
        OUTPUT_DIR
        .
        OUTPUT_PREFIX
        "catch2test."
        OUTPUT_SUFFIX
        .xml)
    else()
        message("no source files discovered for ${module} module")
    endif()
endforeach()
