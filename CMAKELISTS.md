

THE FILE:
```cmake
# Generated Cmake Pico project file

  

cmake_minimum_required(VERSION 3.13)

  

set(CMAKE_C_STANDARD 11)

set(CMAKE_CXX_STANDARD 17)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

  

# Initialise pico_sdk from installed location

# (note this can come from environment, CMake cache etc)

  

# == DO NOT EDIT THE FOLLOWING LINES for the Raspberry Pi Pico VS Code Extension to work ==

if(WIN32)

set(USERHOME $ENV{USERPROFILE})

else()

set(USERHOME $ENV{HOME})

endif()

set(sdkVersion 2.3.0)

set(toolchainVersion 15_2_Rel1)

set(picotoolVersion 2.3.0)

set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)

if (EXISTS ${picoVscode})

include(${picoVscode})

endif()

# ====================================================================================

set(PICO_BOARD pico CACHE STRING "Board type")

  

# Pull in Raspberry Pi Pico SDK (must be before project)

include(pico_sdk_import.cmake)

  

project(Blinky C CXX ASM)

  

# Initialise the Raspberry Pi Pico SDK

pico_sdk_init()

  

# Add executable. Default name is the project name, version 0.1

  

add_executable(Blinky Blinky.c )

  

pico_set_program_name(Blinky "Blinky")

pico_set_program_version(Blinky "0.1")

  

# Generate PIO header

pico_generate_pio_header(Blinky ${CMAKE_CURRENT_LIST_DIR}/blink.pio)

  

# Modify the below lines to enable/disable output over UART/USB

pico_enable_stdio_uart(Blinky 1)

pico_enable_stdio_usb(Blinky 0)

  

# Add the standard library to the build

target_link_libraries(Blinky

pico_stdlib)

  

# Add the standard include files to the build

target_include_directories(Blinky PRIVATE

${CMAKE_CURRENT_LIST_DIR}

)

  

# Add any user requested libraries

target_link_libraries(Blinky

hardware_spi

hardware_i2c

hardware_dma

hardware_pio

hardware_interp

hardware_timer

hardware_watchdog

hardware_clocks

)

  

pico_add_extra_outputs(Blinky)
```


### What a CMakeLists.txt actually is

A `CMakeLists.txt` is a script written in CMake's own scripting language (not C, not Python — its own thing, with its own commands like `set()`, `if()`, `include()`). When you run `cmake`, it reads this file top-to-bottom and executes each command, one after another, like a normal script. As it runs, it builds up information about what needs to be compiled and how. At the end, it writes out a Makefile (or Ninja file, etc.) that actually does the compiling.

Every command in CMake looks like `command_name(arguments)`. That's the entire syntax you need to recognize a line as "doing something."

---

### Line by line

cmake

```cmake
# Generated Cmake Pico project file
```

`#` starts a comment, exactly like `//` in C but for the whole line. CMake ignores it entirely.

cmake

```cmake
cmake_minimum_required(VERSION 3.13)
```

This is a command call: the command is `cmake_minimum_required`, and its argument is `VERSION 3.13`. It tells CMake: "refuse to proceed if the installed CMake is older than version 3.13." Different CMake versions support different features, so this is a safety check — like a `#version` pragma. This should always be the very first real line in the file.

cmake

```cmake
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```

`set(NAME VALUE)` is CMake's assignment statement — it creates or overwrites a **variable** called `NAME` with `VALUE`. Unlike C, there are no types to declare; every CMake variable is just stored as text.

- `CMAKE_C_STANDARD` and `CMAKE_CXX_STANDARD` are **variables CMake itself looks for by name**. If you set them, CMake knows to pass `-std=c11` and `-std=c++17` to the compiler for you, instead of you having to add those flags manually. This is a recurring pattern: CMake has hundreds of "magic" variable names that, when set, change its behavior.
- `CMAKE_EXPORT_COMPILE_COMMANDS ON` tells CMake to generate a file called `compile_commands.json` — a machine-readable list of the exact compiler command used for every source file. This is mainly used by editor tooling (like clangd, for autocomplete/go-to-definition), not by the build itself.

cmake

