## find_package()
1. 用来调用预定义在CMAKE_MODULE_PATH下的Find<name>.cmake模块，cmake会查找Find<name>.cmake文件并执行，找到相应的源文件库文件等
（CMAKE_MOUDLE_PATH可以通过LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)来设置）
2. **REQUIRED参数**：这个包是否是该project必需的。如果包没有找到的话，cmake的过程会终止，并输出警告信息
3. 查找路径：分为模块模式和配置模式。
   - 先用模块模式：在模块路径中寻找 Find<name>.cmake ，这是查找库的一个典型方式。首先CMake查看${CMAKE_MODULE_PATH} 中的所有目录;没有的话再查看它自己的模块目录 <CMAKE_ROOT>/Modules/
（可以在terminal里列出自带的模块cmake --help-module-list）
   - 还没找到的话会进入配置模式：会在~/.cmake/packages/或/usr/local/share/或PATH等中的各个包目录中查找，寻找<库名字的大写>Config.cmake 或者 <库名字的小写>-config.cmake (比如库Opencv，它会查找/usr/local/share/OpenCV中的OpenCVConfig.cmake或opencv-config.cmake)

- If you want to use ${EIGEN3_INCLUDE_DIR}, you have to find_package first!!
    ```cmake
    Find_Package(Eigen3 REQUIRED)
    include_directories(${EIGEN3_INCLUDE_DIR})
    ```
- you can use include_directories("/usr/include/eigen3") instead

eg:
```cmake
add_library(BALProblem SHARED ${PROJECT_SOURCE_DIR}/common/BALProblem.cpp)
add_library(ParseCmd SHARED ${PROJECT_SOURCE_DIR}/common/flags/command_args.cpp)

message("cmake_module_path:" ${CMAKE_MODULE_PATH})
message("cmake_root:" ${CMAKE_ROOT})
LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)

Find_Package(G2O REQUIRED)
message("find g2o libraries in: " ${G2O_INCLUDE_DIR})
message("find g2o lib in " ${G2O_LIBS})

find_package(OpenCV REQUIRED)
message("find opencv lib in: " ${OpenCV_LIBS})
message("find opencv libraries in: " ${OpenCV_INCLUDE_DIRS})
message("cmake_module_path:" ${CMAKE_MODULE_PATH})
```