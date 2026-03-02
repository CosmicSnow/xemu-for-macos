# xemu for macOS (MoltenVK Fork)

[![Download](https://img.shields.io/badge/Download-Latest%20Release-blue)](https://github.com/CosmicSnow/xemu-for-macos/releases/tag/MoltenVK)
[![Original Project](https://img.shields.io/badge/Original%20Project-xemu--project/xemu-green)](https://github.com/xemu-project/xemu)
[![Platform](https://img.shields.io/badge/Platform-macOS%20(Apple%20Silicon%20%26%20Intel)-silver)](https://github.com/CosmicSnow/xemu-for-macos/releases)

This is a **fork of xemu** specifically modified to enable Vulkan rendering support on macOS via [MoltenVK](https://github.com/KhronosGroup/MoltenVK) (Vulkan-over-Metal translation layer).

---

## 🎯 What is this Fork?

This fork contains necessary modifications to make xemu's Vulkan renderer work on macOS. The original xemu project uses **Geometry Shaders** for primitive processing (quads, quad strips, line loops, provoking vertex control), which are **not supported by Apple's Metal API**.

### Original Project

➡️ **https://github.com/xemu-project/xemu**

The upstream xemu project is an original Xbox emulator focused on accurate hardware emulation. This fork maintains compatibility with the original while adding macOS-specific workarounds.

---

## 🤔 Why was this Fork Necessary?

### The Problem

MoltenVK translates Vulkan calls to Metal, but **Metal lacks native Geometry Shader support**. The original xemu relies on Geometry Shaders for:

- **Primitive conversion** (Xbox quads → triangles, quad strips, etc.)
- **Provoking vertex control** (Xbox uses "last vertex", Metal uses "first vertex")
- **Line loop emulation**

### The Solution

This fork implements **CPU-side primitive emulation**:

| Feature | Original xemu | This Fork (macOS) |
|---------|---------------|-------------------|
| Quads | Geometry Shader | CPU Index Generation |
| Quad Strips | Geometry Shader | CPU Index Generation |
| Line Loops | Geometry Shader | CPU Index Generation |
| Provoking Vertex | Geometry Shader | CPU Index Rotation |

The result is fully functional Vulkan rendering on macOS with minimal performance impact.

---

## 📥 Download

### Latest Release

**⬇️ [Download xemu for macOS (MoltenVK)](https://github.com/CosmicSnow/xemu-for-macos/releases/tag/MoltenVK)**

Supports:
- ✅ macOS 11+ (Big Sur and later)
- ✅ Apple Silicon (M1, M2, M3, M4, M5)

---

## 🚀 Installation & Requirements

### Prerequisites

You **must** install the Vulkan Loader before running:

```bash
brew install vulkan-loader
```

This provides the `libvulkan.dylib` library that xemu needs to initialize Vulkan on macOS.

### Installation Steps

1. **Install Vulkan Loader:**
   ```bash
   brew install vulkan-loader
   ```

2. **Download the release:**
   - Go to [Releases](https://github.com/CosmicSnow/xemu-for-macos/releases/tag/MoltenVK)
   - Download `MacOS-xemu-vulkan-v0-8-134.zip`

3. **Run xemu:**
   - Extract the zip file
   - Move `xemu.app` to your Applications folder
   - Launch and enjoy!
  
### Build from Source Steps
Follow https://xemu.app/docs/dev/building-from-source/

---

## 🔧 Technical Details

### Changes from Original xemu

This fork includes the following modifications:

1. **Portability Extensions** - Enables `VK_KHR_portability_subset` and `VK_KHR_portability_enumeration` for MoltenVK compatibility

2. **External Memory Disable** - Disables `VK_KHR_external_memory_*` extensions (not supported on macOS)

3. **Geometry Shader Emulation** - CPU-side conversion of Xbox primitives to Vulkan-compatible formats:
   - [`hw/xbox/nv2a/pgraph/vk/draw.c`](hw/xbox/nv2a/pgraph/vk/draw.c) - `build_emulated_indices_*()` functions
   - [`hw/xbox/nv2a/pgraph/vk/draw.c`](hw/xbox/nv2a/pgraph/vk/draw.c) - `draw_needs_primitive_emulation()` detection

4. **Provoking Vertex Workaround** - Manual index rotation to match Xbox's "last vertex" behavior on Metal's "first vertex" system

5. **Volk Loader Patch** - Added Apple Silicon Homebrew path (`/opt/homebrew/lib`) for Vulkan loader discovery

### File Changes

- `hw/xbox/nv2a/pgraph/vk/instance.c` - Portability extensions enrollment
- `hw/xbox/nv2a/pgraph/vk/renderer.h` - External memory disable for macOS
- `hw/xbox/nv2a/pgraph/vk/draw.c` - CPU primitive emulation
- `hw/xbox/nv2a/pgraph/vk/shaders.c` - Conditional geometry shader pipeline

---

## 🎮 Usage

Once installed, use xemu exactly like the original:

1. Launch xemu
2. Go to **View** → **backend**
3. Select **Vulkan** as the rendering backend
4. Have fun!
<img width="472" height="220" alt="image" src="https://github.com/user-attachments/assets/864a72f1-ba41-43ec-99a1-8c6b4fcb97b8" />


---

## 🐛 Troubleshooting

### "Failed to initialize Vulkan" error

Make sure you installed the Vulkan loader:
```bash
brew install vulkan-loader
```

### Performance issues

- Vulkan on macOS via MoltenVK may have different performance characteristics than native Metal or OpenGL
- For best results, use macOS 13+ (Ventura) or later
- Some games may glitch or show graphic problems

---

## 🤝 Contributing

This fork is provided as-is.

It exists solely as a temporary workaround to enable macOS support while the upstream project works toward an official and definitive solution. There are no plans for further development, feature additions, or long-term maintenance in this fork.

Please treat this repository as a stopgap solution — not a permanent alternative to the original project.

---

## 🚫 Reporting Issues

If you encounter bugs while using this fork:
	•	Do NOT report them to the original xemu project.
	•	Issues specific to this fork must not be submitted upstream.

The original project is actively working on a proper macOS implementation. Any bugs introduced by this fork are unrelated to upstream development and should not create noise in their issue tracker.

For official updates and long-term support, please follow the main project:

https://github.com/xemu-project/xemu

Thank you for understanding.

---

## 📜 License

This fork maintains the same license as the original xemu project (GPL v2). See the original repository for full licensing details.

---

## 🙏 Acknowledgments

- **xemu-project** - For creating the original Xbox emulator
- **MoltenVK Team** - For the Vulkan-to-Metal translation layer
- **Homebrew** - For making Vulkan loader installation easy on macOS

---

**Note:** This is an unofficial fork. For the official xemu project, visit https://xemu.app
