# Hello Window

注意
如果你想尝试在不下载完整源码的情况下跟者这个系列，你可以使用这个空项目作为起点，它已经配置好了全部的包含路径与库，这些配置将适用于整个系列的学习

完整源码的链接

让我们先从创建简单窗口的完整示例代码开始。我们使用GLFW库创建我们的窗口，如你所见，代码并不多。下面的代码会打开一个新空白窗口，标题为`“LearnD3D11 - Hello Window”`并会一直保持打开状态，直到你点击关闭。
```c++
#include <GLFW/glfw3.h> 
#include <cstdint> 
#include <iostream> 
int main(int argc, char* argv[]) 
{ 
	if (!glfwInit()) 
	{ 
		std::cout << "GLFW: Unable to initialize\n"; return -1; 
	} 
	GLFWmonitor* primaryMonitor = glfwGetPrimaryMonitor(); 
	const GLFWvidmode* videoMode = glfwGetVideoMode(primaryMonitor); 
	const int32_t width = static_cast<int32_t>(videoMode->width * 0.9f); 
	const int32_t height = static_cast<int32_t>(videoMode->height * 0.9f); 
	glfwWindowHint(GLFW_SCALE_TO_MONITOR, GLFW_FALSE); 
	glfwWindowHint(GLFW_CLIENT_API, GLFW_NO_API); GLFWwindow* window = 
	glfwCreateWindow( width, height, "LearnD3D11 - Hello Window", nullptr, nullptr); 
	if (window == nullptr) 
	{ 
		std::cout << "GLFW: Unable to create window\n"; 
		glfwTerminate(); 
		return -1; 
	} 
	const int32_t windowLeft = videoMode->width / 2 - width / 2; 
	const int32_t windowTop = videoMode->height / 2 - height / 2; 
	glfwSetWindowPos(window, windowLeft, windowTop); 
	while (!glfwWindowShouldClose(window)) 
	{ 
		glfwPollEvents(); 
		// future update code 
		// future render code 
	} 
	glfwDestroyWindow(window); 
	glfwTerminate(); 
	return 0; 
}
```

现在，让我们仔细分析这个例子。

```c++
#include <GLFW/glfw3.h>
```
C++需要知道所有的定义与声明来自哪里，因此，我们必须包含GLFW的头文件，以确保我们所需的所有内容都在我们的源文件中

```c++
if (!glfwInit()) 
{ 
	std::cout << "GLFW: Unable to initialize\n"; 
	return -1; 
}
```
很显然，不是吗？`glfwInit`会尝试初始化`GLFW`。如果初始化失败，此时，通知用户并结束程序，因为程序再继续执行下去也没有意义。

```c++
GLFWmonitor* primaryMonitor = glfwGetPrimaryMonitor(); 
const GLFWvidmode* videoMode = glfwGetVideoMode(primaryMonitor); 
const int32_t width = static_cast<int32_t>(videoMode->width * 0.9f); const int32_t height = static_cast<int32_t>(videoMode->height * 0.9f);
```
这一部分的代码通过glfwGetPrimaryMonitor函数获取主屏幕，glfwGetVideoMode函数获取当前分辨率，从而我们可以从中计算出窗口的宽度和高度-不论你屏幕的分辨率是多少，窗口的外观都会保持一致，尺寸上，它将覆盖你90%的屏幕。

```c++
glfwWindowHint(GLFW_SCALE_TO_MONITOR, GLFW_FALSE);
```
这将告诉GLFW窗体不可缩放，如果你已经在桌面上设置了除100%外的特定缩放比例。这将保持我们设置的窗口大小，这样可以保持我们设定的窗口大小，使我们无需再考虑部分窗口和像素的缩放问题。

```c++
glfwWindowHint(GLFW_CLIENT_API, GLFW_NO_API);
```
GLFW最初是被用作开发基于OpenGL的应用程序，因此它名字中包含gl，但是近年来它也开始支持其他API而不只是OpenGL。GLFW默认会创建OpenGL上下文，如果我们想使用DirectX我们需要使用glfwWindowHint函数告诉GLFW不这么做。

glfwWindowHint函数可以设定很多其他选项，可以在这里查看详情，这里的很多选项对你的应用程序来说可能是非常有用的，具体取决于你的需求。

