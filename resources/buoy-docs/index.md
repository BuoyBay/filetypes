---
title: "Uniform file types"
summary: "Classify filename extensions with modern macOS uniform type identifiers."
category: "Overview"
order: 0
keywords: ["filetypes", "macOS", "Linux", "Windows", "MIME", "native"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# Uniform file types

Use `filetypes` to label extensions, obtain a preferred MIME type, or test uniform-type conformance. `FileTypeInfo` uses modern UTType APIs on macOS. `GuessFileType` gives a portable name-based guess with a native identifier, an optional MIME type and an uncertainty flag. Neither inspects bytes or validates the contents of a file.

## Platform availability

This backend requires macOS 11 or newer. `FileTypesSupported()` reports the target platform only: it is true on macOS and false on Windows and Linux. The constructor can still throw `UnsupportedOperationException` when UTType is unavailable on an older macOS release. Windows and Linux operations also throw `UnsupportedOperationException`. Name-based guesses work on all three: `GuessFileType` uses UTType on macOS, GIO on Linux and read-only `HKEY_CLASSES_ROOT` lookups on Windows, gated by `FileTypeGuessSupported()` and the non-prompting `FileTypeGuessAvailable()` probe. Guess identifiers live in a platform namespace (`IdentifierNamespace`); a Linux GIO content type is not a UTType, so `FileTypeInfo` and `ConformsTo` stay macOS-only.

## Package use

Install `filetypes-0.1.0.buipkg` in the consumer project's `packages/` directory, then use `Import filetypes`. This package is independently sealed; it does not require importing the other desktop integration packages.

## Behavior and lifetime

Unknown extensions may produce dynamic types, an empty description, or an empty MIME type. Invalid text, empty extensions, and path separators raise `InvalidArgumentException`. A parent identifier that resolves to no type makes `ConformsTo` return false. Classification is not evidence that a file is safe to open or execute. No explicit close is needed.
## Continue reading

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
