# Rust, Embedded, ESP

## Core Concepts

Rust has a standard library, but can be used without it.  Omitting the standard library removes a lot of core rust stuff (no heap allocation, nor concurrency or I/O).  So, if you want to go the `no-std` route, you'll need to use drivers and libraries that compensate for this.

## The lay of the land

### Rust

- There is a [Rust Standard Library](https://doc.rust-lang.org/std/)
- There is an official alternative that is lighter-weight: the [Rust Core Library](https://doc.rust-lang.org/core/)
- You can use Rust with `core` *instead* of `std` in order to get a smaller, more portable binary
- Rust is statically linked, and you have to compile all your dependencies
- Cargo is the build tool
  - Configuration: `cargo.toml` , `.cargo/config.toml`, more on this [here](https://doc.rust-lang.org/cargo/reference/config.html)
  - *Features* - a build flag for dependencies - changes how they are built
  - Clean: `cargo clean` 
  - Build: `cargo build`
  - Run: `cargo run`
  - To analyze the dependency tree: `cargo tree --format '{p} {f}'`

### Embedded Rust

- [Rust on Embedded Devices Working Group](https://github.com/rust-embedded) - We are an [official working group](https://www.rust-lang.org/governance/wgs/embedded) of the Rust language. This organization focuses on improving the end-to-end experience of using Rust in resource-constrained environments and non-traditional platforms.

  - [The Embedded Rust Book](https://docs.rust-embedded.org/book/index.html)

- [embassy](https://embassy.dev/) - Embassy is a project to make async/await a first-class option for embedded development.

  - seems to be designed to work in a `no-std` context, without an 'RTOS'

- Hardware Abstraction Layers aka HALs

  - "hal" in this world typically just means "library" .. there really isn't a lot of common abstraction between libraries or across microcontrollers

  - One notable exception is the official rust [embedded-hal](https://github.com/rust-embedded/embedded-hal) project .. but it's minimal, and in its infancy.  Mostly just supports some low-level things, and I'm not sure it's used by much

  - There are a lot of different "hal" (harware abstraction layer) projects

    - some that wrap esp-idf or another RTOS

    - some that are for bare-metal access to particular devices

    - some for particular libraries

### Espressif & ESP32*

- [esp-idf](https://idf.espressif.com/)  - This is Espressif's Official C-Language Development Framework for all their chips
  - based on their fork of FreeRTOS
  - has all the official drivers for their hardware, etc
  - has a configuration system for enabling the features in FreeRTOS
  - Using it:
    - You're supposed to source their stuff before building: `. ${esp-idf-home}/export.sh`
    - Then, they wrap all the tools (`make`, etc) with `idf.py`, so:
      - `idf.py build`
      - `idf.py flash`
      - `idf.py monitor`

### Rust on Espressif/ESP32*

- [embuild](https://github.com/esp-rs/embuild) - A rust build tool that invokes things like esp-idf.  
  - In the case of esp-idf:
    - the esp-idf distribution is automatically installed to `/.embuild/espressif/esp-idf`
    - the `esp-idf` configuration is used at `/sdkconfig.defaults`
- [esp-rs](https://github.com/esp-rs/) organization - "This organization is managed by employees of Espressif as well as members of the community."
  - Documentation/guides
    - https://docs.esp-rs.org/
    - [Rust on ESP Book](https://docs.esp-rs.org/book/)
  - [esp-up](https://github.com/esp-rs/espup) - Tool for installing and maintaining Espressif Rust ecosystem.
    - `espup install`
  - crates
    - crates based on esp-idf:
      - [esp-idf-sys]() - *Raw* Rust bindings for the esp-idf SDK
      - [esp-idf-svc](https://github.com/esp-rs/esp-idf-svc) - *Safe* Rust wrappers for the <u>services</u> in the esp-idf SDK.  Uses the rust `std` library.
      - [esp-idf-hal](https://github.com/esp-rs/esp-idf-hal) - Safe Rust wrappers for the <u>drivers</u> in the esp-idf SDK.  Uses the rust `std` library.
    - bare-metal crates:
      - [esp-hal](https://github.com/esp-rs/esp-hal) - *Bare-metal* (`no_std`) hardware abstraction layer for Espressif devices.
      - [esp-wifi](https://github.com/esp-rs/esp-wifi?tab=readme-ov-file) -  WiFi, Bluetooth and ESP-NOW driver for use with Espressif chips and *bare-metal* Rust

## Ways to use run rust

- direct (i.e. without rust `std`, esp-idf/freeRtos, or really any full-fledged RTOS)
- with esp-idf (freertos)
  - esp-idf first: there is some sort of esp-idf first way to do it?
  - cargo/rust-first: use embuild to link esp-idf into the final binary

##  Gotchas

- For bare metal: On boards with 2 ports, you have to configure the logging framework to print to the appropriate place (e.g. https://github.com/esp-rs/esp-template/issues/125)