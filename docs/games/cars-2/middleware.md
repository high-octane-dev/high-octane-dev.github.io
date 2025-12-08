# Middleware software used in Cars 2 The Video Game

## Scaleform GFx 3.3.88
The Autodesk Scaleform GFx is used to render the user interface using  `.swf` files that are contained within the `/assets/flash/flash.zip` file. The JPEX Free Flash Decompiler can be used to view and edit the existing `.swf` files in the game. Adding additional menus requires modifying the game via a Pentane plugin.

## FMOD Designer Ver. 4.32.9 (Arcade) / 4.32.8 (Win32Wii)
FMOD Designer was used to create and control the interactive audio system in the game, this includes sound effects, ambience, and event-based audio behaviors. The resulting project data is compiled and loaded through the `FMOD API` at runtime, while the `FSB` file can be edited (This contains the raw audio data) the `FEV` file is compiled and cannot be modified at this time, this means no editing of the events themselves.

## FMOD API
The FMOD API refers to the DLL files used in the games, this refers to all DLL files contained within the root folder of the game that start with `fmod_` alongside both `fmodex.dll` and `fmodexL.dll`. The version of these dll files is different between the two PC versions of the game. Arcade uses the `4.32.20` versions of the DLL files while Win32Wii uses `4.32.8 `.

## Annosoft Lipsync SDK / "The Lipsync Tool!" / (FOSS) SAPI_Lipsync
The Annosoft Lipsync SDK is used to create lip-sync files in the `.anno` format. "The Lipsync Tool!" provided by Annosoft produces phoneme timing data/timestamps, phonemes and weight values which the game then engine parses into an animation for the characters mouth. Cars 2 uses a serialized binary version of these files rather than the raw `.anno` text outputted by the tools. There is currently no method to convert the files to this format.
## Bink Video Ver. 1.9.99.0 / RAD Video Tools
Bink Video is used for playing back videos in the game such as `opening.bik`, and is included in with the game as `binkw32.dll`. RAD Video Tools is used to convert `.bik` files to formats such as `.mp4` and back. Although the exact version of `RAD Video Tools` that was used by Avalanche is unknown, any version of RAD Video Tools will work for creating videos compatible with the game, so long as it's in the Bink 1 format.


