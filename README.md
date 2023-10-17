# Visual Studio Code get started solution
This repository builds an ELF file that prints "GetStarted World" and a counter value via semihosting output on an Arm Virtual Hardware model (Cortex-M3).

## How to setup your CMSIS Csolution CLI Environment:
1. Install Microsoft vcpkg
    - Windows
      - cmd
      ```
        curl -LO https://aka.ms/vcpkg-init.cmd && .\vcpkg-init.cmd
      ```
      - powerShell
      ```
        iex (iwr -useb https://aka.ms/vcpkg-init.ps1)
      ```
    - Linux / macOS
      - shell
      ```
        . <(curl https://aka.ms/vcpkg-init.sh -L)
      ```

2. Enable vcpkg in your shell
   - Windows
     - cmd
     ```
       %USERPROFILE%\.vcpkg\vcpkg-init.cmd
     ```
     - powerShell
     ```
       . ~/.vcpkg/vcpkg-init.ps1
     ``` 
   - Linux / macOS
     - shell
    ```
      . ~/.vcpkg/vcpkg-init
    ```
    
3. Activate required tools
    - from vcpkg-configuration.json file searched for in current directory or any parent directory
    ```
      vcpkg activate
    ```
    - from named configuration.json file
    ```
      vcpkg activate --project mypath/vcpkg-configuration.json
    ```
    - via command line (adhoc) 
    ```
      vcpkg use arm:cmsis-toolbox microsoft:cmake microsoft:ninja arm:arm-none-eabi-gcc
    ```

4. Deactivate previous configuration
  ```
    vcpkg deactivate
  ```

5. Update registries - to access latest artifact versions
  ```
    vcpkg  x-update-registry --all
  ```

6. Create a new vcpkg configuration file
  ```
    vcpkg new --application
  ```
  ```
    vcpkg add artifact arm:cmsis-toolbox [--version major.minor.patch]
    vcpkg add artifact microsoft:cmake
    vcpkg add artifact microsoft:ninja
    vcpkg add artifact arm:arm-none-eabi-gcc
  ```
  ```
    vcpkg  activate
  ```

Note: The above setup uses default values instead of the Environment Variables:
  - CMSIS_COMPILER_ROOT `./etc` directory of the CMSIS-Toolbox installation
  - CMSIS_PACK_ROOT

    Platform    | Default path
    :-----------|:------------
    Linux       | ${HOME}/.cache/arm/packs
    Windows     | ${LOCALAPPDATA}/Arm/Packs
    MacOS       | ${HOME}/.cache/arm/packs
    WSL_Windows | ${LOCALAPPDATA}/Arm/Packs

