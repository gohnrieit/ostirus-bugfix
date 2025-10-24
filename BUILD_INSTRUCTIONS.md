# OsTIrus VST3 Build Instructions

## Bug Fix Applied ✅

The Categories count bug has been fixed in:
- **File**: `source/jucePluginEditorLib/patchmanager/treeitem.cpp`
- **Lines**: 162-175
- **Fix**: Added bounds checking to prevent integer overflow when casting `size_t` to `uint32_t`

## Building OsTIrus VST3 for Windows

### Option 1: Build on Windows (Recommended)

1. **Clone the repository**:
   ```bash
   git clone https://github.com/dsp56300/gearmulator.git
   cd gearmulator
   git checkout 1.4.2
   ```

2. **Install dependencies**:
   - Visual Studio 2019 or 2022
   - CMake 3.15 or later

3. **Build using the provided script**:
   ```batch
   build_win64_vs19.bat
   ```

4. **Or build manually**:
   ```batch
   cmake . -B ./temp/cmake_win64 -G "Visual Studio 16 2019" -A x64 -Dgearmulator_BUILD_FX_PLUGIN=ON -DDSP56300_DEBUGGER=OFF
   cmake --build ./temp/cmake_win64 --config Release
   ```

### Option 2: Cross-compile from Linux

If you want to try cross-compilation from Linux, you'll need:

1. **Complete MinGW-w64 toolchain**:
   ```bash
   # Download from: https://github.com/niXman/mingw-builds-binaries/releases
   wget https://github.com/niXman/mingw-builds-binaries/releases/download/13.2.0-rt_v11-rev1/winlibs-x86_64-posix-seh-gcc-13.2.0-mingw-w64-11.0.0-r1.zip
   unzip winlibs-x86_64-posix-seh-gcc-13.2.0-mingw-w64-11.0.0-r1.zip
   export PATH=$PWD/winlibs-x86_64-posix-seh-gcc-13.2.0-mingw-w64-11.0.0-r1/bin:$PATH
   ```

2. **Configure CMake for cross-compilation**:
   ```bash
   cmake . -B ./temp/cmake_win64_cross \
     -DCMAKE_SYSTEM_NAME=Windows \
     -DCMAKE_C_COMPILER=x86_64-w64-mingw32-gcc \
     -DCMAKE_CXX_COMPILER=x86_64-w64-mingw32-g++ \
     -DCMAKE_RC_COMPILER=x86_64-w64-mingw32-windres \
     -DCMAKE_FIND_ROOT_PATH=/usr/x86_64-w64-mingw32 \
     -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER \
     -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY \
     -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY \
     -Dgearmulator_BUILD_JUCEPLUGIN=ON \
     -Dgearmulator_BUILD_JUCEPLUGIN_CLAP=OFF \
     -Dgearmulator_BUILD_JUCEPLUGIN_LV2=OFF \
     -Dgearmulator_SYNTH_OSTIRUS=ON \
     -Dgearmulator_SYNTH_OSIRUS=OFF \
     -Dgearmulator_SYNTH_VAVRA=OFF \
     -Dgearmulator_SYNTH_XENIA=OFF \
     -Dgearmulator_SYNTH_NODALRED2X=OFF
   ```

3. **Build**:
   ```bash
   cmake --build ./temp/cmake_win64_cross --config Release
   ```

### Option 3: Use GitHub Actions

The repository has GitHub Actions workflows that can build the VST3 automatically. You can:
1. Fork the repository
2. Push the fixed code to your fork
3. Let GitHub Actions build it for you

## What the Fix Does

- **Before**: Categories count would show very large numbers (like 4294967295) when there were many patches
- **After**: Categories count will show the correct number of items, or "?" if the count is too large to display safely
- **Compatibility**: Maintains Windows 7 support using JUCE 7
- **Safety**: Prevents integer overflow that could cause crashes or incorrect display

## Output Location

The compiled VST3 will be located in:
- `temp/cmake_win64/Release/` (Windows build)
- `temp/cmake_win64_cross/Release/` (Cross-compilation build)

Look for files named:
- `OsTIrus.vst3` (main plugin)
- `OsTIrus_FX.vst3` (FX version, if enabled)

## Testing the Fix

After building and installing the VST3:
1. Open your DAW
2. Load OsTIrus
3. Go to the Browser tab
4. Check that "Categories (X)" shows the correct count instead of a very large number

The fix ensures that the Categories count displays correctly and prevents the integer overflow bug that was causing the display issues.
