cmake_minimum_required(VERSION 3.14)

project(DWG_CUDA CXX CUDA)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set( CMAKE_CUDA_STANDARD 17 )
set( CMAKE_CUDA_STANDARD_REQUIRED ON)

### Compiler options
set( CMAKE_EXPORT_COMPILE_COMMANDS 1 ) # Emit a compile flags file to support completion engines 

set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -O3 --use_fast_math")
set(CMAKE_CUDA_ARCHITECTURES OFF)

### Policy settings
cmake_policy(SET CMP0002 NEW)   # don't implicitly dereference inside if()

## Extra cmake module search paths
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

find_package(CGAL COMPONENTS Core)
find_package(PCL REQUIRED COMPONENTS common)
find_package(Eigen3 REQUIRED)
find_package(CUDAToolkit REQUIRED)

### libIGL options: choose your dependencies (by default everything is OFF, in this example we need the viewer) ###
find_package(LIBIGL REQUIRED)
option(LIBIGL_WITH_CGAL             "Use CGAL"           "${CGAL_FOUND}")
option(LIBIGL_WITH_COMISO           "Use CoMiso"         OFF)
option(LIBIGL_WITH_CORK             "Use CORK"           OFF)
option(LIBIGL_WITH_EMBREE           "Use Embree"         OFF)
option(LIBIGL_WITH_LIM              "Use LIM"            OFF)
#find_package(MATLAB QUIET)
option(LIBIGL_WITH_MATLAB           "Use Matlab"         OFF)
option(LIBIGL_WITH_MOSEK            "Use MOSEK"          "${MOSEK_FOUND}")
option(LIBIGL_WITH_OPENGL           "Use OpenGL"         OFF)
option(LIBIGL_WITH_OPENGL_GLFW      "Use OpenGL GLFW"    OFF)
option(LIBIGL_WITH_OPENGL_GLFW_IMGUI "Use OpenGL_GLFW_IMGUI" OFF)
option(LIBIGL_WITH_PNG              "Use PNG"            OFF)
option(LIBIGL_WITH_TETGEN           "Use Tetgen"         OFF)
option(LIBIGL_WITH_TRIANGLE         "Use Triangle"       OFF)
option(LIBIGL_WITH_XML              "Use XML"            OFF)
### End   to be tested ----

# add openMP
find_package(OpenMP REQUIRED)
if(OpenMP_FOUND)
    message(STATUS "found openmp") 
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
else()
    message(FATAL_ERROR "openmp not found!")
endif()

# Add your project files
file(GLOB SRCFILES 
	src/*.cpp
	src/*.cu
    "include/*.h"
    "src/*.cu"
)

add_executable(${PROJECT_NAME} ${SRCFILES} "include/utils.h")
target_link_libraries(${PROJECT_NAME} CUDA::cudart CUDA::cuda_driver)
target_link_libraries(${PROJECT_NAME} igl::core )
# target_link_libraries(${PROJECT_NAME} igl::core igl::cgal )
target_link_libraries(${PROJECT_NAME} Eigen3::Eigen)
target_link_libraries(${PROJECT_NAME} ${PCL_LIBRARIES})
target_include_directories(${PROJECT_NAME} PRIVATE ${PCL_INCLUDE_DIRS})

## Everyone says nvcc inherits the include paths from CMake properly, but it's not working for me.
set(CMAKE_CUDA_FLAGS "-I$ENV{CONDA_PREFIX}/include/eigen3")

 target_include_directories(${PROJECT_NAME} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include/cudaKDTree")
 target_include_directories(${PROJECT_NAME} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include/")
  target_include_directories(${PROJECT_NAME} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include/geometrycentral")

### libIGL options: decide if you want to use the functionalities that depends on cgal
#[[
if(LIBIGL_WITH_CGAL) # Do not remove or move this block, cgal strange build system fails without it
set (CGAL_DIR /root/autodl-tmp/CGAL-5.5.1)
  find_package(CGAL REQUIRED COMPONENTS Core)
  set(CGAL_DONT_OVERRIDE_CMAKE_FLAGS TRUE CACHE BOOL "CGAL's CMAKE Setup is super annoying ")
  #include(${CGAL_USE_FILE})
  target_link_libraries(${PROJECT_NAME} CGAL::CGAL)
endif()
]]
