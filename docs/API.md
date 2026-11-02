# File types · 0.1.0

`Import filetypes`. Two APIs with different identities:

- **`FileTypeInfo` (macOS 11+, unchanged).** `New FileTypeInfo(fileExtension)` uses UTType. Read-only `Identifier`, `Description`, `Extension`, `MIMEType` and `IsDynamic`; `ConformsTo(identifier)` checks UTType conformance. Leading dots are accepted. `FileTypesSupported()` stays True only on macOS, and `FileTypeInfo` still throws `UnsupportedOperationException` on Linux and Windows: a GIO content type or MIME string is not a UTType, and conformance is never emulated.
- **`GuessFileType(fileName)` (macOS, Linux, Windows).** A name-based guess that never opens, reads or stats the file. It returns a read-only `FileTypeGuess` with `Identifier`, `IdentifierNamespace`, `MIMEType As Optional Of String` and `IsUncertain`. `MIMETypeForIdentifier(identifier)` and `IdentifierFromMIMEType(mimeType)` convert within the same namespace. Gate on `FileTypeGuessSupported()` (compile target) and `FileTypeGuessAvailable()` (a non-prompting library probe).

| | macOS | Linux | Windows |
| --- | --- | --- | --- |
| `IdentifierNamespace` | `macos-uti` | `gio-content-type` | `windows-file-extension` |
| `Identifier` for `notes.txt` | `public.plain-text` | `text/plain` (GIO content type) | `.txt` (HKCR key; `*` for no extension) |
| `MIMEType` | UTType preferred MIME | `g_content_type_get_mime_type` | HKCR `.ext` `Content Type` |
| `IsUncertain` | no extension or dynamic type | GIO `result_uncertain` | no extension or no registered `Content Type` |
| `IdentifierFromMIMEType` | declared UTI (dynamic → None) | GIO content type (GIO returns unknown MIME strings unchanged) | HKCR `MIME\Database\Content Type` `Extension` |

Identifiers are only comparable within one namespace. Windows lookups read `HKEY_CLASSES_ROOT` only and never write the registry; results reflect the machine's registered associations. Classification is not evidence that a file is safe to open or execute.

Linux and Windows backends live in internal modules (`FileTypesLinuxBackend`, `FileTypesWindowsBackend`) whose declarations are copied from the container interop modules (`linuxcontent`, `WinRegistry`); the package imports no container code. Description and icon helpers stay in `linuxcontent`.

Inspired by [macoslib’s Cocoa examples](https://github.com/macoslib/macoslib); implemented independently against [Apple’s API](https://developer.apple.com/documentation/uniformtypeidentifiers/uttype).
