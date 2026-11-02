# UTType and filename guesses

`Import filetypes`. Two APIs with different identities:

Identifiers are only comparable within one namespace. Windows lookups read `HKEY_CLASSES_ROOT` only and never write the registry; results reflect the machine's registered associations. Classification is not evidence that a file is safe to open or execute.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add filetypes --git https://github.com/BuoyBay/filetypes.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

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

## Platform and scope

macOS uses UTType. Other targets expose portable name-based guesses; those guesses are not native content inspection.

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer filetypes
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
