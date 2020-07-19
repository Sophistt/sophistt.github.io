---
title: Ros Qt Install and Configuration on Ubuntu
date: 2019-05-23 14:14:30
tags: ROS
categories: Robot
---

# Download and Install

- Dependencies:
  Qt depends on C/C++. Meanwhile, Qt's 3D interface depends on OpenGL lib. Therefore, **gcc/g++ complier** and **OpenGL lib** are required. Open a terminal and enter the following commands so that these dependencies will be installed.
  ```bash
  $ sudo apt-get install mesa-common-dev
  $ sudo apt-get install ligblul-mesa-dev -y
  ```

- Download:
  Download the `.run` file in your required version.
  - [Official download link](https://www.qt.io/download)
  - [Mirror download link](http://download.qt.io/archive/qt/)

- Install:
  Go into the download folder, open a terminal and enter the following command.
  After that, a GUi Setup interface will be opened. Choose your expected path and just click *next*, Qt5 will be installed automatically.
  ```bash
  $ chmod +x qt-opensource-linux-x64-5.x.x.run
  $ ./qt-opensource-linux-x64-5.x.x.run
  ```

# Configure Environment Path
 
  Add Qt5 path to environment path so that we can use it to complie our projects.

1. open ~/.bashrc in the terminal and add the following commands.
  ```bash
  export QTDIR=/install/path/QT5/5.x.x/gcc64$QTDIR
  export PATH=$QTDIR/bin:$PATH
  export LD_LIBRARY_PATH=$QTDIR/lib:$LD_LIBRARY_PATH
  export PATH=/install/path/QT5/Tools/QtCreator/bin:$PATH
 ```

2. Open **qtcreater** by terminal each time.

# Configure ROS Workspace

- Install **qt-ros-plugin**:
  [Download link](https://ros-qtc-plugin.readthedocs.io/en/latest/_source/How-to-Install-Users.html): Download the Xenial Offline Installer and execute it to open the GUI Setup interface.

- Execute **qt-ros-plugin**:
  open a terminal and enter the following command.
  ```bash
  $ qtcreator-ros
  ```

- Create a ROS node with GUI
  - Create Workspace
    Open *qtcreator-ros* and click **New Project**
    <img src="https://i.imgur.com/IuEmubt.png" title="Create Workspace" width="438" height="273" /><br/>
    Choose Workspace name and its path.<br/>
    <img src="https://i.imgur.com/UN4PSXO.png" title="source: imgur.com" width="438" height="273" /><br/>
    Finally, we will get this file structure.<br/>
    <a href="https://imgur.com/BSR8sSx"><img src="https://i.imgur.com/BSR8sSx.png" title="source: imgur.com" /></a>
  - Create Package
    - Qt4
      Use *qt_ros* tool, go into the **src** folder and enter the following commands in terminal to install and create qt package.
      After execute these commands, a package will be created and its CMakelist.txt contains commands that use Qt4.
      ```bash
      $ sudo apt-get install ros-kinetic-qt-ros
      $ catkin_create_qt_pkg
      ```
    - Qt5
      - Use *qt_ros* tool to create package as Qt4, and then change its CMakelist.txt
      - Modify **CMakelist.txt**
      Change the CMakelist.txt contents as followed.
        ```cmake
        CMAKE_MINIMUM_REQUIRED(VERSION 2.8.11)
        PROJECT(test)

        SET(CMAKE_INCLUDE_CURRENT_DIR ON)

        ## Compile as C++11, supported in ROS Kinetic and newer
        add_compile_options(-std=c++11)

        ## Find catkin macros and libraries
        find_package(catkin REQUIRED COMPONENTS
        roscpp
        )

        ###################################
        ## catkin specific configuration ##
        ###################################
        catkin_package(
        #  INCLUDE_DIRS include
        #  LIBRARIES gacui
        #  CATKIN_DEPENDS roscpp message_runtime
        #  DEPENDS system_lib
        )

        ##############################################################################
        # Qt Environment
        ##############################################################################
        find_package(Qt5 REQUIRED COMPONENTS
            Widgets
            WebEngineWidgets
            )

        ##############################################################################
        # Sections and Resources
        ##############################################################################

        # Rename Path
        file(GLOB QT_FORMS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ui/*.ui)
        file(GLOB QT_RESOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} resources/*.qrc)
        file(GLOB_RECURSE QT_MOC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS include/gacui/*.h)

        QT5_ADD_RESOURCES(QT_RESOURCES_CPP ${QT_RESOURCES})
        QT5_WRAP_UI(QT_FORMS_HPP ${QT_FORMS})
        QT5_WRAP_CPP(QT_MOC_HPP ${QT_MOC})


        ##############################################################################
        # Sources
        ##############################################################################

        file(GLOB_RECURSE QT_SOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS src/*.cpp)

        ###########
        ## Build ##
        ###########

        ## Specify additional locations of header files
        INCLUDE_DIRECTORIES(
        # include
        ${catkin_INCLUDE_DIRS}
        )

        ## Add cmake target dependencies of the library
        # add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

        ## Declare a C++ executable
        ADD_EXECUTABLE(${PROJECT_NAME}_node ${QT_SOURCES} ${QT_RESOURCES_CPP} ${QT_FORMS_HPP} ${QT_MOC_HPP})
        ADD_DEPENDENCIES(${PROJECT_NAME}_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
        TARGET_LINK_LIBRARIES(${PROJECT_NAME}_node
        ${catkin_LIBRARIES}
        )

        qt5_use_modules(${PROJECT_NAME}_node Widgets WebEngine WebEngineWidgets)

        ```
  - Compile package
    Open a terminal in root folder of workspace and then execute the following command.
    ```bash
    $ catkin_make
    ```

# Install Qt on PX2 or TX2 Platform

Because PX2 or TX2 use different CPU framework(Arm64) from PC(i386), another install package is necessary to install on these platforms. **levi-armstrong** has provided a useful ppa repository for Ubuntu on Arm64 framework, so that we can use this repository to install Qt conveniently.

Use the following commands to install:
```bash
sudo add-apt-repository ppa:levi-armstrong/qt-libraries-xenial
sudo add-apt-repository ppa:levi-armstrong/ppa
sudo apt-get update
sudo apt install qt59creator
# install qt-creator-plugin-ros(optional)
sudo apt-get install qt57creator-plugin-ros
```
After installing Qt by the previous commands, a folder named `qt59` can be found at `/opt/`. In order to use it, it's necessary to add this directory to **ENV PATH**.
```bash
export PATH=/opt/qt59/bin:$PATH
export LD_LIBRARY_PATH=/opt/qt59/lib:$LD_LIBRARY_PATH
```

# References
- [Ubuntu下ROS开发环境搭建(QT+ros_qtc_plugin)](https://www.cnblogs.com/zengcv/p/6378799.html)   
- [ros-qtc-plugin](https://github.com/ros-industrial/ros_qtc_plugin)
- [如何用Qt对ROS项目进行调试及创建GUI界面](https://blog.csdn.net/u013453604/article/details/52167213)