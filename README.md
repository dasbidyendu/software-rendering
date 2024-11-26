
# 🎨 Graphics Programming with Windows API

This repository is a step-by-step guide to learning graphics programming in C++ using the Windows API. The project involves creating a basic rendering pipeline, working with framebuffers, drawing shapes, and rendering them in a window.

---

## 📝 **Overview**

This project teaches you:
- ✅ How to set up a window using the Windows API.  
- ✅ How to implement a framebuffer for pixel manipulation.  
- ✅ How to render 2D shapes like triangles by manually calculating pixel positions.  
- ✅ Fundamental graphics concepts like barycentric coordinates.  

---

## 🚀 **Getting Started**

### 🔧 **Requirements**
- Windows OS  
- A C++ compiler (e.g., MSVC or MinGW)  
- A code editor or IDE (e.g., Visual Studio, VS Code)  

### ⚙️ **Setup**
1. Clone the repository:  
   ```bash
   git clone <repository_url>
   ```
2. Open the project in your preferred IDE.  
3. Build and run the project.  

---

## 🖥️ **Code Explanation**

### 🔑 **Core Concepts**

1️⃣ **Framebuffer**  
- Acts as an image canvas where each pixel can be manipulated individually.  
- Stored as a 2D array of `Pixel` structs.  

2️⃣ **Rendering**  
- The `renderBufferToWindow` function copies the framebuffer to the actual window using the `StretchDIBits` API.  

3️⃣ **Drawing Shapes**  
- Triangles are drawn using a bounding box approach and barycentric coordinates to determine pixel inclusion.  

---

### 📂 **Code Breakdown**

#### 📄 **Header Files**
```cpp
#define NOMINMAX
#include <Windows.h>
#include "algorithm"
#include "cmath"
```
- Prevents naming conflicts (`NOMINMAX`).  
- Includes Windows API and essential C++ utilities.  

---

#### 🖌️ **Framebuffer and Pixel Structure**
```cpp
struct Pixel {
    unsigned char b, g, r;
};

const int WIDTH = 1000;
const int HEIGHT = 800;
Pixel framebuffer[HEIGHT][WIDTH];
```
- Represents the canvas for rendering.  

---

#### 🧹 **Clearing the Buffer**
```cpp
void clearBuffer(Pixel color) {
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            framebuffer[i][j] = color;
        }
    }
}
```
- Fills the framebuffer with a specific color.  

---

#### 🖼️ **Rendering to Window**
```cpp
void renderBufferToWindow(HDC hdc) {
    BITMAPINFO bmi = {};
    bmi.bmiHeader.biSize = sizeof(BITMAPINFOHEADER);
    bmi.bmiHeader.biWidth = WIDTH;
    bmi.bmiHeader.biHeight = -HEIGHT; // Negative indicates top-down rows
    bmi.bmiHeader.biPlanes = 1;
    bmi.bmiHeader.biBitCount = 24; // 24-bit RGB
    bmi.bmiHeader.biCompression = BI_RGB;

    StretchDIBits(hdc,
        0, 0, WIDTH, HEIGHT,
        0, 0, WIDTH, HEIGHT,
        framebuffer, &bmi, DIB_RGB_COLORS, SRCCOPY);
}
```

---

#### 🔺 **Drawing a Triangle**
```cpp
bool isPointInTriangle(int px, int py, int x1, int y1, int x2, int y2, int x3, int y3) {
    float denom = (y2 - y3) * (x1 - x3) + (x3 - x2) * (y1 - y3);
    float w1 = ((y2 - y3) * (px - x3) + (x3 - x2) * (py - y3)) / denom;
    float w2 = ((y3 - y1) * (px - x3) + (x1 - x3) * (py - y3)) / denom;
    float w3 = 1.0f - w1 - w2;
    return w1 >= 0 && w2 >= 0 && w3 >= 0;
}

void drawTriangle(int x1, int y1, int x2, int y2, int x3, int y3, Pixel color) {
    int minX = std::max(0, std::min({ x1, x2, x3 }));
    int maxX = std::min(WIDTH - 1, std::max({ x1, x2, x3 }));
    int minY = std::max(0, std::min({ y1, y2, y3 }));
    int maxY = std::min(HEIGHT - 1, std::max({ y1, y2, y3 }));

    for (int y = minY; y <= maxY; ++y) {
        for (int x = minX; x <= maxX; ++x) {
            if (isPointInTriangle(x, y, x1, y1, x2, y2, x3, y3)) {
                framebuffer[y][x] = color;
            }
        }
    }
}
```

---

#### 🖌️ **Main Rendering Loop**
```cpp
case WM_PAINT: {
    PAINTSTRUCT ps;
    HDC hdc = BeginPaint(hwnd, &ps);

    clearBuffer({255, 255, 255});
    drawTriangle(200, 100, 500, 700, 800, 300, {0, 0, 255});
    renderBufferToWindow(hdc);

    EndPaint(hwnd, &ps);
    return 0;
}
```
- Handles the painting event by clearing the framebuffer, drawing shapes, and rendering them to the window.  

---

#### 🚪 **Main Function**
```cpp
int WINAPI wWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPWSTR lpCmdLine, int nCmdShow) {
    const wchar_t CLASS_NAME[] = L"SimpleWindowClass";

    WNDCLASS wc = {};
    wc.lpfnWndProc = WindowProc;
    wc.hInstance = hInstance;
    wc.lpszClassName = CLASS_NAME;

    RegisterClass(&wc);

    HWND hwnd = CreateWindowEx(
        0, CLASS_NAME, L"Pure C++ GUI Window", WS_OVERLAPPEDWINDOW,
        CW_USEDEFAULT, CW_USEDEFAULT, windowWidth, windowHeight,
        NULL, NULL, hInstance, NULL
    );

    if (hwnd == NULL) {
        return 0;
    }

    ShowWindow(hwnd, nCmdShow);

    MSG msg = {};
    while (GetMessage(&msg, NULL, 0, 0)) {
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }

    return 0;
}
```

---

## 🌟 **Features**
- Framebuffer-based rendering  
- Basic 2D triangle drawing  
- Customizable framebuffer resolution  
- Real-time rendering to a GUI window  

---

## 🎯 **Future Goals**
- Add support for more shapes (e.g., lines, circles)  
- Build a basic 3D rendering pipeline  
- Load and render `.obj` models  
- Implement anti-aliasing for smoother edges  

---