```cmake
if(WIN32)
    set(USERHOME $ENV{USERPROFILE})
else()
    set(USERHOME $ENV{HOME})
endif()
```

`if()` / `else()` / `endif()` work like a normal if-statement — every `if` must be closed with a matching `endif()`.

- `WIN32` is another built-in variable, automatically set to true by CMake when you're running on Windows. So this reads: "if we're on Windows, do X, otherwise do Y."
- `$ENV{NAME}` is special syntax to read an **operating system environment variable** (the same kind you'd see with `echo $HOME` in a terminal), as opposed to a plain `$` which reads a CMake variable.
- So: this block finds the user's home directory — `%USERPROFILE%` on Windows, `$HOME` on Linux/Mac — and stores it in a CMake variable called `USERHOME`.

cmake

```cmake
set(sdkVersion 2.3.0)
set(toolchainVersion 15_2_Rel1)
set(picotoolVersion 2.3.0)
set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)
```

More plain `set()` assignments. New thing here: `${USERHOME}`. This is **variable expansion/substitution** — anywhere you write `${VARNAME}`, CMake replaces it with that variable's current value before evaluating the line, exactly like `$VARNAME` in a bash script. So this last line builds a file path by gluing the user's home directory together with a fixed subpath.

cmake

```cmake
if (EXISTS ${picoVscode})
    include(${picoVscode})
endif()
```

- `EXISTS <path>` is a check usable inside `if()` conditions — it's true if a file or directory actually exists at that path on disk.
- `include(<file>)` is like `#include` in C, but for CMake scripts: it pastes the contents of another `.cmake` file in at this exact point and executes it as if it were written here directly.

So altogether: "If the VS Code Pico extension has installed its helper config file, load it." If it doesn't exist (e.g., you're not using that VS Code extension), this whole block silently does nothing — which is why it's safe to leave in even if you don't use VS Code.

cmake

```cmake
set(PICO_BOARD pico CACHE STRING "Board type")
```

This is `set()` again, but with two new pieces: `CACHE` and `STRING "Board type"`.

Normal variables (like the ones above) only exist for the duration of this CMake run and are forgotten afterward. A **cache variable** is different — it's saved to a file called `CMakeCache.txt` in your build directory, so it persists across multiple runs of `cmake`, and it can be overridden from the command line (`cmake -DPICO_BOARD=pico_w ..`) without editing this file. `STRING` is the variable's type (for tooling like `cmake-gui` to know how to display it — a text box vs. a checkbox), and `"Board type"` is just a human-readable description shown in that GUI.

So this line means: "there's a setting called `PICO_BOARD`, default it to `pico`, but let the user override it externally." This is how the same CMakeLists.txt supports building for different Pico board variants without editing the file.

cmake

```cmake
include(pico_sdk_import.cmake)
```

Same `include()` as before — pulls in another `.cmake` file (this one ships with the project, sitting next to this CMakeLists.txt). That file sets up all the machinery needed to locate and use the Raspberry Pi Pico SDK. It has to happen before `project()` (next line) because it needs to configure the compiler toolchain before CMake locks that in.

cmake

```cmake
project(Blinky C CXX ASM)
```

`project()` is a required, special command — every CMakeLists.txt needs one near the top. It:

1. Names the overall project (`Blinky`), stored in a variable `PROJECT_NAME`.
2. Declares which languages are used — here C, C++, and ASM (assembly). CMake uses this to go find working compilers for each of these languages on your system and verify they work, before doing anything else.

cmake

```cmake
pico_sdk_init()
```

`pico_sdk_init` is **not** a built-in CMake command — it's a custom command defined inside the Pico SDK files you `include()`d earlier. CMake lets you define your own reusable commands (called **functions** or **macros**) inside `.cmake` files, and once included, you call them exactly like built-in ones. This call does the SDK's internal setup (finding all its library source code, etc.) so you can use it below.

cmake

```cmake
add_executable(Blinky Blinky.c )
```

This is the first command that actually defines something to _build_. `add_executable(NAME sources...)` creates a build **target** — a named thing CMake will produce — called `Blinky`, an executable, built by compiling the source file `Blinky.c`. Everything from here on refers back to this target by name (`Blinky`).

cmake

```cmake
pico_set_program_name(Blinky "Blinky")
pico_set_program_version(Blinky "0.1")
```

