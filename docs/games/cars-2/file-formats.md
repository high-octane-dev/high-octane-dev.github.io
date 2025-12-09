# File Formats

## oct
Tupperware container files for model metadata and textures. Use `oct decode` with the `offsetting` tool to convert them into JSON or YAML, and `oct encode` to rebuild them. Append `-t` to the end of the command to extract or embed textures.

## mer
Meridian tree structures, stored as Tupperware. Use `offsetting` to decode them into standard JSON or YAML.

## vbuf
Stands for Vertex Buffer. Used to store raw vertex data in a format convinent for the rendering engine. Metadata from an `oct` file is required to properly parse these files.

## ibuf
Stands for Index Buffer. Used to store raw index data in a format convinent for the rendering engine. Metadata from an `oct` file is required to properly parse these files.

## dct
Localization dictionaries. Use `indctive` to decode them into standard JSONs.

## zip
ZIP Archives, with extra metadata embedded inside used by the engine's filesystem. Use the `why` tool included in the Python package `c2ditools` to re-pack archives in a game-friendly format.

## dds
Microsoft DirectDraw Surface textures. Can be viewed and edited with most image manipulation tools such as GIMP, Paint.NET, and Adobe Photoshop.

## tfx
Groups of compiled HLSL (High-Level Shader Language) shaders, stored inside Tupperware files. Use `matryoshka` to decode them into standard JSON or YAML.

## swf
Standard Shockwave Flash files. Used by the game's Autodesk Scaleform-backed UI system.

## anno 
Modified version of an Annosoft Lipsync Tool output file. Contains phonemes alongside timestamps that the game uses to lipsync characters mouths to audio.
