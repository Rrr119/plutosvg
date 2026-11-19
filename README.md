![emoji-collection.png](https://github.com/user-attachments/assets/a5de9b70-39a8-4a15-a012-22ab3cb93054)

# PlutoSVG

PlutoSVG is a compact and efficient SVG rendering library written in C. It is specifically designed for parsing and rendering SVG documents embedded in OpenType fonts, providing an optimal balance between speed and minimal memory usage. It is also suitable for rendering scalable icons.

## Basic Usage

```c
#include <plutosvg.h>

#include <stdio.h>

int main(void)
{
    plutosvg_document_t* document = plutosvg_document_load_from_file("camera.svg", -1, -1);
    if(document == NULL) {
        printf("Unable to load: camera.svg\n");
        return -1;
    }

    plutovg_surface_t* surface = plutosvg_document_render_to_surface(document, NULL, -1, -1, NULL, NULL, NULL);
    plutovg_surface_write_to_png(surface, "camera.png");
    plutosvg_document_destroy(document);
    plutovg_surface_destroy(surface);
    return 0;
}
```

![camera.png](https://github.com/sammycage/plutosvg/blob/master/camera.png)

## SVG 2 Mesh Gradients

PlutoSVG supports SVG 2 `meshgradient` paint servers, including `meshrow`
and `meshpatch` subsections, Coons patch paths, shared patch edges, and
`objectBoundingBox` or `userSpaceOnUse` coordinates. Both the SVG 2
lowercase element names and the older camelCase spellings are accepted.

The example below compares the same icon rendered before and after mesh
gradient support:

| Before | After |
| --- | --- |
| ![mesh gradient before](artifacts/mesh-before.png) | ![mesh gradient after](artifacts/mesh-after.png) |

The 2x2 shared-edge test and an `objectBoundingBox` example are available in
`examples/mesh-gradient.svg` and `examples/mesh-gradient-bbox.svg`. Render
them with:

```bash
./build/examples/svg2png examples/mesh-gradient.svg mesh-grid.png
./build/examples/svg2png examples/mesh-gradient-bbox.svg mesh-bbox.png
```

## Integrating with FreeType

```c
#include <plutosvg-ft.h>

#include <ft2build.h>
#include FT_FREETYPE_H
#include FT_MODULE_H

int main(void)
{
    FT_Library library;

    // Initialize the FreeType library
    if(FT_Init_FreeType(&library)) {
        // Handle error
        return -1;
    }

    // Set PlutoSVG hooks for the SVG module
    if(FT_Property_Set(library, "ot-svg", "svg-hooks", &plutosvg_ft_hooks)) {
        // Handle error
        return -1;
    }

    // Your code here

    // Clean up
    FT_Done_FreeType(library);
    return 0;
}
```

## Installation

Follow the steps below to install PlutoSVG using either [Meson](https://mesonbuild.com/) or [CMake](https://cmake.org/).

### Using Meson

```bash
git clone https://github.com/sammycage/plutosvg.git
cd plutosvg
meson setup build
meson compile -C build
meson install -C build
```

### Using CMake

```bash
git clone --recursive https://github.com/sammycage/plutosvg.git
cd plutosvg
cmake -B build .
cmake --build build
cmake --install build
```

### Projects Using PlutoSVG

- [PumpkinOS](https://github.com/migueletto/PumpkinOS)
- [Shell](https://github.com/moudey/Shell)
- [Dear ImGui](https://github.com/ocornut/imgui)
- [SDL_ttf](https://github.com/libsdl-org/SDL_ttf)
- [PCSX2](https://github.com/PCSX2/pcsx2)
- [FastChart](https://github.com/iliaal/fastchart)
