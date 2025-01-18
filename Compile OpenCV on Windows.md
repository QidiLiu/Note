#done

Prerequisite:
- Windows 11
- Visual Studio 2022
- CMake 3.31.3
- Internet outside the Chinese mainland

Reference:
- https://docs.opencv.org/4.x/d3/d52/tutorial_windows_install.html
- https://zhuanlan.zhihu.com/p/470087053
- https://blog.csdn.net/qq_29183811/article/details/126773354

Related: [[Compile OpenCV on Ubuntu (立创开发板 · 泰山派)]]
## Compile OpenCV step by step

Download source of [opencv-4.11.0](https://github.com/opencv/opencv/archive/refs/tags/4.11.0.zip) and [opencv_contrib-4.11.0](https://github.com/opencv/opencv_contrib/archive/refs/tags/4.11.0.zip) and unzip them `<somewhere>`.

Open CMake, configure the "Where is the source code": `<somewhere>/opencv-4.11.0`

And configure the "Where to build the binaries": `<somewhere>/opencv-4.11.0/build`

Press "Configure", if CMake asks if the "build" directory should be created, click "Yes".

Then the CMake would ask for compiler configuration, select "Visual Studio 17 2022" and "Use default native compilers", click "Finish".

Wait until the red items shows up.

Configure as follows:
- Search "BUILD_opencv_world", check the box
- If you want to use the "opencv_contrib" search "OPENCV_EXTRA_MODULES_PATH", enter: `<somewhere>/opencv_contrib-4.11.0/modules`, and search "OPENCV_ENABLE_NONFREE", check the box
- Search "tests", uncheck the boxes:
	- BUILD_PERF_TESTS
	- BUILD_TESTS
	- BUILD_opencv_python_tests
- Search "java", uncheck the boxes:
	- BUILD_JAVA
	- BUILD_opencv_java_bindings_generator
- Search "python", uncheck the boxes:
	- BUILD_opencv_python3
	- BUILD_opencv_python_bindings_generator

Click "Configure" repeatly, until there're no red items left.

Click "Generate".

Click "Open Project", the Visual Studio would be opened. Now you can exit the CMake.

From the menu bar of Visual Studio, choose "Build" > "Batch build", check the boxes:
- ALL_BUILD (Debug, x64)
- ALL_BUILD (Release, x64)
- INSTALL (Debug, x64)
- INSTALL (Release, x64)

Click "Build", the Visual Studio would compile and install OpenCV, this process takes 0.5~3 hours.

If everything goes well, the OpenCV would be installed in: `<somewhere>/opencv-4.11.0/build/install`

## Test

See [How to build applications with OpenCV inside the "Microsoft Visual Studio"](https://docs.opencv.org/4.x/dd/d6e/tutorial_windows_visual_studio_opencv.html)