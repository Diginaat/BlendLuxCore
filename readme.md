## BlendLuxCore, CUDA/OptiX GPU-optimized fork (Diginaat)

> **Works with Blender 4.5 LTS and Blender 5.2 LTS, on Windows x64.**
> One zip for both: it bundles a pyluxcore build for each Blender
> (Python 3.11 for 4.5, Python 3.13 for 5.2), and Blender installs the
> one that matches. Linux and macOS are not supported yet; use the
> official BlendLuxCore there.

| Blender | Python | Status |
|---|---|---|
| 4.5 LTS | 3.11 | supported, tested |
| 5.2 LTS | 3.13 | supported, tested |
| 5.0, 5.1 | 3.11, 3.13 | may work, not tested |
| Linux, macOS | | not supported |

This fork of [BlendLuxCore](https://github.com/LuxCoreRender/BlendLuxCore)
ships the [CUDA/OptiX GPU-optimized LuxCore](https://github.com/Diginaat/LuxCore).
GPU renders on NVIDIA cards are faster; the default image is the same as
with the original LuxCore (unbiased).

### Speed compared to the original

Million samples per second on an RTX 5070 (`luxcoreconsole`, `PATHOCL`,
CUDA + OptiX, median of 3 runs of 30 s, kernels already compiled, adaptive
sampling off). Original = upstream LuxCore `ce1d64155`.

| Scene | Original LuxCore | This fork | Change |
|---|---|---|---|
| LuxBall | 11.94 | 18.00 | +51% |
| DLSC | 12.37 | 21.59 | +75% |
| LuxCore2.1Benchmark | 11.16 | 16.00 | +43% |
| DanishMood | 7.68 | 13.14 | +71% |
| LynxSpider | 5.39 | 15.38 | +185% |

Inside Blender (LuxMark LuxBall, final render, GPU, million samples per
second):

| Blender | Original addon | This fork | Change |
|---|---|---|---|
| 4.5 LTS | 24.67 | 35.12 | +42% |
| 5.2 LTS | 25.20 | 35.14 | +39% |

The GPU API matters: in Blender 4.5 (luxball.blend, 1000x1000, through
the addon) OpenCL gives 13.7 and CUDA/OptiX 19.3 million samples per
second (+41%), with the same image. This fork's addon uses CUDA/OptiX by
default; the original addon uses OpenCL.

The Blender viewport uses another engine (`RTPATHOCL`); its speed-up is
not measured yet.

Details and the full list of engine changes: [Diginaat/LuxCore](https://github.com/Diginaat/LuxCore).

### What is different from the original addon

- The addon zip bundles the fork's pyluxcore wheels (one for Blender 4.5,
  one for Blender 5.2). The original addon downloads the stock pyluxcore
  from PyPI.
- GPU API default: CUDA/OptiX (original: OpenCL). AMD or Intel GPU: select
  OpenCL in Edit > Preferences > Add-ons > BlendLuxCore.
- Render properties > Light Paths > **Path Guiding** (GPU path engine only,
  off by default, experimental).
- Extension name `BlendLuxCoreDiginaat`; the manifest allows Blender 4.5
  up to 5.2 (`blender_version_max = "5.3.0"`) and only Windows x64.

### Install

1. Download `BlendLuxCoreDiginaat-*.zip` from
   [Releases](https://github.com/Diginaat/BlendLuxCore/releases).
2. In Blender 4.5 or 5.2, remove an installed BlendLuxCore first
   (Edit > Preferences > Add-ons), then restart Blender. Do this in each
   Blender version you use: each has its own add-ons.
3. Edit > Preferences > Get Extensions > menu at the top right >
   Install from Disk, and pick the zip.
4. Select the LuxCore render engine and set the device to GPU.
5. If you used BlendLuxCore before: check that Edit > Preferences >
   Add-ons > BlendLuxCore > GPU API is **CUDA/OptiX**. A saved older
   setting can still say OpenCL.

The original readme follows.

---

<p align="center">
<h1 align="center">BlendLuxCore</h1>
</p>
<p align="center">
<b>Blender Addon for LuxCoreRender</b>
</p>


![Example Render](https://luxcorerender.org/wp-content/uploads/2025/02/dark_mode_wide.jpg)


This addon integrates the LuxCore render engine into Blender. It offers advanced features like accelerated rendering of indirect light and efficient rendering of caustics.


## Supported Blender Versions

* BlendLuxCore v2.11.x officially supports Blender versions 4.5 LTS and 5.2 LTS
* Support for other 4.X and non-LTS releases has not been tested and is not guaranteed
* Supported platforms are Windows x86_64, Linux, MacOS Intel, MacOS ARM

**Previous BlendLuxCore releases:**
* Blender 4.2 LTS is supported by BlendLuxCore v2.10.
* Blender 2.93 is supported by BlendLuxCore v2.6.
* Blender 2.83-2.92 are supported by BlendLuxCore v2.5.
* Blender 2.83 is supported by BlendLuxCore v2.4.
* Blender 2.80, 2.81 and 2.82 are supported by BlendLuxCore v2.2 and v2.3.  
* Blender 2.79 is supported by BlendLuxCore v2.0, v2.1 and v2.2.
Supported platforms are Windows, Linux, MacOS Intel

## Installation

### From latest release (recommended)

- Find the latest suitable release of BlendLuxCrore on the release page:  
https://github.com/LuxCoreRender/BlendLuxCore/releases
- From the release assets, download extension `BlendLuxCore-*.zip`
- Open Blender and follow "Install from disk" procedure (https://docs.blender.org/manual/en/latest/editors/preferences/extensions.html)

Beforehand, you must uninstall any previous version of BlendLuxCore: look in the "Get Extensions" panel.

See also https://wiki.luxcorerender.org/BlendLuxCore_Installation for a more detailed guide.

### Building locally from source

**Note: It is recommended to build the extension using GitHub Actions in a local fork!**

Prerequisites: you need `cmake` and `blender` installed and in your `PATH`

Build extension:
- clone this repository: `git clone https://github.com/LuxCoreRender/BlendLuxCore.git`
- configure: `cmake -S BlendLuxCore -B blc-build -DCMAKE_BUILD_TYPE=Release`
- build: `cmake --build blc-build`

To create a `Latest` release, use the option `-DCMAKE_BUILD_TYPE=Latest` in the step `configure`.

The build script then places the collected zip-file in the `blc-build/out` subfolder.

Open Blender and follow "Install from disk" procedure (https://docs.blender.org/manual/en/latest/editors/preferences/extensions.html)

Beforehand, you may want to uninstall previous version of BlendLuxCore: look in the "Get Extensions" panel.

### Installing a development setup

To facilitate the development of BlendLuxCore (and optionally LuxCore), install BlendLuxCore in conjunction with the secondary extension [BlendLuxHelper](https://github.com/LuxCoreRender/BlendLuxHelper)

See the guide on the wiki for full instructions:
https://wiki.luxcorerender.org/Developing_and_debugging_BlendLuxCore

## Important Links

**Homepage:**        **https://luxcorerender.org/**  
**Wiki:**            **https://wiki.luxcorerender.org/**  
**Forums:**          **https://forums.luxcorerender.org/**  
**Discord:**         **https://discord.com/invite/chPGsKV**  
**Gallery:**         **https://luxcorerender.org/gallery/**  
**Example Scenes:**  **https://luxcorerender.org/example-scenes/**  

**In case of questions or support, please open an issue here on GitHub, or get in touch via the Forums or our Discord server.**