# Creating A Plugin (Rust)

WARNING!!!! PENTANE'S API IS NOT STABLE SO ALL OF THIS IS SUBJECT TO CHANGE!!!!

## Prerequisiste Software
   - [rustup](https://www.rust-lang.org/tools/install)
   - [Visual Studio Code](https://code.visualstudio.com/)
   - [git](https://git-scm.com/downloads) (For Version Control)

## Installation
1. Install Git (Follow the on-screen installer)
2. Install rustup (Follow the instructions on the page)
3. Install Visual Studio Code (Follow the on-screen installer)
4. After the installation is complete, open a terminal and execute the following command: ``rustup target add i686-pc-windows-msvc`` to ensure the correct target is installed.

## Creating/Building a Plugin
1. Open a terminal and run ``cargo new <project_name>``, where ``project_name`` is your desired name for the plugin.
2. Run ``cd <project_name>``, then run ``code`` to open Visual Studio Code.
3. Click on ``cargo.toml`` and add these lines to the bottom before then saving the file (Ctrl-S):
```
[lib]
crate-type = ["dylib"]
```
4. Right-click on the root of the project directory, and create a new folder named ``.cargo`` right next to ``src``.
5. Create a new file inside the ``.cargo`` folder named ``config.toml``, and paste these lines into the file before then saving it:
```
[build]
target = ["i686-pc-windows-msvc"]
```
6. Now rename the file ``src/main.rs`` to ``src/lib.rs`` and open it.
7. Delete all contents and paste this template function in:
```
#[no_mangle]
extern "stdcall" fn Pentane_Main() {
	
}
```
8. Now open a terminal inside Visual Studio Code, and execute the following command ``cargo build --release``
9. Navigate to your project folder via Windows Explorer, then follow the path  ``./target/i686-pc-windows-msvc/release/`` to locate ``<project_name>.dll``.
10. Copy your newly compiled plugin to ``Pentane/Plugins``.
11. Enable your plugin in ``Pentane/config.toml``.

Congratulations! You have built your first plugin!