```c++
GLFWwindow* window = glfwCreateWindow(
    width,
    height,
    "LearnD3D11 - Hello Window",
    nullptr,
    nullptr);
if (window == nullptr)
{
    std::cout << "GLFW: Unable to create window\n";
    glfwTerminate();
    return -1;
}
```
这段代码实际创建窗体，如果一切都没问题，我们调用glfwCreateWindow函数给窗体传入大小和标题，确保检查返回值，窗口创建可能失败。

```c++
const int32_t windowLeft = videoMode->width / 2 - width / 2;
const int32_t windowTop = videoMode->height / 2 - height / 2;
glfwSetWindowPos(window, windowLeft, windowTop);
```
GLFW不会像其他库（如SDL）那样自动居中窗口，因此我们要手动将窗体居中。我们只需要一些数学计算与glfwSetWindowPos函数。它设置窗体在屏幕上的位置坐标，窗体的左上角为基准点

```c++
while (!glfwWindowShouldClose(window))
{
    glfwPollEvents();
    // future update code
    // future render code
}
```
这里在某种程度上来说是你的应用程序的核心，主循环。你也可以称之为游戏循环，因为在这里发生一切。渲染，键盘和鼠标输入，对此做出回应，告诉显卡在场景里绘制一只青蛙，它就会一直做这件事，直到它获得不需要处理任何事物的信号，因为你关闭窗体，例如（glfwWindowShouldClose）或者点击ESC键和映射了ESC去关闭窗体。glfwPollEvents 将会确保GLFW知道关于一切需要的从操作系统来的事件

```c++
glfwDestroyWindow(window);
glfwTerminate();
return 0;
```
现在我们清理我们创建的资源，像是窗体和GLFW系统。然后返回给操作系统，没有任何问题

glfwDestroyWindow函数将会显示的销毁窗口，同时，glfwTerminate函数清理GLFW

当你运行程序，你应该可以看到这样

