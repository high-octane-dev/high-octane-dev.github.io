# What is Pentane

Pentane is a mod-loader for the following games:

   - Cars: The Video Game (PC)
   - Cars: Mater-National Championship (PC)
   - Cars 2: The Video Game (PC)
   - Cars 3: Driven to Win (Xbox One)

Notably, it introduces a user-configurable patch system for mods, and most importantly, allows plugin developers to modify game code via the plugin system.

Behind the scenes, Pentane uses the library [sunset](https://github.com/itsmeft24/sunset) to patch Mater-National's file-system code via function hooking, or intercepting/replacing individual functions or parts of functions with custom implementations.