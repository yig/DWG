# Diffusing Winding Gradients (DWG): A Parallel and Scalable Method for 3D Reconstruction from Unoriented Point Clouds
## Building
Please install [CMake](https://cmake.org) 
### Dependencies
  * CGAL-5.6.1
  * PCL
  * Eigen
  * CUDA (We tested on CUDA 11.1 and 11.3)

### Conda
You can install dependencies with conda (and even compile):
```
conda create -n DWG
conda activate DWG
conda install conda-forge::mamba
## Maybe skip the last three packages: `conda-forge::cuda conda-forge::llvm-openmp conda-forge::clangxx`
## The system cuda should be used to match the installed GPU driver.
## On Windows, try the default compiler rather than llvm-openmp and clangxx.
mamba install conda-forge::cgal "conda-forge::pcl>1.11" conda-forge::eigen conda-forge::gmp conda-forge::cmake conda-forge::cuda conda-forge::llvm-openmp conda-forge::clangxx
cmake -B build-clang -D CMAKE_BUILD_TYPE=Release -D CMAKE_POLICY_VERSION_MINIMUM=3.5 -D CMAKE_C_COMPILER=clang -D CMAKE_CXX_COMPILER=clang++
## If using the system compiler: cmake -B build-clang -D CMAKE_BUILD_TYPE=Release -D CMAKE_POLICY_VERSION_MINIMUM=3.5
cmake --build build-clang
```

### Windows 10
    mkdir build && cd build
    cmake .. -G"Visual Studio 16 2019"
    cmake --build . --config Release
### Linux
    mkdir build && cd build
    cmake .. -DCMAKE_BUILD_TYPE=Release
    make

Thanks to [libigl](https://libigl.github.io/), [Geometry Central](https://geometry-central.net/), [cudaKDTree](https://github.com/ingowald/cudaKDTree), [marching-cubes-with-CUDA](https://github.com/lcaucci78/marching-cubes-with-CUDA), these libraries provide us with big assistance.
## Run
    ./DWG_CUDA --in_path <path> --in_name <file name> --out_path <path>
See --help for the function of each flag  `./DWG_CUDA --help`
    
## TODO
  * Provide a more detailed explanation of the parameters.
  * The results of small-scale models on Linux are a bit rougher compared to those on Windows. We will identify the problems as soon as possible.
  * Clean the code.

