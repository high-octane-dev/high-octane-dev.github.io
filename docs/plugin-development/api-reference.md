# API Reference

**WARNING! PENTANE'S API IS NOT STABLE SO ALL OF THIS IS SUBJECT TO CHANGE!**

If you're here for documentation on the Rust API, head over to [docs.rs](https://docs.rs/pentane/latest).

## Structures

`SemVer` - A structure representing a [Semantic Version](https://semver.org/).

| Type | Member | Description |
| ----------- | ----------- | ----------- |
| `std::uint16_t` | `major` | The major version. |
| `std::uint16_t` | `minor` | The minor version. |
| `std::uint16_t` | `patch` | The patch version. |


`PluginInformation` - Important plugin metadata, used by Pentane's plugin loader. Should *always* be exactly 1024 bytes in size.

| Type | Member | Description |
| ----------- | ----------- | ----------- |
| `char[256]` | `name` | A buffer containing a string representing the name of the plugin. |
| `char[256]` | `author` | A buffer containing a string representing the author(s) of the plugin. |
| `PentaneUUID` | `uuid` | The plugin's version 4 UUID. |
| `SemVer` | `version` | The current version of the plugin. |
| `SemVer` | `minimum_pentane_version` | The minimum version of Pentane that is required by the plugin. |
| `char[???]` | `reserved` | Extra space for Pentane to potentially request more metadata from plugins in the future. Please leave this zero-initialized! |

`PentaneCStringView` - A structure representing a non-owning view of a string.

| Type | Member | Description |
| ----------- | ----------- | ----------- |
| `const char* const` | `data` | A const pointer to the string data. Must be UTF-8. |
| `const std::size_t` | `data_len` | The length of the string data in bytes. |

`PentaneUUID` - A structure representing a version 4 UUID.

| Type | Member | Description |
| ----------- | ----------- | ----------- |
| `std::uint8_t[16]` | `data` | A version 4 UUID, stored directly as bytes. |

## Functions

WARNING: All functions are synchronous, and as such, should ***not*** be called excessively during the target application's runtime (i.e. outside of a plugin's `Pentane_Main`) to avoid any deadlocks.

The following functions are exported by all ``Pentane.dll`` binaries, irrespective of the target game:

| Function Name | Return Type | Description |
| ----------- | ----------- | ----------- |
| `Pentane_LogUTF8(PentaneCStringView* c_str);` | `void` | Outputs an input string to the logger. |
| `Pentane_IsPluginLoaded(PentaneUUID* uuid);` | `std::int32_t` | Determines if a currently loaded plugin matches the input UUID. Returns 0 if no loaded plugins match the given UUID, 1 if a plugin with the given UUID is loaded. |
| `Pentane_GetUserLanguage_ISO6391();` | `std::uint16_t` | Retrieves the user-selected language in the form of an ISO 639-1 code. The first character is stored in the upper 8 bits of the return value, while the second character is stored in the lower 8 bits. |

These functions are specific to Cars: Mater-National Championship:

| Function Name | Return Type | Description |
| ----------- | ----------- | ----------- |
| `Pentane_GetDataDirectoryName();` | `PentaneCStringView` | Returns a view of the data directory name. (e.g. "DataPC", "Hi-Octane") |
| `Pentane_IsSaveRedirectionEnabled();` | `std::int32_t` | Returns 0 if save file redirection is disabled, 1 if it is enabled. |

## Example Code

An example for how to use `Pentane_IsPluginLoaded`, `Pentane_LogUTF8`, and `Pentane_GetUserLanguage_ISO6391`.

```c++
constexpr std::uint16_t JAPANESE = static_cast<std::uint16_t>('j') << 8 | 'a';
const PentaneUUID TEST_MOD = {};
// ...
if (Pentane_IsPluginLoaded(&TEST_MOD) != 0) {
	std::string str = "Hello!";
	std::uint16_t code = Pentane_GetUserLanguage_ISO6391();
	if (code == JAPANESE) {
		str = "こんにちは！";
	}
	PentaneCStringView c_str{ str.c_str(), str.size() };
	Pentane_LogUTF8(&c_str);
}
```

An example definition of a plugin's metadata.

```c++
extern "C" PluginInformation Pentane_PluginInformation = PluginInformation {
	.name = "Example Plugin",
	.author = "John Doe",
	.uuid = {},
	.version = { 0, 1, 0 },
	.minimum_pentane_version = { 0, 2, 0 },
	.reserved = {},
};
```