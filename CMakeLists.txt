cmake_minimum_required(VERSION 3.0)

# Project Name
project(RayLib)

# The additional dirs is going to be copied on build or install
set(ADDITIONAL_DIRS)

# Set the installation path
set(INSTALLATION_PATH ${PROJECT_SOURCE_DIR}/installation)

# Auto detect source files and header files
file(GLOB_RECURSE SOURCE_FILES ${PROJECT_SOURCE_DIR}/source/**.cpp)
file(GLOB_RECURSE HEADER_FILES ${PROJECT_SOURCE_DIR}/source/**.h)

# Set the C++ standard to 20
set(CMAKE_CXX_STANDARD 20)

# Get the path to raylib
set(RAYLIB_DIR ${PROJECT_SOURCE_DIR}/deps/raylib)

# Set the raylib directory to being built
add_subdirectory(${RAYLIB_DIR} EXCLUDE_FROM_ALL)

# Set the executable of the project
add_executable(${PROJECT_NAME} ${SOURCE_FILES} ${HEADER_FILES})

# Link with raylib
target_link_libraries(${PROJECT_NAME} PRIVATE raylib)

# Copy the files (after build) of the directories selected ass ADDITIONAL
foreach (dir ${ADDITIONAL_DIRS})
  add_custom_command(TARGET ${PROJECT_NAME}
    COMMAND ${CMAKE_COMMAND} -E make_directory ${PROJECT_BINARY_DIR}/${dir}
  )
  file(GLOB_RECURSE files RELATIVE_PATH ${PROJECT_SOURCE_DIR} ${PROJECT_SOURCE_DIR}/${dir}/*)
  foreach (file ${files})
     add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
       COMMAND ${CMAKE_COMMAND} -E copy ${file} ${PROJECT_BINARY_DIR}/${dir}
    )
  endforeach ()
endforeach ()

# Installation configuration
set(CMAKE_INSTALL_PREFIX ${INSTALLATION_PATH})

install(TARGETS ${PROJECT_NAME}
        RUNTIME DESTINATION .
        )

# Copy the directories in the installation path
foreach (dir ${ADDITIONAL_DIRS})
  install(DIRECTORY ${PROJECT_SOURCE_DIR}/${dir} DESTINATION ${INSTALLATION_PATH})
endforeach ()