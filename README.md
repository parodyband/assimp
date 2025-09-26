# OpenAssetImporter Library Binding for Zig

This repo is a build sdk for [Assimp](https://github.com/assimp/assimp) to be used with the Zig build system.

## macOS Compatibility

⚠️ **Note for macOS users**: Due to conflicts between zlib and macOS system headers, the library must be built with `Z_SOLO` mode which disables certain compression functions. As a result, the following formats are not supported on macOS:

- **3MF** - 3D Manufacturing Format (uses ZIP compression)
- **Assbin** - Assimp Binary format (uses compress2/uncompress)
- **Blend** - Blender files (may use compression)
- **Irr** - Irrlicht scenes (may use compression)
- **Q3BSP** - Quake 3 BSP (may use compression)
- **X** - DirectX .x files (may use compression)
- **XGL** - OpenGL XML format (may use compression)

These formats will be automatically disabled when building on macOS. 

### ✅ Supported Formats on macOS

The following popular formats work normally on macOS:
- **FBX** - Both ASCII and Binary FBX files (the code gracefully handles Z_SOLO mode)
- **glTF/glTF2** - Including embedded and binary formats
- **OBJ** - Wavefront OBJ with MTL support
- **STL** - Both ASCII and binary STL
- **Collada (DAE)** - Full support
- **PLY** - Stanford PLY format
- And many others that don't require the disabled compression functions

## Usage

```zig
const std = @import("std");

// Import the SDK
const Assimp = @import("Sdk.zig");

pub fn build(b: *std.build.Builder) void {
    const mode = b.standardReleaseOptions();

    const exe = b.addExecutable("static-example", null);
    exe.setBuildMode(mode);
    exe.addCSourceFile("src/example.cpp", &[_][]const u8{"-std=c++17"});
    exe.linkLibC();
    exe.linkLibCpp();
    exe.install();
    
    // Create a new instance
    var sdk = Assimp.init(b);

    // And link Assimp statically to our exe and enable a default set of
    // formats.
    sdk.addTo(exe, .static, Assimp.FormatSet.default);
}
```