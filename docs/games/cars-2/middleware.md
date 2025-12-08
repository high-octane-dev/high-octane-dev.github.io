# Middleware

## Scaleform GFx Ver. 3.3.88
Autodesk Scaleform GFx is used to render the user interface using `.swf` files that are contained within the `/assets/flash/flash.zip` file. The JPEX Free Flash Decompiler can be used to view and edit the existing `.swf` files in the game. Adding additional menus requires modifying the game via a Pentane plugin.

## FMOD Designer Ver. 4.32.9 (Arcade)* / 4.32.8 (Win32Wii)
FMOD Designer was used to create and control the interactive audio system in the game, which includes sound effects, ambience, and event-based audio behaviors. The resulting project data is compiled and loaded through the `FMOD API` at runtime, while the `FSB` file can be edited (This contains the raw audio data) the `FEV` files cannot unless they are decompiled into `FDP` files, while theoretically possible this has not been achieved yet, this means no editing of the events themselves. *(In Arcade there is a single 4.32.8 file, don't ask why. ¯\_(ツ)_/¯ )

## FMOD API Ver. 4.32.20 (Arcade) / 4.32.8 (Win32Wii)
The FMOD API refers to all DLL files contained within the root folder of the game that start with `fmod_` alongside both `fmodex.dll` and `fmodexL.dll`. The versions of these DLL files is different between the two PC versions of the game. Arcade uses the `4.32.20` versions of the DLL files while Win32Wii uses `4.32.8 `.

## Annosoft Lipsync SDK / "The Lipsync Tool!" / (FOSS) SAPI_Lipsync 
These tools provided by Annosoft produce `.anno` files that include phonemes, timestamps for said phonemes and weight values which the game engine then parses into an animation for the character's mouth. Cars 2 uses a serialized binary version of these files rather than the raw `.anno` text outputted by the tools. To put it simply, the `.anno` file you get from these tools will NOT work in the game by default and there is no tool that can convert standard `.anno` files to the Avalance format of the same name available to the public at this time. 

## Bink Video Ver. 1.9.99.0 / RAD Video Tools
Bink Video is used for playing back videos in the game, such as `opening.bik`, and is included in with the game as `binkw32.dll`. RAD Video Tools is used to convert `.bik` files to formats such as `.mp4` and back. Although the exact version of `RAD Video Tools` that was used by Avalanche is unknown, any version of RAD Video Tools will work for creating videos compatible with the game, so long as it's in the Bink 1 format.

## Bullet Physics Ver. Ava-2.73
The physics engine used in Cars 2 is a heavily modified fork of Bullet Physics 2.73. Most of the physics code is compiled directly into the game's executable; however, a subset of tunable parameters is exposed through the `all.values.aurora` file located inside the `assets/startup.zip`.