More custom Pico SDK functions. They just embed metadata (a name string, a version string) into the final compiled binary — cosmetic, doesn't affect the logic of the build.

cmake

```cmake
pico_generate_pio_header(Blinky ${CMAKE_CURRENT_LIST_DIR}/blink.pio)
```

Another Pico-SDK-specific function. The Pico has special hardware called PIO (Programmable I/O), programmed in a tiny custom assembly language stored in `.pio` files. This command runs a code generator that converts `blink.pio` into a C header file so your C code can use it, and makes sure that generation step re-runs automatically if `blink.pio` changes.

New variable: `CMAKE_CURRENT_LIST_DIR` is a built-in variable automatically set to the absolute directory path containing the CMakeLists.txt file that's currently executing. Using it instead of a relative path guarantees CMake finds `blink.pio` regardless of what directory you happened to run the `cmake` command from.

cmake

```cmake
pico_enable_stdio_uart(Blinky 1)
pico_enable_stdio_usb(Blinky 0)
```

Pico SDK functions toggling a setting on/off for target `Blinky`: `1` = enabled, `0` = disabled. This controls whether `printf()`-style output in your code gets routed over the UART serial pins (enabled) or over USB (disabled), for debugging.

cmake

```cmake
target_link_libraries(Blinky
        pico_stdlib)
```

This is a core, built-in CMake command, and an important one. `target_link_libraries(TARGET LIBRARIES...)` says: "when building `TARGET`, also link it against these libraries." `pico_stdlib` is a library provided by the Pico SDK (standard I/O, GPIO basics, timing functions, etc.) — without this line, your code couldn't call any Pico SDK functions even if you `#include`d their headers, because the compiled library code wouldn't be linked in.

cmake

```cmake
target_include_directories(Blinky PRIVATE
        ${CMAKE_CURRENT_LIST_DIR}
)
```

`target_include_directories(TARGET ... DIRS...)` tells the compiler "when compiling this target, also look in these directories for `#include` files" — equivalent to the compiler's `-I` flag. Here it adds the CMakeLists.txt's own directory, presumably because `Blinky.c` includes a locally-generated header (like the PIO header from earlier) that lives right there.

The new keyword here is `PRIVATE`. This controls **visibility/propagation** and only matters once you have multiple targets depending on each other:

- `PRIVATE` — only `Blinky` itself uses this include path.
- `PUBLIC` — `Blinky` uses it, _and_ anything that later links against `Blinky` also inherits it automatically.
- `INTERFACE` — `Blinky` itself doesn't use it, but anything linking against `Blinky` does.

Since `Blinky` is a final executable (nothing else will ever link against it), `PRIVATE` vs `PUBLIC` makes no practical difference here — but it's good habit-forming: for libraries, this distinction matters a lot.

cmake

```cmake
target_link_libraries(Blinky 
        hardware_spi
        hardware_i2c
        hardware_dma
        hardware_pio
        hardware_interp
        hardware_timer
        hardware_watchdog
        hardware_clocks
        )
```

Same `target_link_libraries` command as before, called a second time (that's allowed — CMake just accumulates the list rather than overwriting it), adding a bunch more Pico SDK hardware-driver libraries: SPI, I2C, DMA, PIO, interpolator, timer, watchdog, and clock control modules. Each name here corresponds to a chunk of Pico SDK source code that gets compiled in and made available to `Blinky.c`.

cmake

```cmake
pico_add_extra_outputs(Blinky)
```

One more Pico-SDK-custom function. Normally, compiling produces just an `.elf` file. This command tells the build to also produce the extra output formats a Pico actually needs to be flashed with — `.uf2` (drag-and-drop onto the Pico's USB mass-storage bootloader), `.bin`, `.hex`, and a disassembly listing.

---

### The shape of the whole file

Everything before `pico_sdk_init()` is **setup/configuration** — locating tools, setting global options. Everything from `add_executable()` onward is **defining what to actually build**: one target (`Blinky`), its source file, its libraries, its include paths, and its extra outputs. This two-part shape (global setup, then per-target definitions) is typical of almost every CMakeLists.txt you'll encounter, Pico or otherwise.



