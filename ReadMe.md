# EMsoft SDK Superbuild #

## IMPORTANT NOTE ##

If you find bugs and fix them **PLEASE** consider submitting a "Pull Request" from your fork to the official repository. This will allow the entire EMsoft community to benefit from your bug fixes.

## Introduction ##

This cmake project will build an EMsoft SDK by downloading all the necessary 3rd party libraries as prebuilt binaries or as source code and compiling those libraries.  Note that this Develop branch builds the SDK for the regular EMsoft package (up to version 5.X), whereas the DevelopOO branch covers the SDK for the object oriented version 6 EMsoftOO of this package.

## Prerequisites ##

+ CMake 3.14.5 or later installed on system (preferably a recent 3.2x release)
+ Compiler Suite installed on system (on OS X, preferably installed via "brew install gcc")
+ Fortran compiler installed on system (OS X: brew install gcc will install gfortran as well)
+ Git installed on the system

## Supported Platforms ##

| Operating System | C/C++ Compiler | Fortran Compiler |
|------------------|----------------|------------------|
| macOS (>=10.13) | Xcode Native tools (10/11) | GFortran 6.3.0 or Intel Fortran 19\*\* |
| Windows (10/11) | Visual Studio 2022 Build Tools or Visual Studio 2022 | Intel oneAPI Fortran (`ifx` recommended, `ifort` supported if installed) |
| Linux (Ubuntu 16.x, CentOS 7.x) | GCC 7.x and Above, Clang 3.8 and greater | GNU Fortran 6.3.5 20160904 or newer |

\*\*macOS Note: If you are installing Intel Fortran try to install into a location **other** then /opt/intel which is the default. Try /opt/intel_sw instead.

## Git Locations ##

Git verison 2.x is required.

| Operating System |  Notes  |
|------------------|--------------|
| macOS (10.13) | CLI comes with Xcode, [SourceTree](http://www.sourcetreeapp.com) for a nice GUI application |
| Windows (10/11) | [SourceTree](http://www.sourcetreeapp.com). Download and install the app  |
| Linux (Ubuntu 16.x, CentOS 7.x) | Use your package manager to install git.|

## Libraries that are Compiled ##

| Library | Version | Notes |
|---------|---------|-------|
| HDF5 | 1.12.2 | Compiled from source; Windows `ifx`/`IntelLLVM` builds use a static-only HDF5 configuration |
| CLFortran | 0.0.1 | Compiled from source on GitHub; uses the CUDA/OpenCL SDK on Windows |
| FFTW | 3.3.8 | Compiled for GNU Fortran builds; Intel builds use the FFTW interface from oneMKL |
| Intel oneMKL | Installed oneAPI version | Used for Intel Fortran builds; detected via `setvars.bat`, `MKLROOT`, or `ONEAPI_ROOT` |
| Json-Fortran | 4.2.1 on Windows, 4.3.0 elsewhere | Compiled from source on GitHub |
| BCLS | 0.1 | Compiled from source |
| NLopt | 2.10.0 | Compiled from source |
| Eigen | 3.3.9 | Installed when `INSTALL_QT5=ON` |
| oneTBB | 2021.5.0 | Installed when `INSTALL_QT5=ON` |
| EbsdLib | 2.0 | Installed when `INSTALL_QT5=ON` |
| Qt 5 | 5.14.2 by default (`5.12.4` and `5.9.9` are also selectable) | Installed only when `INSTALL_QT5=ON`, or point CMake at an existing Qt installation |

## EMsoft versions ##
EMsoft 5.0.X requires a build of the regular develop branch in the EMsoftSuperbuild repository.  For the Object Oriented version, EMsoftOO, (which is effectively EMsoft 6.0)please use the developOO branch of the EMsoftSuperbuild repository.

## macOS/Linux Instructions ##

1. Install your compiler tools
2. Install CMake on your system
3. Install a Fortran compiler on your system
4. Install Git on your system
5. Clone this repository onto your hard disk.
6. open a terminal and invoke the following commands

        cd EMsoftSuperbuild
        mkdir Debug
        cd Debug
        cmake -DEMsoft_SDK=/Some/Path/To/EMsoft_SDK -DCMAKE_BUILD_TYPE=Debug ../
        make -j
        cd ../
        mkdir Release
        cd Release
        cmake -DEMsoft_SDK=/Some/Path/To/EMsoft_SDK -DCMAKE_BUILD_TYPE=Release ../
        make -j


Note that the Superbuild has been successfully installed on the arm64 M1 platform using Xcode 13 on Monterey, Xcode 14 on Ventura, and Xcode 15 on arm64 M3 Pro with Sonoma.

## Windows Instructions ##

*The current Windows path uses `NMake Makefiles` from a VS 2022 developer prompt together with Intel oneAPI initialized via `setvars.bat`.*

1. Install Visual Studio 2022 Build Tools (or full Visual Studio 2022) with the Desktop development with C++ workload
2. Install CMake on your system
3. Install Intel oneAPI Base Toolkit and Intel oneAPI HPC Toolkit on your system
4. Install the NVIDIA CUDA Toolkit (version 10.0 or newer) on your system
5. Install Git on your system
6. Create the following Directories:
    + C:/Users/[USERNAME]/EMsoft_SDK
    + C:/Users/[USERNAME]/EMsoft-Dev
7. Use a `Release` folder inside `EMsoftSuperbuild` as the build directory.

8. Open an **x64 Native Tools Command Prompt for VS 2022**, initialize oneAPI, and invoke the following commands

        "C:\Program Files (x86)\Intel\oneAPI\setvars.bat" intel64 vs2022
        cd C:/Users/[USERNAME]/EMsoft-Dev
        git clone https://github.com/emsoft-org/EMsoftSuperbuild
        cd EMsoftSuperbuild
        mkdir Release
        cd Release
        cmake -G "NMake Makefiles" -DEMsoft_SDK=C:/Users/[USERNAME]/EMsoft_SDK -DCMAKE_BUILD_TYPE=Release -DCMAKE_Fortran_COMPILER=ifx ../
        cmake --build .

9. If the `Release` folder already exists, `mkdir Release` can be skipped.

10. If CUDA is installed in a non-default location, add `-DNVIDIA_CUDA_DIR=/path/to/NVIDIA GPU Computing Toolkit` to the configure command.

11. If you want to pin a specific CUDA version instead of using the newest installed version detected by the build, add `-DNVIDIA_CUDA_DEV_VERSION=13.0` (or the version you installed).

12. `setvars.bat` is the preferred way to expose Intel oneAPI to the build. The Windows configuration will look for oneMKL through `MKLROOT` and `ONEAPI_ROOT` before falling back to compiler-relative install paths.

13. The initial run of CMake can take a long time because the superbuild downloads and builds third-party dependencies. If you enable the optional Qt path, the download is much larger.

**NOTE**: there is currently a known issue where the Qt 5.x installer will NOT actually run during the Linux cmake process. If the user does *NOT* want to build the GUI application then this is fine. If the user does want to build the GUI application then they will need to install Qt 5.x themselves. The download will be at /path/to/EMsoft_SDK/superbuild/Qt/download

The developer can use CMakeGui if they would like instead of the command lines. The required values are the path to the `EMsoft_SDK` folder, the build type, and on Windows the Fortran compiler (`ifx` or `ifort`).

Once the SDK builds correctly, no errors are reported on the command line, then the developer can proceed to clone and build EMsoft itself.
