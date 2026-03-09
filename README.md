# TinyRenderer

A software rasterization renderer written in C++ based on the [TinyRenderer](https://github.com/ssloy/tinyrenderer) tutorial, built for learning purposes. The only external dependency is the **Eigen** math library.

> 中文专栏笔记（Notes in Chinese）：<https://www.zhihu.com/column/c_1655324252130873344>

---

## ✨ Features

| Feature | Description |
|---|---|
| **Wireframe Rendering** | Draw model edges using line rasterization |
| **Triangle Rasterization** | Fill triangles using a bounding-box + barycentric-coordinate approach |
| **Backface Culling** | Discard triangles whose normals point away from the camera |
| **Z-Buffer** | Per-pixel depth test to handle occlusion correctly |
| **Matrix Transformations** | Model → View (LookAt) → Projection → Viewport pipeline |
| **Gouraud Shading** | Per-vertex lighting interpolated across the triangle |
| **Phong Shading** | Per-pixel lighting with ambient + diffuse + specular components |
| **Texture Mapping** | Diffuse, specular, glow and normal maps loaded from TGA files |
| **Tangent-Space Normal Mapping** | TBN matrix constructed per triangle for accurate surface details |
| **Shadow Mapping** | Two-pass rendering: depth pass from the light, shadow test in the main pass |
| **Programmable Shader Interface** | `vertex()` and `fragment()` virtual methods, similar to GLSL shaders |

---

## 📁 File Architecture

```
TinyRenderer/
├── CMakeLists.txt      # CMake build configuration (requires Eigen via vcpkg)
├── main.cpp            # Entry point; defines GouraudShader, DepthShader, PhongShader
├── gl.h / gl.cpp       # Core "GPU" pipeline: viewport, lookat, projection, triangle rasterizer, Shader base class
├── model.h / model.cpp # OBJ model loader; loads vertices, UVs, normals and TGA textures
├── tgaimage.h / tgaimage.cpp  # Minimal TGA image read/write library
└── obj/                # 3-D assets
    ├── african_head/   # Classic head model with textures
    ├── boggie/         # Boggie character model with textures
    └── diablo3_pose/   # Diablo 3 character model with textures (default scene)
```

### Key Modules

#### `gl.h / gl.cpp` — Rendering Pipeline
Implements the core transformation matrices and rasterization:
- `lookat()` — builds the **View** matrix from eye/center/up vectors
- `projection()` — builds a simple **Projection** matrix (perspective divide via a single coefficient)
- `viewport()` — maps NDC coordinates to screen pixels
- `triangle()` — rasterizes a triangle, runs the fragment shader per pixel, and writes to the z-buffer

#### `model.h / model.cpp` — Model & Texture Loading
Parses Wavefront `.obj` files and auto-loads associated TGA textures:
- Diffuse map (`_diffuse.tga`)
- Normal map (`_nm_tangent.tga`)
- Specular map (`_spec.tga`)
- Glow map (`_glow.tga`)

#### `main.cpp` — Shaders & Render Loop
Contains three shader implementations:

| Shader | Purpose |
|---|---|
| `DepthShader` | First pass — renders the scene from the light's point of view to fill `shadowbuffer` |
| `GouraudShader` | Diffuse lighting computed per vertex and interpolated |
| `PhongShader` | Full per-pixel lighting: ambient + diffuse + specular (Blinn-Phong) + tangent-space normal map + shadow |

---

## 🔧 Build

**Prerequisites**
- CMake ≥ 3.10
- C++11 compiler (MSVC, GCC, Clang)
- [Eigen3](https://eigen.tuxfamily.org/) (installed via [vcpkg](https://github.com/microsoft/vcpkg) by default)

**Steps**
```bash
# 1. Install Eigen via vcpkg (if not already installed)
vcpkg install eigen3

# 2. Configure and build
cmake -B build -DCMAKE_TOOLCHAIN_FILE=<path/to/vcpkg>/scripts/buildsystems/vcpkg.cmake
cmake --build build
```

---

## 🚀 Usage

Run the compiled executable, optionally passing a path to an `.obj` file:

```bash
# Use the default model (diablo3_pose)
./TinyRenderer

# Use a custom model
./TinyRenderer obj/african_head/african_head.obj
```

Output TGA images are written to the current working directory:
- `depth.tga` — shadow-map depth pass
- `diablo_shadow_glow.tga` — final rendered frame

---

## 🖼️ Gallery

**Wireframe Model**

![Wireframe](https://github.com/user-attachments/assets/432636fb-e1e3-4669-ac67-46fe731c9393)

**Flat Shading**

![Flat Shading](https://github.com/user-attachments/assets/4f6861f1-2fe4-4c81-a2bf-e823b38f7d99)

**Gouraud Shading**

![Gouraud Shading](https://github.com/user-attachments/assets/c72f9066-b548-45b4-855a-1ab3a45a2fe5)

**Phong Shading**

![Phong Shading](https://github.com/user-attachments/assets/95ccf774-2580-445e-8151-324f7cbba21c)

