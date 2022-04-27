# SassCompilerTest

This repository is meant to demonstrate a potential bug in [AspNetCore.SassCompiler](https://www.nuget.org/packages/AspNetCore.SassCompiler/), (https://github.com/koenvzeijl/AspNetCore.SassCompiler).

## Problem

It seems that if there are too many SCSS files, the compilation hangs and the build never completes.

## How to reproduce?

### Round 1:

`dotnet build .\BlazorApp\BlazorApp.csproj` --> 50 of 250 SCSS files get compiled, then it hangs

Attempting to stop the build via `Ctrl + C` does not work, gives the following output:

```
Attempting to cancel the build...
C:\Users\{UserName}\.nuget\packages\aspnetcore.sasscompiler\1.51.0\build\AspNetCore.SassCompiler.targets(7,5): warning MSB4220: Waiting for the currently executing task "CompileSass" to cancel. [C:\{PathToRepo}\SassCompilerTest\BlazorApp\BlazorApp.csproj]
```

Killing the process `dart.exe` via Task Manager makes the build fail eventually.

### Round 2:

`dotnet build .\BlazorApp\BlazorApp.csproj` --> another 50 SCSS files get compiled, then it hangs again.

Stopping the build has to be done the same way than above.

-----

## Remarks

1. Via Process Explorer one can find out that `dart.exe` is called as follows:

    ```
    "C:\Users\{UserName}\.nuget\packages\aspnetcore.sasscompiler\1.51.0\build\..\runtimes\win-x64\src\dart.exe" "C:\Users\{UserName}\.nuget\packages\aspnetcore.sasscompiler\1.51.0\build\..\runtimes\win-x64\src\sass.snapshot" --style=expanded --no-source-map --no-error-css --stop-on-error --no-unicode Components --update
    ```

    Executing this command directly via PowerShell for example compiles all 250 SCSS files without a problem.

2. Deleting 201 components with their SCSS files makes the build succeed. That means ...
   + ... 49 components: build succeeds.
   + ... 50 components: build hangs
