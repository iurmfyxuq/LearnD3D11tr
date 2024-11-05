# Hello D3D11

在这一章中，我们将为你介绍D3D11的基础使用，如何创建ID3D11Device同时如何使用它以展示一些东西在我们的窗体中。在最后的章节里，我们建立了一个基础实现为应用程序带窗体通过GLFW。在这里，Main.cpp与Application.cpp将不会出现。

如果你正在看这一章的源码的话，你会注意到Application.cpp与Application.hpp不存在，那时因为我们已经把这两个文件移动到一个单独的Framework工程中，它将创建一个静态库用来缓和章节之间的开发。这个Framework工程将会包含在所有工程项目之间共享的代码，因此它可能会包含很多其他没被使用或是与某些章节不相关的文件。

Framework工程可以在[这里](https://github.com/GraphicsProgramming/learnd3d11/tree/main/src/Cpp/Framework)查看。

请注意，已经存在文件中的代码也是会修改的为了迎合更新的章节与他们的需要。

然而，让我们开始让我们来分解相关的部分以向你展示新类从Application类派生的看起来像什么。

## HelloD3D11Application.hpp
```c++
#pragma once

#include <d3d11.h>
#include <dxgi1_3.h>
#include <wrl.h>

#include <Application.hpp>

class HelloD3D11Application final : public Application
{
    template <typename T>
    using ComPtr = Microsoft::WRL::ComPtr<T>;

public:
    HelloD3D11Application(const std::string& title);
    ~HelloD3D11Application() override;

protected:
    bool Initialize() override;
    bool Load() override;
    void OnResize(
        int32_t width,
        int32_t height) override;
    void Update() override;
    void Render() override;

private:
    bool CreateSwapchainResources();
    void DestroySwapchainResources();

    ComPtr<ID3D11Device> _device = nullptr;
    ComPtr<ID3D11DeviceContext> _deviceContext = nullptr;
    ComPtr<IDXGIFactory2> _dxgiFactory = nullptr;
    ComPtr<IDXGISwapChain1> _swapChain = nullptr;
    ComPtr<ID3D11RenderTargetView> _renderTarget = nullptr;
};
```

## HelloD3D11Application.cpp

实现部分

```c++
#include <DirectXMath.h>
#include <d3dcompiler.h>

#include <iostream>

#pragma comment(lib, "d3d11.lib")
#pragma comment(lib, "dxgi.lib")
#pragma comment(lib, "d3dcompiler.lib")
#pragma comment(lib, "winmm.lib")
#pragma comment(lib, "dxguid.lib")

HelloD3D11Application::HelloD3D11Application(const std::string& title)
    : Application(title)
{
}

HelloD3D11Application::~HelloD3D11Application()
{
}

bool HelloD3D11Application::Initialize()
{
    return true;
}

bool HelloD3D11Application::Load()
{
    return true;
}

bool HelloD3D11Application::CreateSwapchainResources()
{
    return true;
}

void HelloD3D11Application::DestroySwapchainResources()
{
}

void HelloD3D11Application::OnResize(
    const int32_t width,
    const int32_t height)
{
}

void HelloD3D11Application::Update()
{
}

void HelloD3D11Application::Render()
{
}
```

HelloD3D11Application.hpp

```c++
#include <d3d11.h>
#include <dxgi1_3.h>
#include <wrl.h>
```

HelloD3D11Application.cpp
```c++
#include <GLFW/glfw3.h>
#define GLFW_EXPOSE_NATIVE_WIN32
#include <GLFW/glfw3native.h>

#include <d3dcompiler.h>
#include <DirectXMath.h>
```

我们需要导入以下的头文件，这里每一种头文件包括

* d3d11.h D3D11相关代码，它包含我们将会在D3D11中使用的ID3D11XXX接口和大多数枚举
* dxgi1_3 DXGI相关，它包含了IDXGIXXX接口和额外的枚举在需要DXGI结构体
* d3dcompiler.h 包含用于编译我们的HLSL着色器变成字节码的函数，并导入GPU中
* DirectXMath.h DirectX自己的数学库，它包含数据结构与数学函数我们将使用在整个系列中
* wrl.h `用于Microsoft::WRL::ComPtr<T>`，用来管理COM智能指针

```c++
#pragma comment(lib, "d3d11.lib")
#pragma comment(lib, "dxgi.lib")
#pragma comment(lib, "d3dcompiler.lib")
#pragma comment(lib, "winmm.lib")
#pragma comment(lib, "dxguid.lib")
```
当然，只是引入头文件还不够，我们还必须链接到D3D11和相关的库以能够实际的使用声明在头文件中，把#pragma comment(lib, "PATH_TO_LIB")放在HelloD3D11Application.cpp中就会去链接这些库

```c++
ComPtr<IDXGIFactory2> _dxgiFactory = nullptr;
ComPtr<ID3D11Device> _device = nullptr;
ComPtr<ID3D11DeviceContext> _deviceContext = nullptr;
ComPtr<IDXGISwapChain1> _swapChain = nullptr;
ComPtr<ID3D11RenderTargetView> _renderTarget = nullptr;
```
你可能注意到了，我们并没有使用原始指针，而是ComPtr。DirectX是构筑在COM（组件对象模型Component Object Model），同时COM对象使用引用计数来管理对象的生命周期，以AddRef和Release方法的形式。`ComPtr<T>`为我们包装了这些功能，通过创建智能指针，你可以在[这里](https://learn.microsoft.com/zh-cn/cpp/cppcx/wrl/comptr-class?view=msvc-170)了解更多信息。

IDXGIFactory2帮助我们查找我们能够使用去运行的我们图形的适配器。它能列举处所以存在的适配器（GPU），其中有几个安装在你的系统中。如果你有一台笔记本，那就有可能和你的cpu合并，但是通常现在的笔记本也会有独立显卡，或者是你的pc可能安装了不止1张显卡，通过IDXGIFactory2，我们可以挑选其中的一张。它同时也为我能创建交换链，一个用于春初渲染数据的surface，呈现它在输出（或是屏幕）。