![](https://graphicsprogramming.github.io/learnd3d11/1-introduction/images/1-1-1-hello-window-1.png)

为什么是GLFW？
我们决定包含GLFW来管理我们的窗体（以及后面的输入）因为这是非常简单的库，可以直接使用，更重要的是因为一致性，有很多大量使用GLFW的资源，因此这应该是更容易的转变你从别处获取的知识。

这里有其他库，处理窗体与输入（还有其他功能）输出，这里有一些选择：
* SDL2
* SFML

你也可以使用操作系统提供的原生函数与构造，在windows上就是WinAPI，尽管那玩意老掉牙而且你要不需要将所有事都自己来干。但由于很多微软自己的指南依赖于WinAPI，因此我们在这里提供了一个例子。除非明确需要，否则我们将不会包含任何有关Win32的主题。

你可能会在阅读完这篇教程后去编写自己的引擎，要支持多种平台，而不只是windows，因此这里以及包含了GLFW。

## 第一次抽象

在未来的教程系列中我们将会添加更多的程序。但是我们不希望一股脑的把一切放到Main.cpp中，即你的程序主入口点中。一个好的练习就是把程序分开进小的模块中，不要失去概述。

现在，我们还没有太多的代码展示，只有一个窗口，有几行代码构成，但是我们把这些代码抽象成一个新的类叫做Application，也可以说我们的主容器，在这里所有的魔法将会发生。

在开始的时候，我将展示全部的代码，并再次解释是什么意思。

### Application.hpp
```c++
#pragma once

#include <string>
#include <iostream>

struct GLFWwindow;

class Application
{
public:
    Application(const std::string& title);
    virtual ~Application();
    void Run();

protected:
    virtual void Cleanup();
    virtual bool Initialize();
    virtual bool Load() = 0;
    virtual void Render() = 0;
    virtual void Update() = 0;

private:
    GLFWwindow* _window = nullptr;
    int32_t _width = 0;
    int32_t _height = 0;
    std::string_view _title;
};
```

### Application.cpp
```c++
#include "Application.hpp"
#include <GLFW/glfw3.h>

Application::Application(const std::string& title)
{
    _title = title;
}

Application::~Application()
{
    Cleanup();
}

void Application::Run()
{
    if (!Initialize())
    {
        return;
    }

    while (!glfwWindowShouldClose(_window))
    {
        glfwPollEvents();
        Update();
        Render();
    }
}

void Application::Cleanup()
{
    if (_window != nullptr)
    {
        glfwDestroyWindow(_window);
        _window = nullptr;
    }
    glfwTerminate();
}

bool Application::Initialize()
{
    if (!glfwInit())
    {
        std::cout << "GLFW: Unable to initialize\n";
        return false;
    }

    GLFWmonitor* primaryMonitor = glfwGetPrimaryMonitor();
    const GLFWvidmode* videoMode = glfwGetVideoMode(primaryMonitor);
    _width = static_cast<int32_t>(videoMode->width * 0.9f);
    _height = static_cast<int32_t>(videoMode->height * 0.9f);

    glfwWindowHint(GLFW_SCALE_TO_MONITOR, GLFW_FALSE);
    glfwWindowHint(GLFW_CLIENT_API, GLFW_NO_API);
    _window = glfwCreateWindow(_width, _height, _title.data(), nullptr, nullptr);
    if (_window == nullptr)
    {
        std::cout << "GLFW: Unable to create window\n";
        return false;
    }

    const int32_t windowLeft = videoMode->width / 2 - _width / 2;
    const int32_t windowTop = videoMode->height / 2 - _height / 2;
    glfwSetWindowPos(_window, windowLeft, windowTop);

    return true;
}
```

### HelloWindowApplication.hpp
```c++
#include "Application.hpp"

class HelloWindowApplication final : public Application
{
public:
    HelloWindowApplication(const std::string& title);

protected:
    bool Load() override;
    void Render() override;
    void Update() override;
}
```

### HelloWindowApplication.cpp
```c++
#include "HelloWindowApplication.hpp"

HelloWindowApplication::HelloWindowApplication(const std::string& title)
    : Application(title)
{
}

bool HelloWindowApplication::Load()
{
    return true;
}


void HelloWindowApplication::Update()
{
}

void HelloWindowApplication::Render()
{
}
```

### Main.cpp
```c++
#include "HelloWindowApplication.hpp"

int main(int argc, char* argv[])
{
    HelloWindowApplication application{ "LearnD3D11 - Hello Window" };
    application.Run();
}
```

让我们从Main.cpp开始。这就是它所做的，创建名为“helloworld”的应用程序然后运行它。在未来这会与配置加载，初始化日志，初始化链接可能的服务，或者是其他等放在一起。

```c++
public:
...
    void Run();
...
```
这是Applcation类等一部分代码，只显示它的公开可用函数。Run方法在外部世界中最重要的函数之一，像是在Main，这是应用程序的入口点。

我们依旧不想将一切都塞进main函数中或者是某一个单一方法里，因此Run函数会再次分割成以下几个部分。
```c++
void Application::Run()
{
    if (!Initialize())
    {
        return;
    }

    if (!Load())
    {
        return;
    }

    while (!glfwWindowShouldClose(_window))
    {
        glfwPollEvents();
        Update();
        Render();
    }
}
```
这个函数相当的简单。Initialize函数顾名思义，将会初始化app运行所需的一切，当前在我们的例子中只有窗体。在未来的章节里它也将包含初始化D3D11，资源和ImGUI。

Load函数的目的是加载所有的应用程序运行所需的全部资产，在之后的章节里它将会包含纹理，着色器，模型等等。

下一个块就是上述提到的主循环或是游戏循环，再循环之前，通过操作系统检查是否需要处理事件，同时现在我们调用Update与Render方法。

Update方法主要包含询问是否按下键盘与鼠标的，更新变量，等等其他事情，而将实例反映到显示器上这是Render方法。

你大概也注意到了，所有在Application类中受保护的方法都是虚拟的，那是因为我们从Application类中派生了HelloWindowApplication类，并且只关注与这四个方法，如果需要的话。我们现在暂时不需要处理主循环
```c++
virtual void Cleanup();
virtual bool Initialize();
virtual bool Load() = 0;
virtual void Render() = 0;
virtual void Update() = 0;
```

如果你运行这个例子，你会得到一个与之前一样的窗口，只有代码被变得更符合逻辑，这将在我们移动在添加更多代码时，使我们的生活更容易。
(which will make our life easier as we move on adding more and more.)

[没有抽象的HelloWorld项目](https://github.com/GraphicsProgramming/learnd3d11/tree/main/src/Cpp/1-getting-started/1-1-1-HelloGLFWWindow)

[WinAPI版本的HelloWorld项目](https://github.com/GraphicsProgramming/learnd3d11/tree/main/src/Cpp/1-getting-started/1-1-1-HelloWin32Window)

[GitHub项目](https://github.com/GraphicsProgramming/learnd3d11/tree/main/src/Cpp/1-getting-started/1-1-1-HelloWindow)