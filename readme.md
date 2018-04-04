# SDL2 CMAKE TEMPLATE
A template for a sdl2 applictaion with cmake

![Screenshot](screenshot.png)

## Used libraries
* SDL2
* SLD2_image
* SDL2_gfx
* SDL2_mixer

## Build
```bash
git clone https://github.com/ThKattanek/sdl2_cmake_template.git
cd sdl2_cmake_template
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make
make install
```
Optional kann noch -DCMAKE_INSTALL_PREFIX=[InstallPfad] verwendet werden.

## Uninstall
```bash
xargs rm < install_manifest.txt
```
Warning! Directories created by the installation are not removed, but only all files created.

## Compiling for Windows x32 with MXE (Crossdev)
```bash
cd ~
git clone https://github.com/ThKattanek/sdl2_cmake_template.git
cd sdl2_cmake_template
mkdir build-win-x32
cd build-win-x32
[MXE-PATH]/usr/bin/i686-w64-mingw32.static-cmake .. -DWIN32_STATIC_BUILD=TRUE -DCMAKE_INSTALL_PREFIX=../install-win-x32
make
make install
```
## Compiling for Windows x64 with MXE (Crossdev)
```bash
cd ~
git clone https://github.com/ThKattanek/sdl2_cmake_template.git
cd sdl2_cmake_template
mkdir build-win-x64
cd build-win-x64
[MXE-PATH]/usr/bin/x86_64-w64-mingw32.static-cmake .. -DWIN32_STATIC_BUILD=TRUE -DCMAKE_INSTALL_PREFIX=../install-win-x64
make
make install
```
## Complete build and create the windows versions (32/64bit) as 7zip with Script (crossbuild_win_releases.sh)
#### MXE required
```bash
cd ~
git clone https://github.com/ThKattanek/sdl2_cmake_template.git
cd sdl2_cmake_template
./crossbuild_win_releases.sh [MXE-PATH]
```
### [MXE Website](http://mxe.cc)
