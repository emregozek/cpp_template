include(${PROJECT_SOURCE_DIR}/cmake.macros)
subdirlist(children_dirs ${CMAKE_CURRENT_SOURCE_DIR})

foreach(child ${children_dirs})
  get_filename_component(module ${child} NAME)
  message("Processing module: ${module}")
  file(GLOB source_files_exec_${module} "${CMAKE_CURRENT_SOURCE_DIR}/${module}/*.cpp")
  if(NOT
     "${source_files_exec_${module}}"
     STREQUAL
     "")
    add_executable(${module}_exec ${source_files_exec_${module}})
    target_link_libraries(${module}_exec project_warnings project_options)
  else()
    message("No source files have been discovered for ${module} module")
  endif()
endforeach()
