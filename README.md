# Basic starter vscode stm32g030

## Project setup

Use CubeMX to make an .ioc file to set stuff up.
In CubeMX output CMake type.

My actual repro steps are something like:

1. Output type CubeIDE
1. Use STM32 extension to import local project (command-. to see the hidden .cproject)
1. Mess with getting vcpkg working
    1. install locally, then hack the plugin to symlink to working binary.
    1. It updates it and works for a while, but eventually reinstalls the bad executable.
1. Reimport project and import works!
1. Right click CMakeLists.txt -> Configure All Projects
1. ???
1. Seems to almost work, but cmake complains about versions - the extension uses an older version (3.20) than what vcpkg wants to use and the generated CMakeLists.txt has a `cmake_minimum_required(VERSION 3.22)` or something.
1. Switch CubeMX to Output type CMake, regenerate.
1. Right click CMakeLists.txt -> Configure All Projects
1. Version issue goes away, seems to be working


### Peripheral view during debug
Copy in svd file. Can download from ST website, google.
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
