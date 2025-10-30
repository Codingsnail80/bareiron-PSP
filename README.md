# bareiron-PSP
Minimalist Minecraft server for memory-restrictive embedded systems.

The goal of this project is to enable hosting Minecraft servers on very weak devices, such as the PSP. The project's priorities are, in order: **memory usage**, **performance**, and **features**. Because of this, compliance with vanilla Minecraft is not guaranteed, nor is it a goal of the project.

- Minecraft version: `1.21.8`
- Protocol version: `772`

> [!WARNING]
> Currently, only the vanilla client is officially supported. Issues have been reported when using Fabric or similar.

## Compilation
Before compiling, you'll need to dump registry data from a vanilla Minecraft server. Create a folder called `notchian` in the bareiron directory, and put a Minecraft server JAR in it. Download it from here (the official download for that specific server version): [server.jar](https://piston-data.mojang.com/v1/objects/6bce4ef400e4efaa63a13d5e6f6b500be969ef81/server.jar) Then return to the bareiron directory and run: `./extract_registries.sh` Finally, run `build_registries.js` with either [bun](https://bun.sh/), [node](https://nodejs.org/en/download), or [deno](https://docs.deno.com/runtime/getting_started/installation/). These are just javascript interpreters. There is a good chance you already have node installed. Once you have one installed the command would look like: `node build_registries.js` Next make sure you have the pspdev library installed on your system. Their offical wiki and download links are here: [PSPDEV](https://pspdev.github.io/installation.html) If your operating system isn't listed then follow their instructions on compiling it. I recomend using a download if possible though. Follow their instructions on how to install the download to your system so that you can use it to compile this project. Once you have the pspdev library you need to make sure your in bareiron directory. Run: `psp-cmake .` This will generate the Makefile. Next run: `make` Once it's fished you can use the generated EBOOT.PBP. It will work in an emulator, however the physical device is not yet supported but it will be.

## Configuration
Configuring the server requires compiling it from its source code as described in the section above.

Most user-friendly configuration options are available in `include/globals.h`, including WiFi credentials for embedded setups. Some other details, like the MOTD or starting time of day, can be found in `src/globals.c`. For everything else, you'll have to dig through the code.

Here's a summary of some of the more important yet less trivial options for those who plan to use this on a real microcontroller with real players:

- Depending on the player count, the performance of the MCU, and the bandwidth of your network, player position broadcasting could potentially throttle your connection. If you find this to be the case, try commenting out `BROADCAST_ALL_MOVEMENT` and `SCALE_MOVEMENT_UPDATES_TO_PLAYER_COUNT`. This will tie movement to the tickrate. If this change makes movement too choppy, you can decrease `TIME_BETWEEN_TICKS` at the cost of more compute.
- If you experience crashes or instability related to chests or water, those features can be disabled with `ALLOW_CHESTS` and `DO_FLUID_FLOW`, respectively.
- If you find frequent repeated chunk generation to choke the server, increasing `VISITED_HISTORY` might help. There isn't _that_ much of a memory footprint for this - increasing it to `64` for example would only take up 240 extra bytes per allocated player.

## Non-volatile storage (optional)
This section applies to those who target ESP variants and wish to persist world data after a shutdown. *This is not necessary on PC platforms*, as world and player data is written to `world.bin` by default.

The simplest way to accomplish this is to set up LittleFS in PlatformIO and comment out the `#ifndef` surrounding `SYNC_WORLD_TO_DISK` in `globals.h`. Since flash writes are typically slow and blocking, you'll likely want to uncomment `DISK_SYNC_BLOCKS_ON_INTERVAL`. Depending on the flash size of your board, you may also have to decrease `MAX_BLOCK_CHANGES`, so that the world data fits in your LittleFS partition.

If using an SD card module or other virtual file system, you'll have to implement the filesystem setup routine on your own. The built-in serializer should still work though, as it uses POSIX filesystem calls.

Alternatively, if you can't set up a file system, you can dump and upload world data over TCP. This can be enabled by uncommenting `DEV_ENABLE_BEEF_DUMPS` in `globals.h`. *Note: this system implements no security or authentication.* With this option enabled, anyone with access to the server can upload arbitrary world data.

## Contribution
- Create issues and discuss with the maintainer(s) before making pull requests. Even for small changes.
- Follow the existing code style. Ensure that your changes fit in with the surrounding code, even if you disagree with the style. Pull requests with inconsistent style will be nitpicked.
- Test your code before creating a pull request or requesting a review, regardless of how "simple" your change is. It's a basic form of respect towards the maintainer and reviewer.
- Development tooling and compilation improvements _are not welcome,_ unless you've worked with the codebase long enough to have noticed practical shortcomings in that area. Adding a single compiler flag is not a meaningful first contribution.
- For information on the Minecraft server protocol, [refer to the wiki](https://minecraft.wiki/w/Java_Edition_protocol/Packets). For everything else, use a [search engine](https://google.com).
