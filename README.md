# 360° VR Video Player with Spatial Audio

A Unity-based virtual reality application for rendering 360-degree videos with spatialized audio support. Perfect for immersive VR experiences, spatial audio research, and hearing assessment applications.

![Unity Version](https://img.shields.io/badge/Unity-2021.3.3f1-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![VR Support](https://img.shields.io/badge/VR-Meta%20Quest%202%20%7C%20HTC%20Vive-orange)

## Features

- 📹 **360° Video Playback** - Immersive spherical video rendering
- 🎧 **Spatial Audio** - 3D positional audio with HRTF support
- 🥽 **Multi-Platform VR Support** - Compatible with Meta Quest 2 and HTC Vive
- 🎮 **Easy Setup** - Straightforward Unity project configuration
- 🔊 **Multiple Audio Sources** - Support for multiple positioned audio objects
- 🔄 **Customizable** - Extensible architecture for research and development

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Setting Up 360 Video](#setting-up-360-video)
- [Configuring Spatial Audio](#configuring-spatial-audio)
- [Supported VR Headsets](#supported-vr-headsets)
- [Project Structure](#project-structure)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Requirements

### Software

- **Unity Hub** with Unity **2021.3.3f1** or newer
- **Microsoft Visual Studio 2022** (Community Edition or higher)
- **SteamVR** (for HTC Vive) or **Oculus Desktop App** (for Meta Quest 2)

### Hardware

- VR-ready PC with:
  - Intel Core i7 or equivalent processor
  - NVIDIA GeForce RTX 3060 or better GPU
  - 16GB RAM minimum
- Supported VR headset (Meta Quest 2, HTC Vive, or compatible)

### Unity Packages

- SteamVR Plugin v2.7.3+ (for HTC Vive)
- Oculus Integration (for Meta Quest 2)
- Steam Audio v4.1.2+ **OR** 3D Tune-In Toolkit

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/Arun-Seb/360-vr-video-player.git
cd 360-vr-video-player
```

### 2. Import into Unity

Since this is a Unity project, you'll need to properly set it up:

1. Open Unity Hub
2. Click **Add** → **Add project from disk**
3. Navigate to the cloned repository folder
4. Select the project folder and click **Open**
5. Unity will import and compile all assets (this may take a few minutes on first load)

### 2. Import into Unity

Since this is a Unity project, you'll need to properly set it up:

1. Open Unity Hub
2. Click **Add** → **Add project from disk**
3. Navigate to the cloned repository folder
4. Select the project folder and click **Open**
5. Unity will import and compile all assets (this may take a few minutes on first load)

### 3. Install VR Software

#### For HTC Vive:
1. Install [SteamVR](https://store.steampowered.com/app/250820/SteamVR/)
2. Install [VIVEPORT](https://www.vive.com/us/setup/)
3. Run room setup and calibration

#### For Meta Quest 2:
1. Install [Oculus Desktop App](https://www.meta.com/quest/setup/)
2. Enable Oculus Link or Air Link
3. Enable Developer Mode on your Quest 2

### 4. Open Project in Unity

1. Open Unity Hub
2. Click **Add** → **Add project from disk**
3. Navigate to the cloned repository folder
4. Select the project and click **Open**

### 5. Install Required Unity Packages

The project will automatically import required packages. If not:

1. Go to **Window** → **Package Manager**
2. Install:
   - XR Plugin Management
   - XR Interaction Toolkit
   - Your VR platform SDK (Oculus/SteamVR)

## Quick Start

### Basic Setup (5 minutes)

1. **Import Your 360 Video**
   - Drag your 360° video file into `Assets/Videos/`
   - Unity will automatically import it

2. **Configure the Scene**
   - Open `Scenes/MainScene.unity`
   - The scene is pre-configured with a video sphere and camera rig

3. **Assign Your Video**
   - Select the `VideoSphere` object in the Hierarchy
   - In the Inspector, find the **Video Player** component
   - Drag your video to the **Video Clip** field

4. **Connect Your Headset**
   - Ensure your VR headset is connected and recognized
   - Click the **Play** button in Unity

That's it! You should now see your 360° video in VR.

## Setting Up 360 Video

### Step-by-Step Guide

#### 1. Create the Video Sphere

```
Hierarchy → Right-click → 3D Object → Sphere
```

Configure the sphere:
- **Name:** VideoSphere
- **Scale:** X: 200, Y: 200, Z: 200
- **Position:** X: 0, Y: 0, Z: 0

#### 2. Add Video Player Component

1. Select **VideoSphere** in Hierarchy
2. In Inspector, click **Add Component**
3. Search for **Video Player** and add it
4. Configure:
   - ✅ Check **Loop** (for continuous playback)
   - Drag your video to **Video Clip** field

#### 3. Create the Flip Normals Shader

The 360° video needs to render on the **inside** of the sphere. This requires a special shader that flips the normals.

**Option A: Use the Provided Shader (Recommended)**

The shader file is already included in this repository:
- Location: `Assets/Shaders/FlipNormals.shader`
- Simply use it by following step 4 below

**Option B: Create the Shader Manually**

1. In Unity's Assets folder: **Right-click** → **Create** → **Shader** → **Standard Surface Shader**
2. Name it: `FlipNormals`
3. Double-click to open in Visual Studio
4. **Delete all existing code** in the file
5. Copy the code from `Assets/Shaders/FlipNormals.shader` in this repository
6. Paste into Visual Studio
7. **Save the file** (Ctrl+S or File → Save)
8. Return to Unity (it will auto-compile the shader)

**The Shader Code:**

```hlsl
Shader "Custom/FlipNormals" {
    Properties {
        _MainTex ("Base (RGB)", 2D) = "white" {}
    }
    
    SubShader {
        Tags { "RenderType" = "Opaque" }
        Cull Off
        
        CGPROGRAM
        #pragma surface surf Lambert vertex:vert
        
        sampler2D _MainTex;
        
        struct Input {
            float2 uv_MainTex;
            float4 color : COLOR;
        };
        
        void vert(inout appdata_full v) {
            v.normal.xyz = v.normal * -1;
        }
        
        void surf(Input IN, inout SurfaceOutput o) {
            fixed3 result = tex2D(_MainTex, IN.uv_MainTex);
            o.Albedo = result.rgb;
            o.Alpha = 1;
        }
        
        ENDCG
    }
    
    Fallback "Diffuse"
}
```

**What this shader does:**
- `Cull Off` - Allows rendering on the inside of the sphere
- `v.normal.xyz = v.normal * -1` - Flips normals inward so textures render correctly from inside
- This is essential for 360° VR where the camera is inside the sphere looking outward

#### 4. Create and Apply Material

1. In Assets: **Right-click** → **Create** → **Material**
2. Name it: `360VideoMaterial`
3. Drag `FlipNormals` shader onto the material
4. Drag `360VideoMaterial` onto the **VideoSphere** in the scene

#### 5. Setup VR Camera

##### For SteamVR (HTC Vive):

1. Import SteamVR Plugin from Asset Store
2. Drag `[CameraRig]` prefab into scene
3. Position inside the VideoSphere (0, 0, 0)
4. Disable the default **Main Camera**

##### For Meta Quest 2:

1. Import Oculus Integration from Asset Store
2. Drag `OVRCameraRig` prefab into scene
3. Position inside the VideoSphere (0, 0, 0)
4. Disable the default **Main Camera**

## Configuring Spatial Audio

You can use either **Steam Audio** (recommended) or **3D Tune-In Toolkit** for spatial audio.

### Option 1: Steam Audio (Recommended)

#### Installation

1. Download [Steam Audio v4.1.2+](https://valvesoftware.github.io/steam-audio/)
2. Extract and navigate to: `steamaudio_unity/unity/`
3. Double-click `SteamAudio.unitypackage`
4. Import all files into your Unity project

#### Adding Audio Sources

**Method A: Attach to VideoSphere**

1. Select **VideoSphere** in Hierarchy
2. **Add Component** → **Audio Source**
3. Drag your audio file to **AudioClip**
4. Configure:
   - ✅ Check **Spatialize**
   - **Spatial Blend:** 1.0 (fully 3D)
   - ✅ Check **Loop** (if needed)

**Method B: Create Separate Audio Source**

1. **Right-click in Hierarchy** → **Audio** → **Audio Source**
2. Position the source in 3D space (use Transform tools)
3. Assign audio clip and configure as above

#### Enable Steam Audio Spatialization

1. Select your **Audio Source**
2. **Add Component** → Search **Steam Audio Source**
3. In Audio Source component:
   - ✅ Check **Spatialize**
   - Move **Spatial Blend** slider to 1.0

#### Custom HRTF (Optional)

Steam Audio allows custom Head-Related Transfer Functions:

1. In Steam Audio Source component
2. Enable **HRTF Interpolation**
3. Import your SOFA file via Steam Audio Settings

### Option 2: 3D Tune-In Toolkit

The [3DTI Toolkit](https://github.com/3DTune-In/3dti_AudioToolkit_UnityWrapper) is an alternative spatial audio solution.

#### Installation

1. Download the [3DTI Unity Wrapper](https://github.com/3DTune-In/3dti_AudioToolkit_UnityWrapper)
2. Import the package into Unity
3. Follow the 3DTI setup documentation

#### Configuration

1. Add **3DTI Audio Source** component to your audio objects
2. Configure binaural rendering parameters
3. Use the 3DTI Toolkit's HRTF customization features

### Resonance Audio (Legacy Alternative)

Google's Resonance Audio is also supported but cannot use custom HRTFs:

1. Download from [GitHub](https://github.com/resonance-audio/resonance-audio-unity-sdk)
2. Import into Unity
3. Add **Resonance Audio Source** component

**Note:** Resonance Audio has limited HRTF customization compared to Steam Audio and 3DTI.

## Supported VR Headsets

### ✅ Fully Tested

- **HTC Vive** (Original, Pro, Pro 2)
- **Meta Quest 2** (via Link/Air Link)

### ⚙️ Should Work (Untested)

- Meta Quest 3
- Meta Quest Pro
- Valve Index
- HTC Vive Cosmos
- Windows Mixed Reality headsets

### Integration Notes

**Meta Quest 2 Standalone:** For standalone Quest 2 deployment (without PC):
1. Switch platform to **Android** in Build Settings
2. Configure Oculus XR Plugin for Android
3. Build and deploy APK to device

## Project Structure

```
360-vr-video-player/
├── Assets/
│   ├── Scenes/
│   │   └── MainScene.unity          # Main VR scene
│   ├── Shaders/
│   │   └── FlipNormals.shader       # Inverted normals shader (REQUIRED)
│   ├── Videos/                       # Place your 360 videos here
│   ├── Audio/                        # Place your audio files here
│   ├── Materials/
│   │   └── 360VideoMaterial.mat     # Video sphere material
│   └── Prefabs/
│       ├── VideoSphere.prefab       # Pre-configured video sphere
│       └── AudioSource.prefab       # Pre-configured spatial audio
├── ProjectSettings/                  # Unity project settings
├── Packages/
│   └── manifest.json                # Package dependencies
├── README.md                         # This file
├── LICENSE                          # MIT License
└── .gitignore                       # Git ignore rules for Unity
```

### What to Commit to Git

When uploading this project to GitHub, **only** include:

✅ **Include:**
- `Assets/` folder (scripts, shaders, scenes, prefabs)
- `ProjectSettings/` folder
- `Packages/manifest.json`
- `README.md`
- `LICENSE`
- `.gitignore`

❌ **Exclude (via .gitignore):**
- `Library/` - Auto-generated by Unity
- `Temp/` - Temporary build files
- `Obj/` - Compiled object files
- `Build/` - Build output
- `Builds/` - Build output
- `.vs/` - Visual Studio files
- `*.csproj` - Auto-generated project files
- `*.sln` - Auto-generated solution files

**Why?** The `Library` folder alone can be several GB. Unity regenerates it when you open the project.

### Repository Size Guidelines

- **Without Library folder:** ~50-200 MB (manageable)
- **With Library folder:** 2-10+ GB (too large for Git)

Use the provided `.gitignore` file to automatically exclude large auto-generated files.

## Setting Up Your Own Repository

If you're creating your own fork or starting fresh:

### 1. Initialize Git in Your Unity Project

```bash
cd YourUnityProjectFolder
git init
```

### 2. Copy the .gitignore File

Use the `.gitignore` file from this repository, or create one with:

```
# Unity generated folders
[Ll]ibrary/
[Tt]emp/
[Oo]bj/
[Bb]uild/
[Bb]uilds/
[Ll]ogs/
[Uu]ser[Ss]ettings/

# MemoryCaptures can get excessive in size
[Mm]emoryCaptures/

# Visual Studio cache/options
.vs/
*.csproj
*.unityproj
*.sln
*.suo
*.tmp
*.user
*.userprefs
*.pidb
*.booproj
*.svd
*.pdb
*.mdb
*.opendb
*.VC.db

# Unity3D generated meta files
*.pidb.meta
*.pdb.meta
*.mdb.meta

# OS generated
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Exclude video/audio files (too large for Git)
*.mp4
*.mov
*.avi
*.wav
*.mp3
*.ogg
```

### 3. Add and Commit Essential Files

```bash
git add Assets/
git add ProjectSettings/
git add Packages/manifest.json
git add README.md
git add LICENSE
git add .gitignore
git commit -m "Initial commit: 360 VR Video Player"
```

### 4. Push to GitHub

```bash
git remote add origin https://github.com/Arun-Seb/360-vr-video-player.git
git branch -M main
git push -u origin main
```

### Important Note About Large Files

**Video and audio files are too large for Git.** Users should:
1. Clone your repository
2. Add their own video/audio files to the `Assets/Videos/` and `Assets/Audio/` folders
3. These folders are listed in `.gitignore` to prevent accidental commits

If you need to share sample videos, use:
- [Git LFS (Large File Storage)](https://git-lfs.github.com/)
- External hosting (Google Drive, Dropbox) with links in README
- Compressed sample videos (<25MB for GitHub)

## Troubleshooting

### Video Not Appearing

- ✅ Ensure FlipNormals shader is applied to material
- ✅ Check VideoSphere scale is 200, 200, 200
- ✅ Verify camera is positioned inside sphere (0, 0, 0)
- ✅ Make sure Main Camera is disabled

### No Audio or Non-Spatial Sound

- ✅ Check "Spatialize" is enabled in Audio Source
- ✅ Verify Spatial Blend slider is at 1.0
- ✅ Confirm Steam Audio Source component is attached
- ✅ Test with headphones (spatial audio requires stereo output)

### VR Headset Not Detected

- ✅ Ensure SteamVR/Oculus software is running
- ✅ Verify headset shows as connected in VR software
- ✅ Check XR Plugin Management settings in Unity
- ✅ Restart Unity and VR software if needed

### Performance Issues

- Lower video resolution (1080p or 1440p recommended)
- Reduce audio source count
- Check GPU usage in Task Manager
- Ensure laptop is in High Performance mode
- Close background applications

### Quest 2 Link Issues

- Use USB 3.0 cable or stable WiFi 6 for Air Link
- Enable Developer Mode on Quest 2
- Update Oculus Desktop App and Quest firmware
- Try lowering graphics quality in Oculus app

## Advanced Usage

### Multiple Audio Sources

You can have multiple positioned audio sources:

```csharp
// Example: Create audio sources programmatically
for (int i = 0; i < audioCount; i++) {
    GameObject audioObj = new GameObject($"AudioSource_{i}");
    AudioSource source = audioObj.AddComponent<AudioSource>();
    source.clip = audioClips[i];
    source.spatialize = true;
    source.spatialBlend = 1.0f;
    
    // Position in 3D space
    audioObj.transform.position = positions[i];
}
```

### Custom Video Loading

See the example scripts in `Assets/Scripts/` for runtime video loading.

## Performance Optimization

- **Video Resolution:** 4K maximum for most hardware
- **Video Codec:** H.264 is well-supported
- **Audio Channels:** Up to 41 channels tested
- **Frame Rate:** 30 or 60 fps recommended

## Future Enhancements

- [ ] Multi-channel audio support (41+ channels)
- [ ] Runtime video switching UI
- [ ] Custom HRTF loading interface
- [ ] Performance profiling tools
- [ ] Recording/replay functionality
- [ ] Network streaming support

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Citation

If you use this project in academic research, please cite:

```bibtex
@software{360_vr_video_player,
  title = {360° VR Video Player with Spatial Audio},
  author = {Sebastian, Arun},
  year = {2022},
  url = {https://github.com/Arun-Seb/360-vr-video-player}
}
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Steam Audio by Valve Software
- 3D Tune-In Toolkit team
- Unity Technologies
- SteamVR Plugin developers

## Support

For issues, questions, or suggestions:
- 🐛 [Open an issue](https://github.com/Arun-Seb/360-vr-video-player/issues)
- 💬 [Start a discussion](https://github.com/Arun-Seb/360-vr-video-player/discussions)

---

**Built with ❤️ for VR research and immersive experiences**
