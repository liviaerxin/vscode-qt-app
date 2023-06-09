# Vcpkg + Qt + VSCode Application on Windows, OSX

Set up a light/simple cross-platform desktop GUI application using following tech stacks:

- Vcpkg: c++ package manager
- Qt: GUI framework
- VSCode: IDE

Some takeaways in  the project:

- Support OSX and Windows platform
- Install application under `./install` directory by default
- Package a standalone and relocatable application including all dependent shared libraries
- Use shared libraries on Windows/OSX
  - `arm64-osx-dynamic` currently needs [vcpkg: branch fix-install-name-rpath-osx-dynamic](https://github.com/liviaerxin/vcpkg/tree/fix-install-name-rpath-osx-dynamic)
- Use vcpkg manifest mode
  - dynamic libraries are installed into `build/vcpkg_installed` in project

A more basic project located at [GitHub - liviaerxin/cmake-package-app](https://github.com/liviaerxin/cmake-package-app-example)

## Windows

Prerequisites, set environment variables permanently

```sh
[Environment]::SetEnvironmentVariable('VCPKG_ROOT', "Whatever you need it to be", "Machine")
```

Configure, Build and Install

```sh
cmake -B build -S . --preset=debug
cmake -B build -S . --preset=debug --fresh
cmake -B build -S . --preset=debug -DVCPKG_BINARY_SOURCES=clear --fresh
cmake -B build -S . --preset=debug -DVCPKG_BINARY_SOURCES=clear -DCMAKE_BUILD_TYPE=Release --fresh

cmake --build build
cmake --build build --config=release

cmake --install build
cmake --install build --config=release --prefix=C:\Users\Frank\Desktop\p\vcpkg-qt-app
```

## OSX

Prerequisites, set environment variables permanently

```sh
# edit in `~/.zshrc`, add the following
export VCPKG_ROOT=/opt/vcpkg/
```

[Optional]Install

```sh
cd build
vcpkg install --host-triplet=arm64-osx-dynamic --triplet=arm64-osx-dynamic
```

Configure, Build and Install

```sh
cmake -B build -S . --preset=debug -DVCPKG_TARGET_TRIPLET=arm64-osx-dynamic -DVCPKG_HOST_TRIPLET=arm64-osx-dynamic
cmake -B build -S . --preset=debug -DVCPKG_TARGET_TRIPLET=arm64-osx-dynamic -DVCPKG_HOST_TRIPLET=arm64-osx-dynamic -DVCPKG_BINARY_SOURCES=clear

cmake -B build -S . --preset=debug -DVCPKG_TARGET_TRIPLET=arm64-osx-dynamic -DVCPKG_HOST_TRIPLET=arm64-osx-dynamic --fresh

cmake -B build -S . --preset=debug -DVCPKG_TARGET_TRIPLET=arm64-osx-dynamic -DVCPKG_HOST_TRIPLET=arm64-osx-dynamic -DCMAKE_BUILD_TYPE=Debug --fresh
cmake -B build -S . --preset=debug -DVCPKG_TARGET_TRIPLET=arm64-osx-dynamic -DVCPKG_HOST_TRIPLET=arm64-osx-dynamic -DCMAKE_BUILD_TYPE=Release --fresh

cmake --build build

cmake --install build
```

## More

qt widget application:

```jsonc title="vcpkg.json"
{
  "dependencies": [
    {
      "name": "qtbase",
      "default-features": false,
      "features": [
        "gui",
        "widgets"
      ]
    }
  ]
}
```

qt quick+qml application:

```jsonc title="vcpkg.json"
{
  "dependencies": [
    {
      "name": "qtbase",
      "default-features": false,
      "features": [
        "gui",
        "widgets"
      ]
    },
    "qttools",
    "qtdeclarative"
  ]
}
```

## References

[Getting Started with Manifest mode | Microsoft Learn](https://learn.microsoft.com/en-us/vcpkg/examples/manifest-mode-cmake)

[Qt's CMake deployment APIQt's CMake deployment API](https://www.qt.io/blog/cmake-deployment-api)

[Platform-specific guides » macOS | Magnum C++ docs](https://doc.magnum.graphics/magnum/platforms-macos.html)

[c++ - CMake MacOS X bundle with BundleUtiliies for Qt application - Stack Overflow](https://stackoverflow.com/questions/35612687/cmake-macos-x-bundle-with-bundleutiliies-for-qt-application)

[GitHub - ionyshch/cmake-bundle-macos: Portable sample app for MacOS X using CMake and CPack.](https://github.com/ionyshch/cmake-bundle-macos)

[It’s Time To Do CMake Right | pablo arias](https://pabloariasal.github.io/2018/02/19/its-time-to-do-cmake-right/)

[c++ - Can't use fixup_bundle() to create a portable bundle with Qt - Stack Overflow](https://stackoverflow.com/questions/17974439/cant-use-fixup-bundle-to-create-a-portable-bundle-with-qt)