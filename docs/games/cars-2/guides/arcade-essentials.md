# Installing ArcadeEssentials

Si necesita un tutorial en español, haga clic [aquí](https://docs.google.com/SPANISH_TUTORIAL_HERE).

If you require a French-language tutorial, click [here](https://docs.google.com/FRENCH_TUTORIAL_HERE).

If you require a German-language tutorial, click [here](https://docs.google.com/GERMAN_TUTORIAL_HERE).

If you require a Polish-language tutorial, click [here](https://docs.google.com/POLISH_TUTORIAL_HERE).

Если вам нужен русскоязычный туториал, нажмите [здесь](https://docs.google.com/document/d/1YhnYanMW06EJkPTmW2CpTRwuoUwi82A4tlPDrTKS3Dc).

WARNING: ArcadeEssentials is **NOT COMPATIBLE** with PC version of Cars 2: The Video Game! It is *only* compatible with Cars 2: Arcade.

1. If you haven't already installed Pentane for Cars 2: Arcade, refer to the [Pentane installation tutorial](../../../installation-and-setup.md) and complete its setup process before proceeding.
2. Head to the releases page on the ArcadeEssentials GitHub repository, linked [here](https://github.com/itsmeft24/ArcadeEssentials/releases/latest).
3. Download the `release.zip` file linked under the Assets tab.
4. You will find two folders in the zip file, one named `Pentane` and one labeled `assets`.
	- If you're planning on installing an overhaul mod such as *Cars 2: Restorations* or *Cars 2: Deluxe*, just copy the `Pentane` folder into the root of your game's installation, right next to `sdaemon.exe` and `Pentane.dll`.
	- Otherwise, copy **both** the `assets` folder and the `Pentane` folder into the root of your game's installation, right next to `sdaemon.exe` and `Pentane.dll`. Make sure to *replace* any files already present in the destination.
5. Open `Pentane/config.toml` in a text editor, like Notepad or Notepad++.
6. Under the `[plugins]` section, edit the `enabled_plugins` list to include `"ArcadeEssentials.dll"`, and save your changes.
	- Example:
	```toml
	[plugins]
	enabled_plugins = ["ArcadeEssentials.dll"]
	```
7. Check to make sure your `config.toml` file is still valid TOML, by using a linter such as *toml-lint.com*.
8. You're done!