cmake_minimum_required(VERSION 3.15...3.22)

project(scikit_build_example VERSION "0.0.1")

if(SKBUILD)
  # Scikit-Build does not add your site-packages to the search path
  # automatically, so we need to add it _or_ the pybind11 specific directory
  # here.
  execute_process(
    COMMAND "${PYTHON_EXECUTABLE}" -c
            "import pybind11; print(pybind11.get_cmake_dir())"
    OUTPUT_VARIABLE _tmp_dir
    OUTPUT_STRIP_TRAILING_WHITESPACE COMMAND_ECHO STDOUT)
  list(APPEND CMAKE_PREFIX_PATH "${_tmp_dir}")
endif()

# Now we can find pybind11
find_package(pybind11 CONFIG REQUIRED)

SET(OpenGL_GL_PREFERENCE LEGACY)

find_package(OpenGL REQUIRED)
find_package(glfw3  REQUIRED)
find_package(GLEW   REQUIRED)
find_package(glm   REQUIRED)

message("GL include dir: ${OPENGL_INCLUDE_DIR}")
message("GL link libraries: ${OPENGL_gl_LIBRARY}")



pybind11_add_module(_core MODULE src/main.cpp)

target_compile_definitions(_core PRIVATE VERSION_INFO=${PROJECT_VERSION})
if(APPLE)
  target_link_libraries(_core PRIVATE ${OPENGL_gl_LIBRARY} GLEW::glew_s glfw )
else()
  target_link_libraries(_core PRIVATE ${OPENGL_gl_LIBRARY} GLEW glfw)
endif()


install(TARGETS _core DESTINATION .)

