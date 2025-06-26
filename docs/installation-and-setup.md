# Installation and Setup

Si necesita un tutorial en español, haga clic [aquí](https://docs.google.com/SPANISH_TUTORIAL_HERE).

If you require a French-language tutorial, click [here](https://docs.google.com/FRENCH_TUTORIAL_HERE).

If you require a German-language tutorial, click [here](https://docs.google.com/GERMAN_TUTORIAL_HERE).

If you require a Polish-language tutorial, click [here](https://docs.google.com/POLISH_TUTORIAL_HERE).

Если вам нужен русскоязычный туториал, нажмите [здесь](https://docs.google.com/document/d/1GjcIO-WiiNa1pBaw-ebC5h4a-Ynzf2rBOzqBgNX-oEU).

## Unpacking your Executable

If you're installing Pentane for the Steam version of a supported game, you will **need** to unpack your game's executable file before Pentane installation.

***Note***: If you're installing Pentane for the CD release of *Cars: Mater-National Championship*, the Amazon Games version of *Cars: The Video Game*, or *Cars 2: Arcade*, you **should skip this section**.

1. Head to the releases page on the Steamless GitHub repository, linked [here](https://github.com/atom0s/Steamless/releases/latest).
2. Download the `.zip` file linked under the Assets tab, and extract it.
3. Once you have extracted the `.zip` file, open `Steamless.exe`.
4. Directly under the first numbered label, titled *"Select File to Unpack"*, click on the button with the elipsis.
5. Navigate to the folder where your game is installed. This is usually inside the `C:\Program Files (x86)\Steam\steamapps\common` folder by default.
6. Select your game's executable file, and click *Open*.
	- For *Cars: The Video Game*, this is `Cars.exe`.
	- For *Cars: Mater-National Championship*, this is `Cars_Mater.exe`.
	- For *Cars 2: The Video Game*, this is `Game-Cars.exe`.
7. Click the *"Unpack File"* button. Once you see the *"Successfully unpacked file!"* message appear, continue to the next step.
8. Exit *Steamless*, and navigate back to the folder where your target game is installed. You should now see a new executable file, named exactly the same as the old one, except with `.unpacked.exe` appended to the end of the filename.
9. **Delete** the old executable, and name your newly-unpacked executable *the same name as the old one*.
10. You're done! You may continue onward to Pentane installation.

## Installing Pentane (Non-Game-Specific)
1. Head to the releases page on the Pentane Installer GitHub repository, linked [here](https://github.com/high-octane-dev/pentane-installer/releases/latest).
2. Download the `PentaneInstaller.exe` file and open it. Navigate to the folder where your target game is installed, and select its executable file.
	- For *Cars: The Video Game (Amazon/Steam Unpacked)*, this is `Cars.exe`.
	- For *Cars: Mater-National Championship (CD/Steam Unpacked)*, this is `Cars_Mater.exe`.
	- For *Cars 2: The Video Game (Steam Unpacked)*, this is `Game-Cars.exe`.
	- For *Cars 2: Arcade (Version 1.42)*, this is `sdaemon.exe`.
3. Head to the releases page on the Pentane GitHub repository, linked [here](https://github.com/high-octane-dev/pentane/releases/latest/).
4. Download the correct `.dll` file for your target game, linked under the Assets tab. Note that it is *very important* you select the correct `.dll`, as they are *not* cross-compatible.
	- For *Cars: The Video Game (Amazon/Steam Unpacked)*, this is `Pentane-TVG.dll`.
	- For *Cars: Mater-National Championship (CD/Steam Unpacked)*, this is `Pentane-MN.dll`.
	- For *Cars 2: The Video Game (Steam Unpacked)*, this is `Pentane-2TVG.dll`.
	- For *Cars 2: Arcade (Version 1.42)*, this is `Pentane-2TVGA.dll`.
5. Rename your newly-downloaded `dll` file to `Pentane.dll`, and place it in root folder of your target game's installation, next to its executable file.
6. In the root folder of your target game, create a `Pentane` folder next to `Pentane.dll` and your game's executable file.
7. Create a `Plugins` folder inside the `Pentane` folder. This is where all your Pentane plugins for the target game will reside.
8. You're done! Please continue onward to configuring Pentane.

## Configuring Pentane
1. Navigate to the `assets/config-templates` folder in the Pentane GitHub repository, linked [here](https://github.com/high-octane-dev/pentane/tree/main/assets/config-templates/).
2. Click on the `.toml` file that corresponds with your target game.
	- For *Cars: The Video Game*, this is `cars-the-video-game.toml`.
	- For *Cars: Mater-National Championship*, this is `cars-mater-national.toml`.
	- For *Cars 2: The Video Game*, this is `cars-2.toml`.
	- For *Cars 2: Arcade*, this is `cars-2-arcade.toml`.
3. Click on the *"Copy raw file"* button located next to the button labelled *Raw*, or alternatively, select the contents of the file and copy it to your clipboard.
4. Open a text editor, such as Notepad or Notepad++, and paste your clipboard contents into a new document. The contents of this file should be identical to what is on the repository.
5. Save this file as `config.toml` inside the `Pentane` folder for your target game. If done correctly, `Pentane/config.toml` should exist relative to your game's executable and `Pentane.dll` file.
6. Re-open the `config.toml` file in a text editor and edit the configuration options to your liking. Make sure you do not remove any existing options.
7. Check to make sure your `config.toml` file is still valid TOML, by using a linter such as *toml-lint.com*.
8. You're done!

NOTE: If you're installing Pentane for Cars 2: Arcade, it is *highly recommended* you also install ArcadeEssentials. If you'd like to install ArcadeEssentials, continue onward to its installation tutorial [here](./games/cars-2/guides/arcade-essentials.md).