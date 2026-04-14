# File Formats

## oct
Tupperware container files for model metadata and textures. Use `offsetting oct decode` via the [offsetting](https://github.com/offsetting/offsetting) tool suite to convert them into JSON or YAML, and `offsetting oct encode` to rebuild them. Append `-t` to the end of the command to extract or embed textures. You take a deeper dive into the `.oct` format [here](oct-format.md).

## mer
Meridian tree structures, stored as Tupperware. Use `offsetting` to decode them into standard JSON or YAML.

## vbuf
Stands for Vertex Buffer. Used to store raw vertex data in a format convenient for the rendering engine. Metadata from an `.oct` file is required to properly parse these files.

## ibuf
Stands for Index Buffer. Used to store raw index data in a format convenient for the rendering engine. Metadata from an `.oct` file is required to properly parse these files.

## dct
Localization dictionaries. Use `offsetting dct unpack` to decode them into standard JSONs.

## zip
ZIP Archives, with extra metadata embedded inside used by the engine's filesystem. Use the `zip` or `zip-c2` tool included in [offsetting](https://github.com/offsetting/offsetting) to re-pack archives in a game-friendly format.

## dds
Microsoft DirectDraw Surface textures. Can be viewed and edited with most image manipulation tools such as GIMP, Paint.NET, and Adobe Photoshop.

## tfx
Groups of compiled HLSL (High-Level Shader Language) shaders, stored inside Tupperware files. Use `offsetting oct decode` to decode them into standard JSON or YAML.

## swf
Standard Shockwave Flash files. Used by the game's Autodesk Scaleform-backed UI system.

## anno 
Modified version of an Annosoft Lipsync Tool output file. Contains phonemes alongside timestamps that the game uses to lipsync characters mouths to audio.
