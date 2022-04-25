include(FetchContent)
FetchContent_Declare(googletest
                     URL https://github.com/google/googletest/archive/609281088cfefc76f9d0ce82e1ff6c30cc3591e5.zip)

set(gtest_force_shared_crt
    ON
    CACHE BOOL "" FORCE)

FetchContent_MakeAvailable(googletest)

include(${PROJECT_SOURCE_DIR}/cmake.macros)
subdirlist(children_dirs ${CMAKE_CURRENT_SOURCE_DIR})

include(GoogleTest)

foreach(child ${children_dirs})
    get_filename_component(module ${child} NAME)
    message("module: ${module}")
    file(GLOB source_files_${module} "${CMAKE_SOURCE_DIR}/src/${module}/*.cpp")
    if (NOT "${source_files_${module}}" STREQUAL "")
      add_library(${module}_lib SHARED ${source_files_${module}})
      file(GLOB test_files_${module} "${child}/*.cpp")
      message("test_files_${module} " ${test_files_${module}})
      add_executable(gtest_${module} ${test_files_${module}})
      target_link_libraries(
        gtest_${module}
        ${module}_lib
        project_warnings
        project_options
        gtest_main)
        message("test discovery")
        gtest_discover_tests(gtest_${module} DISCOVERY_MODE PRE_TEST)
    else()
        message("no source files discovered for ${module} module")
    endif()
endforeach()


