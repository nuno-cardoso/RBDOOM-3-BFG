```
    ____   ____   ____                           _____  ____   ______ ______
   / __ \ / __ ) / __ \ ____   ____   ____ ___  |__  / / __ ) / ____// ____/
  / /_/ // __  |/ / / // __ \ / __ \ / __ `__ \  /_ < / __  |/ /_   / / __  
 / _, _// /_/ // /_/ // /_/ // /_/ // / / / / /___/ // /_/ // __/  / /_/ /  
/_/ |_|/_____//_____/ \____/ \____//_/ /_/ /_//____//_____//_/     \____/   
_______________________________________________________________________
```


RBDOOM-3-BFG Release Notes - https://github.com/RobertBeckebans/RBDOOM-3-BFG

Thank you for downloading RBDOOM-3-BFG.


_______________________________________

TBD - RBDOOM-3-BFG 1.6.0
_______________________________


## .plan - April 24, 2024

Cudos to Stephen Saunders for most changes in this build. NVRHI was updated to the version on 25 February.
The shader compiling part was also split out of NVRHI into a new ShaderMake tool by Nvidia.

You can get Blender lights to work with the glTF workflow without the need to place fake light entities in Blender.
VR options are stripped from the settings menu and com_showFPS > 2 show the VRAM memory usage.

Optick has been improved for macOS and Vulkan and otherwise most changes are developer related.
The renderdemo code has been removed and if you compile the engine without Classic Doom support then you will bypass the startup screen and get into the main menu immediatly.

Changelog:

* Read Blender lights directly through the KHR_lights_punctual glTF extension

* Don't let VR options of other VR builds to break rendering of the non-VR master

* Fix testVideo to check for viewDef->viewEntitys (i.e. 3D/2D) not console state

* When playing testVideos, skip sRGB to linear conversion only when console active (i.e. 2D)

* Check for valid allocations before freeing Bink Decoder bundles

* Renamed DX12/Vulkan specific cvars with a r_vk/r_dx prefix

* Set r_maxFrameLatency max value constraint to NUM_FRAME_DATA

* Change r_maxFrameLatency cvar name and set to default value of 2 frames

* Implement m_frameLatencyWaitableObject sync for reduced DX12 frame latency

* Extend Optick to support data tags on custom storage events

* Added CMake -DRETAIL option for shipping builds on Github/ModDB

* Skip startup if not compiled with Doom Classic support, closes #874

* More renderdemo code removed

* Killed hard to maintain renderdemo code

* Fix for cinematic audio when playing Bink video files with ffmpeg decoder, improve ffmpeg a/v resync

* Show VRAM memory usage with com_showFPS > 2 in separate line

* Correct some uint64 types and add Optick frame tag for DX12 / Vulkan Present()

* Optick: Eliminate need for blocking sleep wait at start of Vulkan clock sync

* Optick: Remove blocking sleep wait at start of Vulkan clock synchronization

* Complete Optick instrumentation and align with HUD GPU timers



## .plan - January 20, 2024

Cudos to Stephen Saunders for this build and to reeFridge for finding the issue.
This fixes a number of multiplayer issues. Create Private Match with clients now works reliably. The missing functionality of the online game browser and leaderboards hasn't been fixed.

Changelog:

* Fixes regression Multiplayer: Accessing memory after it has been freed #846 caused by an earlier, but broken memory leak fix on my part. The fix is almost identical to PR Free idLobby memory inside destructor #847

* Fixes a critical issue with mis-reading network snapshot data on the client side, which caused no end of problems with mis-rendering, slowdowns, unstable connections, etc. The client was ignoring the entity network-synced flag which tells it whether it should read entity class-specific data. It was trying to read it all the time and sometimes coming up with null data which caused tons of problems with rendering and physics calculations (e.g. operations on NaN numbers). Simple fix was to respect the entity's network-synced flag on the client side but it makes all the difference.

* Fixes an incorrect assert on multiplayer VoiceChat shutdown

* Allows r_useScissor and r_useParallelAdd cvars to be changed in multiplayer mode for use in bake* operations on multiplayer maps

* Fixed a couple of uninitialized variables that showed up in valgrind when in multiplayer mode

* Added Amstrad CPC 6128 Retro rendering mode


## .plan - January 03, 2024

This is a preview build of the new Retro 8-bit/16-bit/PSX rendering modes.

The new rendering modes can be set in the menu options but it's controlled mainly r_renderMode.
The values are 0 = Doom, 1 = Commodore 64, 2 = Commodore 64 Highres, 3 = Sega Genesis, 4 = Sega Genesis Highres, 5 = Sony PSX

The Commodore 64 mode regulates all colors down to the original 16 color palette.
The Sega mode mimics 9 bit color HW which means 3 bit per color channel resulting in a total of 512 colors.
The PSX mode only turns off linear filtering for the textures and applies a screen space dithering effect.

All retro rendering modes try to mimic the 320x240 resolution but it is extended to 16:9 so it is 480 x 270.
Highres modes only apply a higher resolution dithering on the pixelated output.

The PSX mode has no additional artifacts yet like wiggling vertices or textures.

There are also 2 new CRT filters that are drawn on top of everything else (even the console) for more arcade vibes.

Changelog:

* Fixed scissor clipping issues of regular surfaces like light flares #651

* Duplicating lights with Ctrl+D works now

* Merged script interpreter improvements from Dhewm3, especially that fixes https://github.com/dhewm/dhewm3/issues/303

* Doubled MAX_GLOBALS for the Runners 2.6 mod

* Crash fix between level switching and loading of new textures for D3HDP and other mods

* Fixed many small memory leaks (thanks to Steve Saunders)

* Reduced console spam and got rid of the depth-stencil is read-only warnings

* Added image_pixelLook to disable texture filtering on most textures regardless of the render mode

* Changed devtools.cfg so you can easily switch between the new render modes with F7 and F8


Changelog TrenchBroomBFG:

* Added Show patches option to View Options


## .plan - October 27, 2023

This is a preview build of the new WIP ingame Light Editor with some important bugfixes in the convertMapToValve220 command.

The Light editor can be opened using the `editLights` command in the console. Just bind F1 editLights.

It docked into certain screen areas.
It features a gizmos for translation, rotation and scaling lights and a experimental menu bar.
Light transforms can also be edited directly. 
Gizmos can use snapping with defaults: 4 units for grid snapping, 15 degrees for angle snapping and 10% for scale snapping.

It also provides a few keyboard shortcuts:

G: Change to the translation gizmo
R: Change to rotation gizmo
Alt + R: Reset the current rotation
s: Change to scaling gizmo

Ctrl + S - Save all changed lights
Ctrl + D - Duplicate the existing light (Should not be used yet beause it adds a light every frame. It is better to add lights in TrenchBroom)

Steven Pridham also fixed the game related post-processing fullscreen FX effects.
Steven Saunders fixed many Linux/macOS specific compiler warnings and extended the renderer to support the Optick profiler using the Vulkan backend.
That feature is for coders and not part of the binary.

Changelog:

* Added new helper entityDefs like func_elevator_model in base/def/_tb_helpers.def for TrenchBroomBFG

* Prioritize .wav and .ogg files over shipped .idwav files so overriding existing sounds works better

* Fixed some critical bugs in the convertMapToValve220 command. Added origin brushes

* Light editor can use the rotation/scale gizmos

* Light editor can use the translation gizmo

* Fixed Imgui and light scissor clipping issues. Closes #651

* Wrote simple exportMaterialsToBlender command which saves all materials to base/_bl/materials.json

* Started to simplify the light editor

* Use same Instance pattern of AF editor for light editor

* Always draw the console after Imgui

* Added code to load UE5 editor themes into Imgui

* Imgui ingame tools can use the docking feature now

* Updated Imgui to newest docking release v1.89.9

* Fix fullscreen warp FX for grabber and various effects like Berserker (Thanks Steven Pridham)

* Fixed HLSL code to compile with newer versions of DXC

* Fix system vs. bundled library logic, suppress gcc/clang warnings for some third party source libs (jpeg, zlib, minizip)

* Fix MSVC warnings, suppress for some third party source libs (jpeg, png, oggvorbis)

* Update CMakeLists to add clang -Wno-shorten-64-to-32 flag to suppress flood of int conversion warnings

* Update rapidjson lib to remove deprecated std::iterator template and replace with required iterator types

* Update jpeglib's format_message() error routine to use snprintf() for buffer security

* Replace sprintf() / vsprintf() with idStr::snPrintf() / idStr::vsnPrintf() for buffer security

* BFG Resource File Manager under tools/bfgpakexplorer has been updated


Changelog TrenchBroomBFG:

* Updated FGDs exported from the engine which can be found in base/_tb/fgd/*

* Bezier patches can be duplicated and copy pasted within a map and copied from another TrenchBroomBFG instance

* the DOOM-3-models.fgd has been restored


_______________________________________

23 May 2023 - RBDOOM-3-BFG 1.5.1
_______________________________

# RBDOOM-3-BFG 1.5.1 Hotfix Patch

This is just a bugfix build that addresses several crashes and minor changes.

## Quick Installation Guide for Beginners

1. Make a new DoomBFG folder
2. Copy base/ from your Steam Doom 3 BFG folder into DoomBFG
3. Download the RBDOOM-3-BFG 1.3.0 full package from the [RBDOOM-3-BFG ModDB page](https://www.moddb.com/mods/rbdoom-3-bfg) and extract it over DoomBFG.
4. Do the same with the 1.5.1 patch.

If you already have a version of RBDOOM-3-BFG then you can just unpack this patch over it.

Changelog:

* Bumped version to 1.5.1

* Fixed "Failed to create a graphics pipeline state object" error in the Erebus 3 map. #770

* Disable DXGI automatic display mode switching for alt-enter borderless fullscreen

* Fixed crash in Delta Labs Sector 3 with Vulkan on Linux (Radeon RX 6700)


_______________________________________

29 April 2023 - RBDOOM-3-BFG 1.5.0
_______________________________

# RBDOOM-3-BFG 1.5.0 adds DX12, Vulkan and glTF 2 Support

So over 600 commits later and probably over 1200 hours provided by 4 programmers RBDOOM-3-BFG 1.5.0 is final after one year of development.

This update is huge and replaces OpenGL with DX12 and Vulkan using Nvidia's NVRHI API and adds support for glTF 2 models and animations.

## DX12 & Vulkan through NVRHI

<img src="https://i.imgur.com/QKTukSq.jpg" width="192"> <img src="https://i.imgur.com/ymRXjQo.jpg" width="192"> 

OpenGL is dead and won't be further developed. OpenGL was holding back the development of this source port for quite some time and all the HLSL -> GLSL translation code with different outputs for Windows, Mesa and Vulkan GLSL was a time eater.

I was excited when Vulkan was announced but it wasn't fun at all to work with because it is way too verbose and difficult to work with even after 17 years of OpenGL experience. It was even annoying to just render anything offscreen like shadow maps. Right now, Vulkan offers many extensions to make those tasks simpler but if you are using modern Vulkan then it is already so different that people demand to make a new Vulkan 2 API.
Vulkan became immediatly the extension hell OpenGL was and all I wanted was a nice and simple API like OpenGL ES 3 but with proper error feedback and more speed.

I was looking for several years for a good abstraction layer of modern graphics APIs like DX12 and Vulkan. I considered The-Forge, Dilligent Engine, Tiny Renderers (Google) or BGFX for reducing the maintencance costs of the RBDOOM-3-BFG renderer.

Then I stumbled over an excellent article by Alexey Panteleev about [Writing Portable Rendering Code with NVRHI](https://developer.nvidia.com/blog/writing-portable-rendering-code-with-nvrhi/).

<img src="https://imgur.com/Th703A5.jpg" width="576">

NVRHI (NVIDIA Rendering Hardware Interface) is a library that implements a common abstraction layer over multiple graphics APIs (GAPIs): Direct3D 11, Direct3D 12, and Vulkan 1.2. It works on Windows (x64 and ARM64) and Linux (x64 and ARM64) and macOS through MoltenVK. It is not tied to Nvidia GPUs but also works with AMD and Intel drivers.

I suggested it in Discord and that we should go into that direction if we move the renderer to an abstraction library. Stephen Pridham took the initiative and wrote the initial port to NVRHI. Porting a game like Doom 3 BFG to another render API is not trivial.
It also changed my plans for 2022 and ruined many weekends by fixing renderer related bugs.
However I want to continue to work on this project in my spare time for the next 10 years so the effort was worth it. In the end it turned out that NVRHI is as easy to use as OpenGL with the Donut samples provided.
There is definitely some lack of documentation but now you have a former AAA game rendered with NVRHI as an example.
RBDOOM-3-BFG has a similar renderer complexity to older Call of Duty titles or the Source engine.
The implementation is not optimal in terms of performance and very close to the original rendering architecture of Doom 3 BFG.
However it is nice and simple that way.

Stephen Pridham did the major porting effort and was a big help when addressing certain difficult issues.
Stephen Saunders helped out with many Vulkan related rendering bugs. He also got it working with MoltenVk on macOS and improved support for AMD Vulkan users by integrating AMD's Vulkan Memory Allocator (VMA). He also contributed many other smaller bugfixes and improvements. Big thanks for both guys for all the help!
Also thanks to Alexey Panteleev at Nvidia who helped us through Discord and by pointing us into the right directions.

NVRHI will be also the gate opener for cross platform hardware accelerated Raytracing in the future so it is overall a big win for this source port.

There are also some immediate advantages for RBDOOM-3-BFG besides the smaller renderer backend.

### DX12 is faster than OpenGL

This is actually one of the most demananding scenes in Doom 3 with many shadow casting point lights. DX12 is now almost twice as fast as OpenGL. Vulkan is still lacking behind but DX12 is the primary API for Windows users which are the vast majority.
Stephen Saunders also provided triple buffering with NVRHI wich leads to a higher parallelization between the CPU and graphics card.

<img src="https://imgur.com/WWLN49t.jpg" width="384"> <img src="https://imgur.com/KrjppYU.jpg" width="384"> 

### Temporal Anti Aliasing

MSAA/SMAA have been replaced with a Temporal Anti Aliasing solution by Nvidia. This not only fixes geometric aliasing at the edges but also shader based aliasing within the geometries like extreme specular highlights caused by the new PBR shaders.

<img src="https://imgur.com/kQ5L37H.jpg" width="384"> <img src="https://imgur.com/bbRLVHg.jpg" width="384"> 

### New SSAO

The SSAO implementation has been updated with the latest code from Nvidia's Donut samples which outperforms the old implementation and also adds better stability on different view angles.
The old SSAO in 1.4.0 also lacked filtering and looked very noisy. The TAA would help but now it's really fixed and better.
It's hard to tell in these screenshots but the new SSAO gives most scenes a lot more depth.

<img src="https://imgur.com/GaUE8VM.jpg" width="384"> <img src="https://imgur.com/9UMVoY3.jpg" width="384"> 

## glTF 2 Support

<img src="https://imgur.com/nU13NP6.jpg" width="576">

Harrie van Ginneken contributed a new glTF 2 loader written from scratch using the awesome id Tech 4 idlib framework.
Also a big thanks here because this is huge and took a lot of time to develop.

The importer is not designed to work with all glTF 2 models you can find on the internet but it serves as a solid import facility from Blender. It can read ASCII .gltf and binary .glb models. It is recommend to use .glb for faster load times.
Models imported as .glb will be cached as .bglb models in the generated folder like all other model formats.
Cool about the current implementation is that animations are converted straight to the .bMD5Anim binary format of the Doom 3 BFG edition. This makes it a very tight integration into the existing architecture.

The basic idea about the glTF 2 `loader` is to make it easier getting models into the engine without any third party plugins in Blender. Another big disadvantage of .md5mesh exporters is that the format simply lacks support for normals.
So all normalmaps baked in Blender or Substance Painter using the Mikktspace algorithm would look wrong.
glTF 2 fixes this problem and it not only has support for normals but also tangents and those have to be calculated the Mikkelsen way so that's a perfect fit.

You can also use .glb files for static models without a skeleton just like with the original 3ds Max .ase and Lightwave .lwo files.
On top of that you can also use glTF 2 for new camera animations but that needs to be covered in an upcoming tutorial.

I also want to go back and keep things simple so I don't want to import and configure a model like in Unity or Unreal. I just want to load models and use them and the maximum should be to specify which frame plays a foot sound or which bone needs to be used to attach an item to it.

However it might be the case that someone exports a model from Blender and in a team environment someone else needs an option to load it differently. E.g. the front in Blender is -Y and forward in Doom is +X. So we need to rotate a model by 90 degrees around Z axis. All these things did the Maya .mb -> .md5mesh converter plugin for Doom 3 and we allow similar options in model declarations.

```
model character_mcneil
{
  // scale from meters to Doom units (inches), add an "origin" bone from rig origin to pelvis and 
  // build automatically a root motion from the pelvis bone
	options -scale 39.37 -addorigin -transfermotion "pelvis"
	
	mesh models/characters/mcneil/mcneil.glb
	
	channel legs ( origin *pelvis *foot_l *foot_r)
	
	anim walk models/characters/mcneil/mcneil.Walk.glb
	anim idle models/characters/mcneil/mcneil.Idle.glb
}
```

Supported options are:

* -armature `"armatureName"` if a glTF 2 file has more than one skeleton

* -reorient `pitch, yaw, roll`

* -rotate `yaw`

* -transferMotion `"boneName"` to enable root animations like in Unity

* -addorigin so we don't need put that extra "origin" bone in every Armature and can use common rigs like Mixamo or Rigify

* -scale `factor`

* -nomikktspace, specify if you want to use old normalmaps from an original Doom 3 model

```
NOTE: Material names in Blender need to be 1:1 the material names defined in the corresponding materials/*.mtr files.
```

Models are expected to be exported by Blender with the following settings:

```
Format: glTF Binary (.glb)
Transform: +Y Up On
Materials: Export
Images: None
Compression: Off
Data: Custom Properties On
Animation: Animation On, Shape Keys Off, Skinning On
```

## glTF 2 dmap Support and Blender Mapping Workflow

I rewrote dmap 8 years ago to compile a BSP from a polygon based map format instead of brushes. 
Now, Harrie van Ginneken has extended it so we can compile glTF 2 files.
The trick is to use custom properties in Blender and the engine treats the .glb file like a .map file.

There is too much to explain here and this needs to be covered in a tutorial series along with a new Blender addon for editing the entities. But this is quite cool. You can now make maps without TrenchBroom or a Radiant based editor. Theoretically it is possible to use any 3D modelling package as a level editor now.

Just save your map as .glb file and compile it like:

```
dmap mymap.glb
```

Harrie managed to port entire asset packs with animations and hundreds of meshes by using a few clever Python scripts within Blender.

Here is a small demo of his WIP game which shows GPU skinning using Mixamo animations and a working BSP with typical Doom 3 style portal culling:

[![WIP Space Game](https://img.youtube.com/vi/KdwInY0A_qs/0.jpg)](https://www.youtube.com/watch?v=KdwInY0A_qs)

Here is an another example map by Dmitri Engman that was done entirely in Blender. Kudos to him for pulling this off without documentation.

[![STACK ROCK DUNGEON](https://img.youtube.com/vi/8t7rzVGQScY/0.jpg)](https://www.youtube.com/watch?v=8t7rzVGQScY)

He also wrote a tutorial here: https://modwiki.dhewm3.org/RBDoom3BFG-Blender-Mapping#2-BLENDER-SET-UP

## Imgui Articulated Figure Editor

Stephen Pridham added a new Imgui based Articulated Figure editor.

<img src="https://imgur.com/atETc0r.jpg" width="576">

## Imgui Light Editor

The ImGui light editor, which you can spawn with editLights, has been slightly improved.
It shows the bounding boxes of the lights and you can move the lights with the arrow keys similar to TrenchBroom.

<img src="https://imgur.com/iYFLd2o.jpg" width="576">

## Support for OGG Vorbis Soundtracks

Like in modern Quake sourceports users can now just throw their favorite soundtrack into base/music/ like base/music/aqm/*.ogg or into a modfolder/music/ path.
The game will play automatically it in the background as a looping track until the end of the map.
Each map will pick a different track but mappers can also define a "music" track in the worldspawn entity.
You can also fine tune the volume of each track if you write a sound shader for your files.

There is an example in mod_alternativequakemusic/sound/_rbdoom_aqm_tracks.sndshd for the Alternative Quake Music sound track.

## Optick Profiler

The super lightweight C++ profiler Optick has been integrated. You need to recompile the engine with the CMake option -DOPTICK=ON but then you can run the profiler along with RBDOOM-3-BFG and make detailed captures about what is going on.
It's also very useful if you want to understand the multithreaded architecture of the BFG edition.

<img src="https://imgur.com/3OEbO0M.jpg" width="576">

## Borderless Fullscreen

Stephen Saunders contributed support for borderlesss fullscreen mode which can also be set by commandline with r_fullscreen -2. It's enabled automatically if you switch from windowed mode to fullscreen by using Alt + Enter. You can’t tell the difference visually. However, stick to borderless mode over fullscreen mode if you use multiple monitors. 
You can access your displays and launch various apps while your game or work stays on the main display.

This has the advantage that you can run RBDOOM-3-BFG simultanously along with TrenchBroomBFG, Blender, Krita or any other gamedev weapon of choice and it makes it easier to to switch between the applications.

But, there is a downside to borderless mode, depending on your device. 
Most operating systems like Windows, for example, will continue dedicating resources to all background processes. 
This means you might get less performance and experience input lag.
If you need to squeeze some extra FPS, go back to real fullscreen mode to see if it makes a difference.

## TrenchBroomBFG

The custom TrenchBroom build has been renamed to TrenchBroomBFG. This avoids conflicts with the original program like config files that are saved in the user directory. It also ignores all the other unsupported game profiles.
The new version has been updated to TrenchBroom 2023.1. It also supports the glTF 2 formats through the Assimp library but in this version the material loader has been tweaked to work directly with Doom 3 .mtr materials.
The icon size option also affects the entity inspector now which helps with high resolutions.


The engine command exportFGD was changed so it generates more variations of FGD files:

```
DOOM-3-all-and-models.fgd
DOOM-3-all.fgd
DOOM-3-multiplayer.fgd
DOOM-3-slim-and-models.fgd
DOOM-3-slim.fgd
```

If you switch to one of those with the additional autogenerated model entities then you can drag n drop meshes into your map without the models zoomap.


## Summarized Changelog

[RENDERING]

* Renderer uses DX12 & Vulkan instead of OpenGL

* Shaders are not compiled at runtime anymore. They are compiled in advance by CMake using the DXC shader compiler and distributed in binary form under base/renderprogs2/dxil/*.bin

* All shaders have been rewritten to proper HLSL

* MSAA/SMAA have been replaced with a temporal anti aliasing (TAA) solution by Nvidia. This not only fixes geometric aliasing but also shader based aliasing like extreme specular highlights by the PBR shaders.

* Tone mapping has been replaced using new shaders by Nvidia

* Removed stencil shadow volumes with additional fixes by icecoldduke

* Integrate the AMD's VMA allocator for images and buffer objects

* Shadow mapping uses a fat shadowmap atlas instead of switching between shadowmap buffers and the HDR render target for each light. This improves the speed of the offscreen shadowmap generation.

* Blend shadowmaps smoothly in for small distant lights

* Skip shadowmaps for small distant lights

* Added menu option to choose between DX12 and Vulkan

* Detect displays that only support 144, 165 and 240 Hz

* Show picked GPU with com_showFPS > 1

* Went back to 15 bits for VERTCACHE_FRAME_MASK to avoid weird model distortions in a single frame every 68 seconds

* Reverted com_smp back to CVAR_BOOL

* Removed obsolete cvar r_useHDR

* Removed obsolete cvar r_useSeamlessCubeMap

* Removed unused cvar r_useHalfLambert

* Show shadow atlas stats with com_showFPS 3

* Enable DX12 / Vulkan debug layers with r_useValidationLayers 2

[DMAP]

* Dmap: always write a .cm file, especially when overwriting from a mod dir

* Support the Valve 220 texture projection in MapPolygonMesh::ConvertFromBrush()

* Automatically remove map collision .cm, .bcm files before running dmap

* Changed dmap to support compiling maps from glTF 2 .glb models instead of .map files

* Fixed AAS compiler to work with maps compiled from glTF 2 .glb files

* Tweaked dmap -glview option to dump an .obj next to the .proc file with similar content and to print an ASCII art BSP tree in the proc file

[MISCELLANEOUS]

* Don't generate collision models for every rendermodel in advance. Restored vanilla Doom 3 behaviour.

* Crashfix: Don't refesh the screen using prints during engine shutdown

* Print engine version when starting to write a qconsole.log

* Improved Quake .map converter to get Makkon's samplemaps working

* Bumped the required C++ standard to C++14

* Add move semantics to idList and idStr (thanks Admer)
	* idListArrayResize uses std::move
	* idStr implements move constructor
	* and move operator
	* mpMap_t also implements a move operator

* Fix snprintf() buffer length issues for Doom Classic on Linux with GCC 12 compiler

* Fix ImGui include path handling to be consistent with other external libs

* Applied MD3 crashfix by Daniel Gibson

* Restore support for FFMPEG legacy channel_layout & channels fields, remove self-assignment in idSoundDecoder_Vorbis::Open()

* Update FFMPEG deprecated calls to use supported ch_layout structure

* R_TestVideo_f command fixes and Cinematics startup fix for NVRHI (BinkDec green frame)

* Add s_playCinematicAudio cvar to enable/disable cinematic audio playback

* Support mp4/webm/mkv cinematics for mods, fix duration calculation, fix testVideo shutdown on exit

* Tweaked exportFGD to output more FGDs with autogenerated model definitions

* Fixed missing shadows in the kitchen of Mars City 1

* Fixed fake parallel light in Mars City 1 opening scene

* Integrated Optick profiler. Needs CMake option -DOPTICK=ON

* LightEditor: Draw volume of selected light and move it with arrow keys

* Support Borderless Fullscreen and Borderless Window modes for Win32 and SDL platforms

* Fixed crash when executing extract_resources.cfg on Linux

[TRENCHBROOMBFG]

* Renamed custom TrenchBroom fork to TrenchBroomBFG

* Change TrenchBroomBFG to only load Doom 3 related game profiles as it breaks compatibility to other Quake based games

* TrenchBroomBFG code was updated with the stable TrenchBroom 2023.1 release which contains tons of bugfixes and improvements

* If you turn off certain brushes like visportals in the View Options you won't accidently select by clicking to brushes behind them

* TrenchBroomBFG uses the Assimp library for glTF2 support

* func_door FGD fix

* Use Icon size preferences not only in the textures browser but also in the entity browser

* FGD proxymodel evaluation fix #4052

* Use fixed size (-8 -8 0, 8 8 16) bounds for all model based entities

* Fixed missing support for the basecolormap keyword in .mtr files





_______________________________________

06 March 2022 - RBDOOM-3-BFG 1.4.0
_______________________________

<img src="https://i.imgur.com/3sUxOZi.jpg">

<img src="https://i.imgur.com/ez4M4PE.jpg">

<img src="https://i.imgur.com/8j4VmuR.jpg">

## .plan

This version improves support for mapping with TrenchBroom. Until now you needed to extract and copy the vanilla Doom 3 models and textures over to the base/ folder to see the content in the TrenchBroom entity browser and texture viewer.
Owning the original game next to the BFG edition is not necessary anymore.
This version comes with a couple of new RBDOOM-3-BFG console commands that lets you export particular parts of the .resources files to the base/_tb/ folder.

You need to call exportImagesToTrenchBroom once and you are good to go to start mapping with the TrenchBroom level editor.

TrenchBroom comes with several more Doom 3 specific changes. After loading a map TrenchBroom generates unique entity names and also fixes missing or bad "model" keys for brush based entitites.
Also creating new entities like light will automatically receive names like light_2.

This patch also contains a couple of func_group related bugfixes. func_group works now with brush based entities, point entities and just regular brushes.

## Changelog

[TRENCHBROOM]

* Tweaked exportFGD command for new icons

* Added new icons to TrenchBroom for certain entities like lights, speakers or particle emitters

* TrenchBroom offers a dropdown menu to select the Quake 1 light style for lights

* Drastically improved loading time of textures for materials in TrenchBroom

* Added RBDoom console command convertMapToValve220 `<map>`. You can also type `exec convert_maps_to_valve220.cfg` to convert all Doom 3 .map files into the TrenchBroom friendly format. Converted maps are saved with the _valve220.map suffix.

* Added RBDoom console command exportImagesToTrenchBroom which decompresses and saves all .bimage images to base/_tb/*.png files

* Added RBDoom console command exportModelsToTrenchBroom which saves all .base|.blwo|.bmd5mesh models to _tb/*.obj proxy files. This commands also generates helper entities for TrenchBroom so all mapobject/models are also available in the Entity Inspector using the DOOM-3-models.fgd.

* Added RBDoom console command makeZooMapForModels which makes a Source engine style zoo map with mapobject/models like .blwo, .base et cetera and saves it to maps/zoomaps/zoo_models.map. This helps mappers to get a good overview of the trememdous amount of custom models available in Doom 3 BFG by sorting them into categories and arranging them in 3D. It also filters models so that only modular models are picked that can be reused in new maps.

* TrenchBroom got several Doom 3 specific issue generators to help mappers avoiding pitfalls during mapping

* Changed TrenchBroom's rotation tool to use the "angles" key by default to remove some Quake related limitations


[MISCELLANEOUS]

* Stencil shadows work again (thanks to Stephen Pridham) 

* Fixed black screen after using the reloadImages command

* Added CMake options STANDALONE and DOOM_CLASSIC

* Added command convertMapQuakeToDoom `<map>` that expects a Quake 1 .map in the Valve220 format and does some Doom 3 specific fixes

* The gamecode ignores func_group entities if they were created by TrenchBroom instead to warn that there is no spawn function

* dmap / idMapFile move brushes of func_group entities to worldspawn before compiling the BSP. 
  This also means func_group brushes are structural. 
  If you want to optimize the BSP then move those to func_static instead which is the same as func_detail in Quake.
  
* Fixed that dmap failed writing the BSP .proc file if the command was interrupted by an error

[COMMUNITY]

Steve Saunders contributed

* Updated mac OS support

* Improved Vulkan / Molten support

* Fixed FFmpeg 5 compatibility for newer Linux distros

* Bink videos can play audio if they contain audio tracks (merged from DOOM BFA by Mr.GK)


[ASSETS]

* Added TrenchBroom helper entityDefs like a Quake 3 style misc_model to comply with TrenchBroom's Solid/PointClass rules for editing entities

* Added new Creative Commons CC0 textures/common/ and textures/editor/ replacement textures because they didn't ship with the BFG edition

* Added base/convert_maps_to_valve220.cfg which lets you convert all maps to the Valve 220 .map format in one shot

* Added base/maps/zoomaps/zoo_models.map



_______________________________________

30 October 2021 - RBDOOM-3-BFG 1.3.0 - Download it from the [RBDOOM-3-BFG ModDB Page](https://www.moddb.com/mods/rbdoom-3-bfg) 
_______________________________

<img src="https://i.imgur.com/ykY9tMs.png">

# RBDOOM-3-BFG 1.3.0 adds PBR, Baked GI and TrenchBroom Mapping Support

The main goal of this 1.3.0 release is enabling modders the ability to make new content using up to date Material & Lighting standards. Adding PBR is a requirement to make the new content look the same in RBDOOM-3-BFG as in Blender 2.9x with Cycles or Eevee and Substance Designer. PBR became the standard material authoring since 2014. Many texture packs for Doom 3 like the Wulfen & Monoxead packs were made before and are heavily outdated. With this release modders can work with modern tools and expect that their content looks as expected.

However the PBR implementation is restricted to standard PBR using the Roughness/Metallic workflow for now.
Specialized rendering paths for skin, clothes and vegetation will be in future releases.

## Physically Based Rendering

Implementing Physically Based Rendering (PBR) in Doom 3 is a challenge and comes with a few compromises because the Doom 3 content was designed to work with the hardware constraints in 2004 and that even meant to run on a Geforce 3.

The light rigs aren't made for PBR but it is possible to achieve good PBR lighting even with the old content by tweaking the light formulars with a few good magic constants. However I also want to support the modding scene to allow them to create brand new PBR materials made with Substance Designer/Painter or other modern tools so multiple rendering paths have been implemented.

PBR allows artists to create textures that are based on real world measured color values and they look more or less the same in any renderer that follows the PBR guidelines and formulars.

***RBDOOM-3-BFG only supports the standard PBR Roughness/Metallic workflow.***

## Baked Global Illumination using Irradiance Volumes and Image Based Lighting

*To achieve the typical PBR look from an artistic point of view it also means to that it is necessary to add indirect lighting.
Doom 3 and even Doom 3 BFG had no indirect lighting.*

Doom 3 BFG is a big game. Doom 3, Resurrection of Evil and Lost Missions sum up to 47 big single player levels with an average of ~60 - 110 BSP portal areas or let's call them rooms / floors. Each room can have up to 50 shadow casting lights and most of them are point lights.
I needed a good automatic solution that fixes the pitch black areas without destroying the original look and feel of the game.
I also needed to add environment probes for each room so PBR materials can actually reflect the environment.

So RBDOOM-3-BFG comes with 2 systems to achieve this and both are automatic approaches so everything can be achieved in a reasonable amount of time.
The first system are environment probes which are placed into the center of the rooms. They can also be manually tweaked by adding env_probe entities in the maps. They use L4 spherical harmonics for diffuse reflections and GGX convolved mip maps for specular reflections.
The second system refines this by using a light grid for each room which provides a sort of a localized/improved version of the surrounding light for each corner of the room.

### Irradiance Volumes aka Light Grids

RBDOOM-3-BFG 1.3.0 brings back the Quake 3 light grid but this time the grid points feature spherical harmonics encoded as octahedrons and it can be evaluated per pixel. This means it can be used on any geometry and serves as an irradiance volume.
Unlike Quake 3 this isn't radiosity which is limited to diffuse only reflections. The diffuse reflectivity is built using all kinds of incoming light: diffuse, specular and emissive (sky, light emitting GUIs, VFX).

<img src="https://i.imgur.com/DKoBaP6.png" width="384"> <img src="https://i.imgur.com/Yrhh28g.png" width="384">

Lightgrids can be baked after loading the map and by typing:

```
bakeLightGrids [<switches>...]
<Switches>
 limit[num] : max probes per BSP area (default 16384)
 bounce[num] : number of bounces or number of light reuse (default 1)
 grid( xdim ydim zdim ) : light grid size steps into each direction (default 64 64 128)
```

This will generate a ***.lightgrid*** file next to your .map file and it will also store a light grid atlas for each BSP area under ***env/maps/<path/to/your/map/>***

<img src="https://i.imgur.com/HeXnVLs.jpg" width="640">

Limit is 16384 by default and means the maximum number of light grid points in a single light grid.
Quake 3 had one light grid that streched over the entire map and distributed lighting every 64 x 64 x 128 units by default.
If the maps were too big then q3map2 made the default grid size broader like 80 x 80 x 144, 96 x 96 x 160 and so on until the maximum number of light grid points was reached.

The Quake 3 approach wouldn't work with Doom 3 because the maps are too big and it would result in up to 800k probes for some maps or the grid density would very coarse.

RBDOOM-3-BFG uses the bounding size of the BSP portal areas and puts smaller light grids into those BSP areas.

<img src="https://i.imgur.com/pTR06dH.png" width="640">

This way we can maintain a good grid density and avoid wasting storage because of light grid points that are in empty space.

> But what is an Irradiance Volume or Light Grid exactly?

It tells each model or lit pixel how the indirect diffuse lighting is coming from any direction. The Probulator page by Yuri O'Donnell has some good examples:

Left: The captured view using a panorama layout. Right: The Diffuse lighting using Level 4 Spherical Harmonics aka Irradiance.
<img src="https://i.imgur.com/4i52c4k.png" width="384"> <img src="https://i.imgur.com/Qq2HYuK.png" width="384">

Now think of this for each of the grid points in RBDOOM-3-BFG. If a model is placed between those probes the lighting will be interpolated by the nearest 8 grid points similar like in Quake 3.

Quake 3 only stored the dominant light direction, the average light color of that direction and an ambient color term for each grid point.
In RBDOOM-3-BFG you basically can have the diffuse lighting information for **any** world space direction instead.
This is a way more advanced technique.

### Image Based Lighting and Environment Probes

Environment probes supplement the light grids. While light grids provide diffuse lighting information the signal isn't good enough to provide plausible specular light reflections. This is where environment probes are needed.

If a level designer doesn't put any env_probe entities into a map then they are automatically distributed through the map using the BSP area bounds and placed in the center of them.

Environment probes can be computed after loading the map and by typing:
```
bakeEnvironmentProbes
```

This will generate EXR files in ***env/maps/<path/to/your/map/>***.

The environment probes use an octahedron encoding and the specular mipmaps are convolved using the Split Sum Approximation by Brian Karris [Epic 2013]. Convolving the environment probes is done on the CPU using the id Tech 5 multi threading code so it will consume all your available cores.

For artists this basically means if you increase the roughness in your material then you increase the mip map level of the environment probe it samples light from and it gets blurier.

### Fallback for Missing Data

If you haven't downloaded the additional baked light data from the [RBDOOM-3-BFG ModDB Page](https://www.moddb.com/mods/rbdoom-3-bfg) and just run RBDOOM-3-BFG.exe with the required DLLs (or you built it yourself) it will use an internal fallback.
RBDOOM-3-BFG.exe has one prebaked environment probe that is compiled into the executable.

<img src="https://i.imgur.com/Q9ONWaq.jpg" width="384"> <img src="https://i.imgur.com/tM0aEIV.png" width="384"> 

It's the light data from the Mars City 1 lobby in the screenshot above. Using this data for the entire game is inacurrate but a better compromise than using a fixed global light direction and some sort of Rim lighting hack like in version 1.2.0.
The default irradiance / radiance data gives the entire game a warmer look and it fits for being on Mars all the time.


### Some Examples of Indirect Lighting

<img src="https://i.imgur.com/DqTEbzU.jpg" width="384"> <img src="https://media.moddb.com/images/mods/1/50/49231/rbdoom-3-bfg-20210409-221842-001.png" width="384">

Left: No extra ambient pass. Ambient is pure black like in original Doom 3. Right: Extra ambient pass with r_forceAmbient 0.5 using local environment probe for irradiance and radiance lighting.

<img src="https://i.imgur.com/ZEI4i87.png" width="384"> <img src="https://i.imgur.com/FC82oOM.png" width="384">

<img src="https://i.imgur.com/LRJBJwV.png" width="384"> <img src="https://i.imgur.com/GPD2aIr.png" width="384">

<img src="https://i.imgur.com/PVAXGui.png" width="384"> <img src="https://i.imgur.com/NleLuWY.png" width="384">

<img src="https://i.imgur.com/vxAgY2S.png" width="384"> <img src="https://i.imgur.com/8avH7DY.png" width="384">

<img src="https://i.imgur.com/KESmZld.png" width="384"> <img src="https://i.imgur.com/lHc7Pb9.png" width="384">

Some examples that show additional environment lighting on all assets.

<img src="https://i.imgur.com/xBPa2Y8.png" width="384"> <img src="https://i.imgur.com/MCjwFE7.png" width="384">

## PBR Texture format

<img src="https://i.imgur.com/j8nYYls.png" width="640">

In Doom 3 a classic simple materials looks like this:
```
textures/base_wall/snpanel2rust
{
  qer_editorimage		textures/base_wall/snpanel2rust.tga
	
  bumpmap           textures/base_wall/snpanel2_local.tga
  diffusemap        textures/base_wall/snpanel2rust_d.tga
  specularmap       textures/base_wall/snpanel2rust_s.tga
}
```

It's usually rendered with Blinn-Phong specular with a fixed specular exponent.
Specularmaps are more or less Gloss maps.

In RBDOOM-3-BFG it uses the PBR GGX Cook-Torrence formular and you can vary the roughness along the material like in other modern engines and you usually define a texture with the _rmao suffix.

RMAO Image Channels             | Description
:-----------------------------  | :------------------------------------------------
Red                             | Roughness
Green                           | Metalness
Blue                            | Ambient Occlusion

Example material:
```
models/mapobjects/materialorb/orb
{
  qer_editorimage   models/mapobjects/pbr/materialorb/substance/metal04_basecolor.png
  
  basecolormap      models/mapobjects/pbr/materialorb/substance/metal04_basecolor.png
  normalmap         models/mapobjects/pbr/materialorb/substance/metal04_normal.png
  specularmap       models/mapobjects/pbr/materialorb/substance/metal04_rmao.png
}
```

The engine will also look for _rmao.[png/tga] overrides for old materials and if it finds those it will render them using a better PBR path. Old school specularmaps also go through a GGX pipeline but the roughness is estimated from the glossmap.

The Ambient Occlusion will be mixed with the Screen Space Ambient Occlusion and will only affect indirect lighting contributed by the environment probes.

## Filmic Post Processing

TL;DR If you enable it then you play DOOM 3 BFG with the optics of a Zack Snyder movie.

This release adds chromatic abberation and filmic dithering using Blue Noise.
The effect is heavy and is usually aimed in Film production to mix real camera footage with CG generated content.

Dithering demonstration: left side is quantized to 3 bits for each color channel. Right side is also only 3 bits but dithered with chromatic Blue Noise. The interesting fact about the dithering here is shown in the upper debug bands.
The first top band is the original signal. The second shows just 8 blocks and if you dither the those blocks with Blue Noise then it is close to the original signal which is surprising.

<img src="https://i.imgur.com/QJv2wH2.png" width="384"> <img src="https://i.imgur.com/MaXqld4.png" width="384">



## TrenchBroom Mapping Support

<img src="https://i.imgur.com/tIj6wpd.jpg" width="640">

***This is still very much Work in Progress and not supported by the official TrenchBroom.***

Mapping for Doom 3 using TrenchBroom requires an extended unofficial build that is bundled with the official RBDOOM-3-BFG 7z package.
You can find the customized version under tools/trenchbroom/.

Doom 3 also requires some extensions in order to work with TrenchBroom. I usually develop these things for RBDOOM-3-BFG but I created a seperated brach for vanilla Doom 3 so everybody can adopt TrenchBroom and the new Doom 3 (Valve) .map support.

https://github.com/RobertBeckebans/DOOM-3/tree/506-TrenchBroom-interop

The goal of the TrenchBroom support is to make it easier to create new maps. It doesn't allow to create bezier patches at the moment so you won't be able to edit existing Doom 3 maps.

You can only save maps to the Doom 3 Valve format but you can copy paste from the vanilla Doom 3 .map format into the Doom 3 (Valve)configuration and reset your texture alignment as you want.
TrenchBroom doesn't support brush primitives like in D3Radiant or DarkRadiant and if you are familiar with TrenchBroom then you know that the preferred .map format is some kind of (Valve) .map format for your game.

The Quake 1/2/3 communities already adopted the Valve .map format in the BSP compilers and I did the same with dmap in RBDOOM-3-BFG.

Here is an overview of the changes made to TrenchBroom:

***New***
* Doom 3 .map parser with brushDef3, patchDef2, patchDef3 primitives
* Doom 3 Valve .map configuration
* Quake 3 .shader parser adopted to support .mtr
* .mtr support includes support for Doom 3 diffuse stages and the lookup for them is like in idMaterial::GetEditorImage()
* New Doom 3 OBJ parser. My TB Interop branch automatically creates OBJ files to work with TB and it also allows seamless interop with Blender 2.8x and 2.9x with the need of additional model formats for func_static entities (like misc_model for Quake 3)
* Game FGDs for Doom 3 and Doom 3 BFG

***Issues***
* It has no support for BFG .resource files and .bimage files. BFG only shipped for precompressed textures and no .tga files so people who want to mod for BFG have to copy the vanilla D3 base/textures/* and base/models/* to D3BFG/base/
* Many entities work differently in Doom 3 if they have an origin. Brush work in D3 is usually stored in entity space and not world space. This is a major issue and not solved. I couldn't figure out how to parse the origin first and then translate the brushes accordingly.
* Doom 3's primary model formats are LWO and ASE. LWO and .md5mesh model support is missing.
* Some ASE models can't be loaded and materials are usually all wrong if loaded

### Some Scenes of Mars City 1 loaded into TrenchBroom
<img src="https://i.imgur.com/nqR04z8.jpg" width="384"> <img src="https://i.imgur.com/GxL1X02.jpg" width="384">


## General Changelog

[PBR]

* r_useHDR 1 is now the default mode and can't be turned off because it is a requirement for Physically Based Rendering

* Changed light interaction shaders to use PBR GGX Cook-Torrance specular contribution. The material roughness is estimated by the old school Doom 3 glossmap if no PBR texture is provided

* PBR texture support

* Extended ambient pass to support Light Grids and Environment Probes

* Turned off Half-Lambert lighting hack in favor of Image Based Lighting

* Improved r_hdrDebug which shows F-stops like in Filament

* Turned off r_hdrAutomaticExposure by default because it caused too much flickering and needs further work. The new default values work well with all Doom 3 content

* r_lightScale (default 3) can be changed

* Light shaders were tweaked with magic constants that r_forceAmbient 0.5, r_exposure 0.5 look as bright as in Doom 2016 or any other PBR renderer

* SSAO only affects the ambient pass and not the direct lighting and the ambient occlusion affects the specular indirect contribution depending on the roughness of a material. See moving Frostbite to PBR (Lagarde2014)

* Added HACK to look for PBR reflection maps with the suffix _rmao if a specular map was specified and ends with _s.tga. This allows to override the materials with PBR textures without touching the material .mtr files.

* Fixed ambient lights, 3D GUIs and fog being too bright in HDR mode

* Fixed darkening of the screen in HDR mode when you get hit by an enemy

* Fixed ellipse bug when using Grabber gun in HDR mode #407

* Added LoadEXR using TinyEXR, LoadHDR using stb_image

* Optimized R_WriteEXR using TinyEXR compression

* Added Spherical Harmonics math from Probulator by Yuri O'Donnell

* Added Octahedron math by Morgan McGuire - http://jcgt.org/published/0003/02/01/

[VULKAN]

* **NOTE THE VULKAN BACKEND IS STILL NOT FINISHED!!!**

* Fixed GPU Skinning with Vulkan

* Fixed the lighting with stencil shadows with Vulkan and added Carmarck's Rerverse optimization

* Added anisotropic filtering to Vulkan

* Added Git submodule glslang 7.10.2984 -> stable release Nov 15, 2018

* Vulkan version builds on Linux. Big thanks to Eric Womer for helping out with the SDL 2 part

* Fixed Bink video playback with Vulkan

* ImGui runs with Vulkan by skipping all Vulkan implementation details for it and rendering ImGui on a higher level like the Flash GUI

[TRENCHBROOM]

* idMapFile and dmap were changed to support the Valve 220 .map format to aid mapping with TrenchBroom

* Added exportFGD [models] console command which exports all def/*.def entityDef declarations to base/exported/_tb/ as Forge Game Data files. TrenchBroom has native support to read those files https://developer.valvesoftware.com/wiki/FGD.
Using the models argument will also export all needed models by entity declarations to base/_tb/ as Wavefront OBJ files.

* To make it easier getting static models from Blender/Maya/3D Studio Max into TrenchBroom and the engine Wavefront OBJ model support has been ported from IcedTech

* Support ***angles*** keyword again for TrenchBroom like in Quake 3

[MISCELLANEOUS]

* com_showFPS bigger than 1 uses ImGui to show more detailed renderer stats like the original console prints with r_speeds

* Removed 32bit support: FFmpeg and OpenAL libraries are only distributed as Win64 versions, 32bit CMakes files are gone

* Added Blue Noise based Filmic Dithering by Timothy Lottes and Chromatic Aberration

* Added Contrast Adaptive Sharpening (AMD) by Justin Marshall (IcedTech)

* Improved Shadow Mapping quality with Vogel Disk Sampling by Panos Karabelas and using dithering the result with Blue Noise magic by Alan Wolfe

* Improved Screen Space Ambient Occlusion performance by enhancing the quality with Blue Noise and skipping the expensive extra bilateral filtering pass

* Updated idRenderLog to support RenderDoc and Nvidia's Nsight and only issue OpenGL or Vulkan debug commands if the debug extensions are detected. Reference: https://devblogs.nvidia.com/best-practices-gpu-performance-events/

* Artistic Style C++ beautifier configuration has slightly changed to work closer to Clang Format's behaviour

* Updated documentation regarding modding support in the README

* Bumped the static vertex cache limit of 31 MB to roughly ~ 128 MB to help with some custom models and maps by the Doom 3 community

* Added support for Mikkelsen tangent space standard for new assets (thanks to Stephen Pridham)

* Renamed r_useFilmicPostProcessEffects to r_useFilmicPostProcessing

* Replaced Motion Blur System Option with Filmic VFX (r_useFilmicPostProcessing)

* Windows builds still require OpenGL 4.5 but they run in compatibility profile instead of core profile

* Initial support for the MIPS64 architecture (thanks to Ramil Sattarov)

* Initial support for the LoongArch64 architecture (thanks to Ramil Sattarov)

* Initial support for the PPC64 architecture (thanks to Trung Lê)

* Initial support for the Raspberry Pi 4 (thanks to Alejandro Piñeiro)

* Updated Mac OS support (thanks to Steve Saunders)

* Improved console layout (thanks to Justin Marshall)

* Added invertGreen( normalmap.png ) material keyword to allow flipping the Y-Axis for tangent space normal maps

* Changed to sys_lang to be saved to config so it has to be set per cmdline only a single time

* Fixed bug that GOG builds default to Japanese instead of English

* Support for Steam and GOG base path detection for Windows if sys_useSteamPath or sys_useGOGPath is set to 1 (default 0)

* Changed CMake MSVC setup to enable debugging without manually changing paths (thanks to Patrick Raynor)

* Allow _extra_ents.map files next to the original .map files so new entities can be added to existing maps or old entities can be tweaked with new values

* Fixed DPI Scaling problems for 4K screens

[ASSETS]

* This release is the first time that contains changes to the base/ folder and is not a pure executable patch

* base/materials/*.mtr contain the Doom 3 BFG material files with some minor fixes so TrenchBroom can load them

* base/def/*.def are the Doom 3 BFG entity definition files so DarkRadiant is functional

* base/maps/game/mars_city1_extra_ents.map contains a fog light fix in the first hangar scene

* base/maps/game/*_extra_ents.map files contain additional env_probe entities and light entity tweaks

* base/maps/game/*.lightgrid files and base/env/game/<map>/*.exr contain the new precomputed PBR light data

* New entity definition env_probe

* New material textures/common/origin so mappers can create brushes that set the entity origin

* Changed light entity definition to support Quake 1 light styles

_______________________________________

October 2019 - RBDOOM-3-BFG 1.2.0
_______________________________

This is a maintenance release without Vulkan support even though it contains a lot Vulkan specific code.

* Experimental Work in Progress Vulkan renderer backend based on Dustin Land's Doom 3 BFG Vulkan port
  This renderer backend only supports ambient lighting and no light interactions but it also comes with a big renderer code cleanup.

  The changes also fixed several rendering bugs that occured in OpenGL:
  
* Fixed black dots when HDR and SSAO is enabled

* Hit/damage markers show a white background #422

* Refactored and simplified OpenGL renderer backend to match Vulkan backend

* Renamed the .vertex and .pixel shader files to .hlsl
  This allows better editing of the shaders within Visual Studio including Intellisense support.

* Integrated libbinkdec for video playback as a slim alternative to FFmpeg (thanks to Daniel Gibson)

* Added in-engine Flash debugging tools and new console variables.
  These tools help to analyse the id Tech view of Flash and what SWF tags are supported and how they are interpreted
  by id Tech's own ActionScript 2 interpreter
	- postLoadExportAtlas
	- postLoadExportSWF
	- postLoadExportJSON
	- swf_show : Draws the bounding box of instanced Flash sprites in red and their names

* Added Steel Storm 2 Engine render demo fixes

* Merged LordHavoc's image compression progress bar which shows up in the map loading screen
  when loading and compressing new images from mods
  
* Added instructions how to use these Doom 3 port with the GOG installer

* Many smaller compiler related fixes like VS 2017 and VS 2019 support.
  If it fails to compile with GCC then it should at least build with Clang on Linux

* Initial support for the E2K (MCST Elbrus 2000) architecture (thanks to Ramil Sattarov)

_______________________________________

11 April 2016 - RBDOOM-3-BFG 1.1.0 preview 3
_______________________________

* True 64 bit HDR lighting with adaptive filmic tone mapping and gamma-correct rendering in linear RGB space

* Enhanced Subpixel Morphological Antialiasing
	For more information see "Anti-Aliasing Methods in CryENGINE 3" and the docs at http://www.iryoku.com/smaa/
* Filmic post process effects like Technicolor color grading and film grain

* Fixed issues with Mesa drivers and allowed them to use shadow mapping

* Defaulted fs_resourceLoadPriority to 0 so it is not necessary anymore to specify when running a modification

* Additional ambient render pass to make the game less dark similar to the Quake 4 r_forceAmbient technique

* Screen Space Ambient Occlusion http://graphics.cs.williams.edu/papers/SAOHPG12/

* Did some fine tuning to the Half-Lambert lighting curve so bump mapping doesn't loose too much details


_______________________________________

7 March 2015 - RBDOOM-3-BFG 1.0.3
_______________________________

* SDL 2 is the default for Linux

* CMake options like -DUSE_SYSTEM_LIBJPEG to aid Linux distribution package maintainers

* SDL gamepad support and other SDL input improvements like support for more mouse buttons

* Mac OS X support (experimental)

* XAudio 2, Windows 8 SDK fixes

* Better Mesa support (not including advanced shadow mapping)

* Added back dmap and aas compilers (mapping tools)

* Cinematic sequences can be skipped

* Localization support for other languages than English

* Improved modding support through loading of custom models and animations (see section 12 MODIFICATIONS in the README)



_______________________________________

17 May 2014 - RBDOOM-3-BFG 1.0.2

RBDOOM-3-BFG-1.0.2-ATI-hotfix1-win32-20140517-git-952907f.7z
_______________________________

This release doubles the shadow mapping performance and allows the game to be played at solid 120 fps in 1080p with a Nvidia GTX 660 Ti.
It also adds a graphics option to enable/disable shadow mapping and fixes a few problems with FFmpeg playing Bink videos.



_______________________________________

11 May 2014 - RBDOOM-3-BFG 1.0.1

RBDOOM-3-BFG-win32-20140511-git-f950769.7z
_______________________________

This release improves the performance drastically and adds shadow mapping support for translucent surfaces like grates.


_______________________________________

10 May 2014 - RBDOOM-3-BFG 1.0

RBDOOM-3-BFG-win32-20140510-git-14f87fe.7z
_______________________________

* Added soft shadow mapping

* Added PNG image support

* Replaced QGL with GLEW

* base/renderprogs/ can be baked into the executable using Premake/Lua

* Replaced Visual Studio solution with CMake

* Linux port using SDL/SDL2 and OpenAL

* 64 bit support for Windows and Linux

* Sourcecode cleanup using Artistic Style 2.03 C++ beautifier and by fixing tons of warnings using Clang compiler

* Fast compile times using precompiled header support

* Tons of renderer bugfixes and Cg -> GLSL converter that supports ES 2.00, ES 3.30 additional to GLSL 1.50

* Netcode fixes to allow multiplayer sessions to friends with +connect <ip of friend> (manual port forwarding required)