## How to setup your CMSIS Csolution Development Environment:
1. Download & Install [Microsoft Visual Studio Code](https://code.visualstudio.com/download) for your operating system.
2. Launch Visual Studio Code. From the 'View' menu open 'Extensions' (ctrl+shift+x). Search for "Keil Studio Pack" and select the install button.
3. From the 'View' menu open 'Source Control'. Select 'Clone Repository' and copy the url: https://github.com/Open-CMSIS-Pack/vscode-get-started into the input dialog
4. Specify the destination folder to clone to and select 'Open' when asked 'Would you like to open the cloned directory?'
5. Open the 'Explorer' view (ctrl-shift-e) and select the file 'vcpkg-configuration.json'. This file instructs [Microsoft vcpkg](https://github.com/microsoft/vcpkg-tool#vcpkg-artifacts) to install the prerequisite artifacts required for building the solution.
  - ctools 2.0.0  [CMSIS-Toolbox](https://github.com/Open-CMSIS-Pack/devtools/blob/main/tools/projmgr/docs/Manual/Overview.md)
  - cmake 3.25.2
  - ninja 1.10.2
  - arm-none-eabi-gcc 12.2.1-mpacbti (GNU Arm Embedded Toolchain 12.2.1)
6. Open the 'CMSIS' view from the side bar and press the 'Build' button. The last line of the ninja build output will tell you where you can
find the application elf file. Alternatively you can select 'Build' or 'Rebuild' from the context menu of the `*.cprj` file of the solution context
(e.g. hello.debug+avh.cprj)

Note: Any terminal that is openened within VSCode after vcpkg got activated for the folder, will have all the above tools added to the path. 
This allows you to run tools from the [CMSIS-Toolbox](https://github.com/Open-CMSIS-Pack/devtools/blob/main/tools/projmgr/docs/Manual/Overview.md) like:
- [`cpackget`](https://github.com/Open-CMSIS-Pack/cpackget#usage) for installing and uninstalling CMSIS-Packs
- [`csolution`]() for updating, validating and converting from the CMSIS Project Management [YML input format](https://github.com/Open-CMSIS-Pack/devtools/blob/main/tools/projmgr/docs/Manual/YML-Input-Format.md#yaml-input-format)
  to the CMSIS Build [XML `cprj` format](https://open-cmsis-pack.github.io/devtools/buildmgr/latest/element_cprj.html) used by `cbuildgen`.
- [`cbuildgen`](https://open-cmsis-pack.github.io/devtools/buildmgr/latest/cbuildgen.html#cbuildgen_invocation) 
- [`cbuild`](https://github.com/Open-CMSIS-Pack/cbuild#usage) for an orchestrated build of one or more `configurations` of a csolution.
   
## Additional Tools

- [Keil MDK 5.38a](https://www2.keil.com/mdk5/) or later
  - VHT_MPS2_Cortex-M3: [Arm Virtual Hardware](https://arm-software.github.io/AVH/main/overview/html/index.html) for Cortex-M3 (v11.19.23 - Windows only - requires MDK-Professional license)
    - set the environment variable PATH to point to the 'VHT' directory of the MDK installation (`c:\Keil_v5\ARM\VHT`).
  - Arm Compiler 6.19 (included in Keil MDK: Community Edition is sufficient for building the solution)
    - set the environment variable AC6_TOOLCHAIN_6_19_0 to point to the bin directory of the installed toolchain to register the Arm Compiler 6.

## Project Structure

The project is generated using the [CMSIS-Toolbox](https://github.com/Open-CMSIS-Pack/devtools/blob/main/tools/projmgr/docs/Manual/Overview.md) and is written in the [`csolution`](https://github.com/Open-CMSIS-Pack/devtools/blob/main/tools/projmgr/docs/Manual/YML-Format.md) format:

- [`cdefault.yml`] is located in the ./etc directory of the CMSIS-Toolbox. It sets the default toolchain specific commandline options for supported toolchains.
  In case a solution specific version is required, you can copy the file locally and it will be used by the tools instead.
- [`get_started.csolution.yml`](./get_started.csolution.yml) lists and defines the hardware targets, build-types and projects.
- [`hello/hello.cproject.yml`](./hello/hello.cproject.yml) defines the packs, components, source files and output files.

## Build Solution/Project

Use the `cbuild` command from CMSIS-Toolbox to generate and build one or all configurations of the solution:

- find out which `contexts` are specified by the solution:
```bash
./ $ cbuild list contexts get_started.csolution.yml
hello.debug+vht  
hello.release+vht
```

- build the context `hello.debug+vht` and install the required CMSIS Packs if not installed:
```bash
./ $ cbuild get_started.csolution.yml --packs --update-rte --context hello.debug+avh

info cbuild: Build Invocation 2.0.0 (C) 2023 Arm Ltd. and Contributors
ARM::CMSIS
 :
info cbuild: Building context: "hello.debug+avh"
================================================

M650: Command completed successfully.

M652: Generated file for project build: 'hello/tmp/hello/debug/avh/CMakeLists.txt'
 :
info cbuild: build finished successfully!
```

- build the configuration `.debug+avh` using Arm Compiler 6 (AC6)
Open `get_started.csolution.yml` file in the editor and configure AC6 as compiler:
```yaml
  compiler: AC6
```
rebuild the configuration after adding and generating missing configuration files (`--update-rte`):
```bash
./ $ cbuild get_started.csolution.yml --context .debug+avh --packs --update-rte --rebuild
```

## Execute Project

The project is configured for execution on Arm Virtual Hardware (AVH) modelling an MPS2 board running an Arm Cortex-M3 processor. 
This model is part of the Keil MDK Professional Edition for Windows and removes the requirement for a physical hardware board.

```bash
./ $ VHT_MPS2_Cortex-M3 -f vht-config.txt -a out/hello/avh/debug/hello.axf
```
