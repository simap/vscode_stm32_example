# Basic starter vscode stm32g030

## Prerequisites:

1. STM32CubeMX
1. STM32CubeCLT (installs a bunch of stuff to `/opt/ST/` like cmake, ninja, GCC, etc. and SVD files)
1. Maybe STM32CubeIDE
1. VSCode + 
    1. [STM32 VS Code Extension](https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension)
    1. The above should also install [Embedded Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-embedded-tools) as a dependency. 


## Project setup

Use CubeMX to make an .ioc file to set stuff up.
In CubeMX output CMake type.

My actual repro steps are something like:

1. Output type CubeIDE
1. Use STM32 extension to import local project (command-. to see the hidden .cproject)
1. Mess with getting vcpkg working
    1. the extension has some broken `vcpkg-macos.app` binary. `Failed with exit code "null".`
    1. Using Console or `log stream --predicate "sender == 'AppleMobileFileIntegrity' or sender == 'AppleSystemPolicy' or process == 'amfid' or process == 'taskgated-helper' or process == 'syspolicyd'"` I can see gatekeeper related errors, but nothing that seems to exactly match anything I can find. 
    1. install vcpkg locally
    1. can't seem to get extension to use a local install. 
    1. DO NOT set extension VCPKG_ROOT to local install dir, it will nuke it even the `.git` directory
    1. install vcpkg locally, again
    1. hack the plugin to symlink to working binary. That's in `~/.vscode/extensions/ms-vscode.vscode-embedded-tools-0.7.240217001-darwin-arm64/assets/platform/darwin-arm64/`
    1. It updates it and works for a while, but eventually reinstalls the bad executable.
1. Reimport project and import works!
1. Right click CMakeLists.txt -> Configure All Projects
1. ???
1. Seems to almost work, but cmake complains about versions - the extension uses an older version (3.20) than what vcpkg wants to use and the generated CMakeLists.txt has a `cmake_minimum_required(VERSION 3.22)` or something.
1. Switch CubeMX to Output type CMake, regenerate.
1. Right click CMakeLists.txt -> Configure All Projects
1. Version issue goes away, seems to be working

I should have kept better notes, but I also ran in to some other issues at various points.

1. STM32G030F6Px_FLASH.ld was missing. I think that was with the cubeIDE export.
1. Ended up installing CubeIDE at some point, but never needed to generate a project in there, but some extensions might need it to exist.
1. Can't use the STM32 extension to make a new project, it launches CubeMX but hangs on chip selector step. CubeMX stand alone works.


### Peripheral view during debug
Copy in svd file. Can download from ST website, google. Also in `/opt/ST/STM32CubeCLT_1.15.0/STMicroelectronics_CMSIS_SVD` from the CLT package.

add svdPath to .vscode/launch.json
```
      "svdPath": "${workspaceFolder}/STM32G030.svd",
```

### Map file
add magic to CMakeLists.txt

```
target_link_options(${PROJECT_NAME}
  PUBLIC
    LINKER:-Map=${PROJECT_NAME}.map
)
```

## Using

Run and Debug -> Launch -> Green triangle works. 

It compiles stuff, flashes with stlink, starts debug. Breaks on first bit of code in startup. Run/step, etc work. Can see variables and cpu reg in Variables panel. Watch panel works. See above about SVD file to enable Peripherals panel.

Can build and clean, etc. 

## TODO

Figure out how to:

1. build a non-debug version
1. control optimization settings
1. link in arm math lib, etc.
