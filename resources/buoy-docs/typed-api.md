---
title: "filetypes typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["FileTypeGuess", "FileTypeInfo"]
dependencies: []
symbols: ["FileTypeGuess", "FileTypeInfo"]
related: ["api.md", "getting-started.md", "index.md"]
---

# filetypes typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

### FileTypesSupported

`Public Function FileTypesSupported() As Boolean`

Frozen: True only where the original macOS UTType FileTypeInfo backend runs. FileTypeInfo stays macOS-only; portable name-based guesses use GuessFileType, gated by FileTypeGuessSupported.

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### FileTypeGuessSupported

`Public Function FileTypeGuessSupported() As Boolean`

True when this compile target has a GuessFileType backend: macOS UTType, Linux GIO, Windows HKEY_CLASSES_ROOT. Compile-time only. Also gates MIMETypeForIdentifier and IdentifierFromMIMEType.

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### FileTypeGuessAvailable

`Public Function FileTypeGuessAvailable() As Boolean`

Probe, never prompts. macOS: dlopen of the UniformTypeIdentifiers system framework (False before macOS 11). Linux: library probe for libgio/libglib; it does not prove a shared-mime-info database is installed. Windows: library probe for advapi32. False on unsupported targets.

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### GuessFileType

`Public Function GuessFileType(fileName As String) As FileTypeGuess`

Guesses a file type from fileName alone (a name or path). Never opens, reads or stats the file, and never prompts. InvalidArgumentException for an empty or NUL-containing name; UnsupportedOperationException on targets without a backend; Linux raises LibraryUnavailableException without GIO.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `fileName` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `FileTypeGuess` |

### MIMETypeForIdentifier

`Public Function MIMETypeForIdentifier(identifier As String) As Optional Of String`

Preferred MIME type for a native identifier from GuessFileType (same platform namespace). None when no MIME type is registered.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `identifier` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Optional Of String` |

### IdentifierFromMIMEType

`Public Function IdentifierFromMIMEType(mimeType As String) As Optional Of String`

Native identifier for a MIME type: a declared UTI on macOS (dynamic types give None), a GIO content type on Linux, a registered extension key on Windows. None when the platform has no mapping.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `mimeType` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Optional Of String` |

## FileTypeInfo

`Public Class FileTypeInfo`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Identifier` | `String` | Yes | No | — | — |
| `Description` | `String` | Yes | No | — | — |
| `Extension` | `String` | Yes | No | — | — |
| `MIMEType` | `String` | Yes | No | — | — |
| `IsDynamic` | `Boolean` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(fileExtension As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `fileExtension` | `String` | `ByVal` | Required |

Returns: no value.

### ConformsTo

`Public Function ConformsTo(identifier As String) As Boolean`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `identifier` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

## FileTypeGuess

`Public Class FileTypeGuess`

A name-based file-type guess. Identifier is in the platform's native namespace (IdentifierNamespace): never compare it across platforms, and a GIO content type is not a UTType even when it looks like a MIME string.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Identifier` | `String` | Yes | No | — | macOS UTI ("public.plain-text"), Linux GIO content type ("text/plain"), or Windows lower-case extension key (".txt"; "*" for a name with no extension). |
| `IdentifierNamespace` | `String` | Yes | No | — | Exactly one of "macos-uti", "gio-content-type", "windows-file-extension". |
| `MIMEType` | `Optional Of String` | Yes | No | — | Preferred MIME type, or None when the platform has none registered. |
| `IsUncertain` | `Boolean` | Yes | No | — | macOS: no extension or a dynamic (undeclared) type. Linux: GIO's own result_uncertain flag. Windows: no extension, or no registered Content Type for it. |

### FileTypesLinuxAvailable

`Public Function FileTypesLinuxAvailable() As Boolean`

Library probe only (dlopen); does not start services or read files.

Availability: `#If TargetLinux Then`

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### FileTypesLinuxGuess

`Public Function FileTypesLinuxGuess(filename As String, ByRef uncertain As Boolean) As String`

g_content_type_guess by name only (data = NULL); never opens the file.

Availability: `#If TargetLinux Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `filename` | `String` | `ByVal` | Required |
| `uncertain` | `Boolean` | `ByRef` | Required |

#### Returns

| Type |
| --- |
| `String` |

### FileTypesLinuxMIMEType

`Public Function FileTypesLinuxMIMEType(contentType As String) As Optional Of String`

Availability: `#If TargetLinux Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `contentType` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Optional Of String` |

### FileTypesLinuxFromMIMEType

`Public Function FileTypesLinuxFromMIMEType(mimeType As String) As Optional Of String`

Availability: `#If TargetLinux Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `mimeType` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Optional Of String` |

### FileTypesWindowsContentType

`Public Function FileTypesWindowsContentType(extensionKey As String) As Optional Of String`

HKCR\<.ext> "Content Type"; None when the extension has no registered MIME type.

Availability: `#If TargetWindows Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `extensionKey` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Optional Of String` |

### FileTypesWindowsExtensionForMIMEType

`Public Function FileTypesWindowsExtensionForMIMEType(mimeType As String) As Optional Of String`

HKCR\MIME\Database\Content Type\<mime> "Extension"; None when unregistered.

Availability: `#If TargetWindows Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `mimeType` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Optional Of String` |
