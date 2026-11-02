---
title: "Uniform file types getting started"
summary: "Install and use filetypes with an availability check and a complete example."
category: "Guides"
order: 10
keywords: ["filetypes", "install", "example"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

## First use

Pass an extension such as `pdf` or `.pdf`, rather than a complete filename or path. Read the copied identifier, description, preferred extension, and MIME type. Use `ConformsTo` with a parent type identifier when grouping related types.

```basic
Import filetypes

Sub Main()
    If Not FileTypesSupported() Then
        Print "Uniform file types require macOS 11 or newer."
        Return
    End If
    Dim info As New FileTypeInfo("pdf")
    Print info.Identifier
    Print info.MIMEType
    If info.ConformsTo("public.data") Then Print "This is a data type."
End Sub
```

On any platform, guess from a name instead:

```basic
Import filetypes

Sub Main()
    If Not FileTypeGuessSupported() Or Not FileTypeGuessAvailable() Then Return
    Dim guess As FileTypeGuess = GuessFileType("report.pdf")
    Print guess.IdentifierNamespace + ": " + guess.Identifier
    If guess.MIMEType.HasValue Then Print guess.MIMEType.Value
    If guess.IsUncertain Then Print "Low-confidence guess"
End Sub
```

The example checks backend availability before invoking macOS-only operations. It does not catch input or access failures; catch exceptions at your application boundary and present a useful message to the user.

## Platform and error handling

This backend requires macOS 11 or newer. `FileTypesSupported()` reports the target platform only: it is true on macOS and false on Windows and Linux. The constructor can still throw `UnsupportedOperationException` when UTType is unavailable on an older macOS release. `FileTypeInfo` operations on Windows and Linux also throw `UnsupportedOperationException`. Name-based guesses work on all three: `GuessFileType` uses UTType on macOS, GIO on Linux and read-only `HKEY_CLASSES_ROOT` lookups on Windows, gated by `FileTypeGuessSupported()` and the non-prompting `FileTypeGuessAvailable()` probe. Guess identifiers live in a platform namespace (`IdentifierNamespace`); a Linux GIO content type is not a UTType, so `FileTypeInfo` and `ConformsTo` stay macOS-only. Unknown extensions may produce dynamic types, an empty description, or an empty MIME type. Invalid text, empty extensions, and path separators raise `InvalidArgumentException`. A parent identifier that resolves to no type makes `ConformsTo` return false. Classification is not evidence that a file is safe to open or execute. No explicit close is needed.
## Continue reading

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
