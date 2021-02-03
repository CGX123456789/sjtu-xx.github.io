---
title: mac下的c++库安装
toc: true
date: 2019-11-25 18:51:00
tags: [c++,qt]
categories: 
- C++
- 基础
---

# 1.qt的安装
## 安装步骤
命令行执行
```bash
brew install qt
```
<!--more-->
记录执行结果
![执行结果截图](qt.png)

## 提示无法找到QtWidgets等时
在环境变量中添加`export CMAKE_PREFIX_PATH=/usr/local/opt/qt5/`,重启使得环境生效
## qt assistant中帮助文档的安装
qt assistant中的帮助文档发现不存在，所以进行安装
qt assistant中的帮助文档不能单独安装，需要下载所有的源码进行安装
1. 下载源码
`git clone git://code.qt.io/qt/qt5.git`
`git checkout v5.14.0`
`perl init-repository`
2. configure生成makefile
`cd qt5`
`qt5 `


# 2.vtk的安装
[ref](https://stackoverflow.com/questions/32853082/how-to-install-vtk-on-a-mac)
## 安装步骤
- 命令行执行`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
- 命令行执行`brew install vtk`


# 3.oce（OpenCASCADE）的安装
[ref](https://github.com/tpaviot/oce/wiki/Build-%28Mac-OSX%29)
命令行执行`brew tap brewsci/science && brew install oce`
执行结果
![执行结果](oce.png)

# 4.opencv的安装
命令行执行`brew install opencv`
执行结果
![执行结果](opencv.png)

# Clion+QT+VTK环+OpenCV环境搭建
> 注意：
> 在clion中编译时，不要使用gcc，将编译器设置为系统默认的clang。（由于brew install默认识别系统自带的编译器）
> Perferences->Toolchains->System(default) (如果之前修改过，新建一个设置为默认)

## clion中cmakelist配置
```cmake
cmake_minimum_required(VERSION 3.15)
project(test)

set(CMAKE_CXX_STANDARD 14)

set(Qt5_DIR /usr/local/opt/qt/lib/)
#set(OCE_DIR /usr/local/Cellar/oce/0.18.2/OCE.framework)
find_package(VTK REQUIRED)
find_package(Qt5Widgets REQUIRED)
find_package(Qt5Gui REQUIRED)
find_package(Qt5Core REQUIRED)
find_package(Qt5OpenGL REQUIRED)
#find_package(OCE REQUIRED COMPONENTS TKPrim)
#include_directories(${OCE_INCLUDE_DIRS})
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})
include(${VTK_USE_FILE})

add_executable(test main.cpp)
link_directories(${OpenCV_LIBRARY_DIRS})
target_link_libraries(${PROJECT_NAME} Qt5::Widgets Qt5::Widgets Qt5::Core Qt5::Gui Qt5::OpenGL ${VTK_LIBRARIES} ${OpenCV_LIBS})
```

**由于vtk,opencv等会自动调用qt，不对qt进行单独测试**
测试文件:（opencv）：
```c++
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <iostream>

using namespace cv;
using namespace std;

int main( int argc, char** argv )
{
    if( argc != 2)
    {
        cout <<" Usage: display_image ImageToLoadAndDisplay" << endl;
        return -1;
    }

    Mat image;
    image = imread(argv[1], 1);   // Read the file

    if(! image.data )                              // Check for invalid input
    {
        cout <<  "Could not open or find the image" << std::endl ;
        return -1;
    }

    namedWindow( "Display window", WINDOW_AUTOSIZE );// Create a window for display.
    imshow( "Display window", image );                   // Show our image inside it.

    waitKey(0);                                          // Wait for a keystroke in the window
    return 0;
}
```

测试文件（vtk）：
```c++
/*=========================================================================

  Program:   Visualization Toolkit
  Module:    Cube.cxx

  Copyright (c) Ken Martin, Will Schroeder, Bill Lorensen
  All rights reserved.
  See Copyright.txt or http://www.kitware.com/Copyright.htm for details.

     This software is distributed WITHOUT ANY WARRANTY; without even
     the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR
     PURPOSE.  See the above copyright notice for more information.

=========================================================================*/
// This example shows how to manually create vtkPolyData.

// For a python version, please see:
// [Cube](https://lorensen.github.io/VTKExamples/site/Python/GeometricObjects/Cube/)

#include <vtkActor.h>
#include <vtkCamera.h>
#include <vtkCellArray.h>
#include <vtkFloatArray.h>
#include <vtkNamedColors.h>
#include <vtkNew.h>
#include <vtkPointData.h>
#include <vtkPoints.h>
#include <vtkPolyData.h>
#include <vtkPolyDataMapper.h>
#include <vtkRenderWindow.h>
#include <vtkRenderWindowInteractor.h>
#include <vtkRenderer.h>

#include <array>

int main(int, char *[])
{
    vtkNew<vtkNamedColors> colors;

    std::array<std::array<double, 3>, 8> pts = {{{{0, 0, 0}},
                                                        {{1, 0, 0}},
                                                        {{1, 1, 0}},
                                                        {{0, 1, 0}},
                                                        {{0, 0, 1}},
                                                        {{1, 0, 1}},
                                                        {{1, 1, 1}},
                                                        {{0, 1, 1}}}};
    // The ordering of the corner points on each face.
    std::array<std::array<vtkIdType, 4>, 6> ordering = {{{{0, 1, 2, 3}},
                                                                {{4, 5, 6, 7}},
                                                                {{0, 1, 5, 4}},
                                                                {{1, 2, 6, 5}},
                                                                {{2, 3, 7, 6}},
                                                                {{3, 0, 4, 7}}}};

    // We'll create the building blocks of polydata including data attributes.
    vtkNew<vtkPolyData> cube;
    vtkNew<vtkPoints> points;
    vtkNew<vtkCellArray> polys;
    vtkNew<vtkFloatArray> scalars;

    // Load the point, cell, and data attributes.
    for (auto i = 0ul; i < pts.size(); ++i)
    {
        points->InsertPoint(i, pts[i].data());
        scalars->InsertTuple1(i, i);
    }
    for (auto&& i : ordering)
    {
        polys->InsertNextCell(vtkIdType(i.size()), i.data());
    }

    // We now assign the pieces to the vtkPolyData.
    cube->SetPoints(points);
    cube->SetPolys(polys);
    cube->GetPointData()->SetScalars(scalars);

    // Now we'll look at it.
    vtkNew<vtkPolyDataMapper> cubeMapper;
    cubeMapper->SetInputData(cube);
    cubeMapper->SetScalarRange(cube->GetScalarRange());
    vtkNew<vtkActor> cubeActor;
    cubeActor->SetMapper(cubeMapper);

    // The usual rendering stuff.
    vtkNew<vtkCamera> camera;
    camera->SetPosition(1, 1, 1);
    camera->SetFocalPoint(0, 0, 0);

    vtkNew<vtkRenderer> renderer;
    vtkNew<vtkRenderWindow> renWin;
    renWin->AddRenderer(renderer);

    vtkNew<vtkRenderWindowInteractor> iren;
    iren->SetRenderWindow(renWin);

    renderer->AddActor(cubeActor);
    renderer->SetActiveCamera(camera);
    renderer->ResetCamera();
    renderer->SetBackground(colors->GetColor3d("Cornsilk").GetData());

    renWin->SetSize(600, 600);

    // interact with data
    renWin->Render();
    iren->Start();

    return EXIT_SUCCESS;
}
```

测试文件（oce/opencascade）
```c++

```

# 卸载
通过cmake安装的软件要通过如下方式进行卸载：
`cat install_manifest.txt | sudo xargs rm`
