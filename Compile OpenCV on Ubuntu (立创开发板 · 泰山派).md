#done 

Prerequisite:
- Ubuntu 20.04.6 LTS (Notice: the processor of 泰山派 "RK3566" is **ARM architecture**)

Reference:
- https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html
- https://blog.csdn.net/tiaoteek/article/details/138621636

Related: [[Compile OpenCV on Windows]]
## Compile OpenCV step by step

Install essential packages.
```bash
sudo apt update
sudo apt upgrade
sudo apt install -y wget build-essential cmake unzip git
sudo apt install -y libgtk-3-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libxvidcore-dev libx264-dev openexr libatlas-base-dev libopenexr-dev libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-dev gfortran
```

Download source of [opencv-4.11.0](https://github.com/opencv/opencv/archive/refs/tags/4.11.0.zip) and [opencv_contrib-4.11.0](https://github.com/opencv/opencv_contrib/archive/refs/tags/4.11.0.zip) and unzip them `<somewhere>`.
```bash
cd <somewhere>
wget https://github.com/opencv/opencv/archive/refs/tags/4.11.0.zip
unzip 4.11.0.zip
rm 4.11.0.zip
wget https://github.com/opencv/opencv_contrib/archive/refs/tags/4.11.0.zip
unzip 4.11.0.zip
rm 4.11.0.zip
```

Create build and install dir in opencv source dir.
```bash
cd opencv-4.11.0 && mkdir build && cd build && mkdir install
```

Configure
```bash
cmake -D CMAKE_INSTALL_PREFIX=./install -D OPENCV_GENERATE_PKGCONFIG=ON -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.11.0/modules -D OPENCV_ENABLE_NONFREE=ON -D BUILD_PERF_TESTS=OFF -D BUILD_TESTS=OFF -D BUILD_opencv_python_tests=OFF -D BUILD_JAVA=OFF -D BUILD_opencv_java_bindings_generator=OFF -D BUILD_opencv_python3=OFF -D BUILD_opencv_python_bindings_generator=OFF ..
```

Build and install
```bash
sudo cmake --build .
sudo make install
```

## Test

Set up the project directory.
```bash
mkdir opencv_test cd opencv_test mkdir src
vim src/main.cpp
```

Write the source code.
```cpp
#include <opencv2/opencv.hpp>
#include <iostream>

int main(int argc, char** argv) {
    // Check if an image path is provided
    if (argc != 2) {
        std::cout << "Usage: " << argv[0] << " <path_to_image>" << std::endl;
        return -1;
    }

    // Read the image file
    cv::Mat image = cv::imread(argv[1], cv::IMREAD_COLOR);

    // Check for invalid input
    if(image.empty()) {
        std::cout << "Could not open or find the image" << std::endl;
        return -1;
    }

    // Display the image in a window
    cv::namedWindow("Display window", cv::WINDOW_AUTOSIZE);
    cv::imshow("Display window", image);

    // Wait for a keystroke in the window
    cv::waitKey(0);

    return 0;
}
```

Create CMakeLists.txt `vim CMakeLists.txt`
```cmake
cmake_minimum_required(VERSION 3.10)
project(OpenCVTest)

set(OpenCV_DIR ~/Lib/opencv-4.11.0/build/install/lib/cmake/opencv4)
find_package(OpenCV REQUIRED)

add_executable(opencv_test src/main.cpp)
	target_include_directories(${MAIN} PRIVATE ${OpenCV_INCLUDE_DIRS})
	target_link_libraries(opencv_test ${OpenCV_LIBS})
```

Build and run the test program.
```bash
mkdir build
cd build
cmake ..
cmake --build .
./opencv_test
```
