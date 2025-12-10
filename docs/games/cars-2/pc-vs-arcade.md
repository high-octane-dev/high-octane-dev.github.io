# Cars 2: The Video Game (PC) vs Cars 2: Arcade

## What is Cars 2: The Video Game (PC)?
Cars 2: The Video Game (PC), internally known as the `Win32_Wii` target, refers to the official Windows versions of the game available on Steam and physical CDs. It is essentially a stripped-down port of the Wii version that simultaneously introduces various post-processing effects and settings menus to improve the overall user experience. However, Win32_Wii is widely considered a botched port due to its inability to replicate original lighting and material properties in the Wii version.

## What is Cars 2: Arcade?
Cars 2: Arcade, on the other hand, is a development build of the HD version of Cars 2: The Video Game, internally known as the `Win32` target, which was heavily modified by *Raw Thrills* for use in arcade machines. *Raw Thrills* simplified the game down into a generic kart racer fit for arcade venues like Chuck E. Cheese, resulting in the removal of numerous gameplay features to accommodate the cabinet's limited control scheme. As a result, only traditional races are functional, and all other game modes are completely absent. Additionally, only 6 of the 15 original race courses are playable in Arcade, and all Arena maps have also been removed.

## Why is this important?
Initially, it was assumed that the features missing from Cars 2: Arcade were impossible to restore because they had been either mutilated or stripped entirely from the game. This assumption, interestingly, proved false; rather than the features being fully removed or corrupted, many aspects of the original game's core functionality were merely disabled or bypassed by Raw Thrills' changes. This was a crucial distinction: the code for features like alternate game modes remained (mostly) intact, allowing them to be reactivated via binary patching.

Thus, *ArcadeEssentials* was developed to re-enable the majority of Arcade's disabled features and fix several issues introduced by Raw Thrills' various patches, effectively transforming Arcade into a proper native port of the HD version of the game for Windows.

Despite this, the missing maps and track assets remained a major barrier. To address this, various tools to endian-swap the missing assets from the Xbox 360 version of the game into Arcade-friendly formats were developed, and then used for *Cars 2: Restorations*. This filled in the last major gaps and brought the game closer than ever to a fully restored experience.

## Where does this leave us now?
From late 2021 to mid 2025, the `Win32_Wii` version served as the primary modding platform for Cars 2: The Video Game. However, the combined efforts of *ArcadeEssentials* and *Cars 2: Restorations* have since established Arcade as the primary modding platform. `Win32_Wii` is now widely considered the legacy modding platform.
