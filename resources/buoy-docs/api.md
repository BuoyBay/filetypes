---
title: "Uniform file types API"
summary: "Public filetypes types and operations with behavior and lifetime notes."
category: "Reference"
order: 20
keywords: ["filetypes", "API", "reference"]
symbols: ["FileTypesSupported", "FileTypeGuessSupported", "FileTypeGuessAvailable", "GuessFileType", "FileTypeGuess", "FileTypeGuess.Identifier", "FileTypeGuess.IdentifierNamespace", "FileTypeGuess.MIMEType", "FileTypeGuess.IsUncertain", "MIMETypeForIdentifier", "IdentifierFromMIMEType", "FileTypeInfo", "FileTypeInfo.Identifier", "FileTypeInfo.Description", "FileTypeInfo.Extension", "FileTypeInfo.MIMEType", "FileTypeInfo.IsDynamic", "FileTypeInfo.ConformsTo"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

`Import filetypes` exposes the following public API. `FileTypesLinuxBackend` and `FileTypesWindowsBackend` are internal modules, not public API. Properties described as read-only have no public setter.

| Symbol | Behavior |
| --- | --- |
| `FileTypesSupported() As Boolean` | Reports the macOS target backend, not an operating-system version check. |
| `FileTypeInfo(fileExtension As String)` | Resolve an extension; one leading dot is accepted. |
| `FileTypeInfo.Identifier As String` | Read-only uniform type identifier. |
| `FileTypeInfo.Description As String` | Read-only localized type description when supplied by the OS. |
| `FileTypeInfo.Extension As String` | Read-only preferred filename extension. |
| `FileTypeInfo.MIMEType As String` | Read-only preferred MIME type; may be empty. |
| `FileTypeInfo.IsDynamic As Boolean` | Read-only flag identifying a dynamic type. |
| `FileTypeInfo.ConformsTo(identifier As String) As Boolean` | Test native conformance; an unknown parent returns false. |
| `FileTypeGuessSupported() As Boolean` | True on macOS, Linux and Windows targets. Compile-time only. |
| `FileTypeGuessAvailable() As Boolean` | Non-prompting probe: UTType framework load (macOS), libgio/libglib library probe (Linux), advapi32 (Windows). |
| `GuessFileType(fileName As String) As FileTypeGuess` | Name-only guess; never opens the file. Empty or NUL names raise `InvalidArgumentException`; Linux without GIO raises `LibraryUnavailableException`. |
| `FileTypeGuess.Identifier As String` | Native identifier: UTI, GIO content type, or Windows extension key (`.txt`, `*` for none). |
| `FileTypeGuess.IdentifierNamespace As String` | Exactly one of `macos-uti`, `gio-content-type`, `windows-file-extension`. |
| `FileTypeGuess.MIMEType As Optional Of String` | Preferred MIME type, None when none is registered. |
| `FileTypeGuess.IsUncertain As Boolean` | The platform could not identify the type confidently. |
| `MIMETypeForIdentifier(identifier As String) As Optional Of String` | MIME type for an identifier in this platform's namespace. Windows requires an extension key. |
| `IdentifierFromMIMEType(mimeType As String) As Optional Of String` | Native identifier for a `type/subtype` MIME string; malformed input raises `InvalidArgumentException`. Both components must be nonempty RFC 2045 ASCII tokens separated by exactly one slash; parameters and whitespace are not accepted. Input case is passed unchanged to the native backend. Linux preserves valid unknown MIME strings through GIO. |

## Errors and availability

This backend requires macOS 11 or newer. `FileTypesSupported()` reports the target platform only: it is true on macOS and false on Windows and Linux. The constructor can still throw `UnsupportedOperationException` when UTType is unavailable on an older macOS release. `FileTypeInfo` operations on Windows and Linux also throw `UnsupportedOperationException`. Name-based guesses work on all three: `GuessFileType` uses UTType on macOS, GIO on Linux and read-only `HKEY_CLASSES_ROOT` lookups on Windows, gated by `FileTypeGuessSupported()` and the non-prompting `FileTypeGuessAvailable()` probe. Guess identifiers live in a platform namespace (`IdentifierNamespace`); a Linux GIO content type is not a UTType, so `FileTypeInfo` and `ConformsTo` stay macOS-only. Unknown extensions may produce dynamic types, an empty description, or an empty MIME type. Invalid text, empty extensions, and path separators raise `InvalidArgumentException`. A parent identifier that resolves to no type makes `ConformsTo` return false. Classification is not evidence that a file is safe to open or execute. No explicit close is needed.
## Continue reading

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)

[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
