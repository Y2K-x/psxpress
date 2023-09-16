![PSXpress Logo](https://github.com/Y2K-x/psxpress/tree/main/Logos/psxpress_logo_scaled.png)

# PSXpress
A compact handheld PlayStation console using original PS1 hardware.

## Scope
This end goal of this project is to create a compact PS1 portable using modern portablizing techniques. A special motherboard trim for PU-23 units is being developed that leverages the CD subsystem emulation capabilities of the PSIO ODE. Using PSIO, a trim size of roughly 80mm<sup>2</sup> is achievable, making it the smallest PS1 trim that has been concieved.

Currently, this first iteration of PSXpress is being designed for the [BitBuilt 2023 Summer Build Competition](https://bitbuilt.net/forums/index.php?threads/bitbuilts-2023-summer-building-competition.5710/), however I have plans to continue it further, gradually refining the design to become a highly integrated build using a fully custom motherboard. This has multiple advantages, but will take a long time to complete. The first iteration of the build will be using standard motherboard trimming techniques & a modular design with hand wired relocations.

The ultimate purpose of this project is to forward my electronics design learning goals & to create a build that encorporates the best techniques that the portablizing community has to offer.

## Progress
Currently, the first iteration of the system has yet to be completed. The hope was to have it completed for the BitBuilt Summer Build Competition deadline on October 1st, however that unfortunately does not seem like it will happen. Regardless, I have made a considerable amount of progress and fully intend on seeing this build through to the end.

As of now, I have finished these parts of the build:
- Trim development - the trim layout has been finalized and all relocations have been mapped out to be within the trim area
- Video DAC Relocation - the Video DAC on the PS1 needs to be relocated in order to be within the trim area. I have developed a PCB that accepts the VDAC chip and outputs a clean RGBs signal.
- Power Management - For a portable build, a compact and efficient power management solution is key. I have developed a small 3cm * 2cm board that accepts a 12V DC input and can charge 18650/21700 lithium batteries in a 1S setup. This power management system also has 2 on board 2A voltage regulators for a 3v5 rail (main PS1 system power) and a 5V rail for peripherals & other things.

These are the parts of the project that need to be completed still:
- Case design: I have a rough idea on the layout of the build, but I have been holding off on 3D CAD work until I can prove that the trim itself will work how I want it to
- The trim + relocations: While the trim layout is completed, I still need to perform all the relocations & actually trim the board. The VDAC relocation is done and installed, and I have tested the BIOS and PSIO relocation, however the final relocations have yet to be performed.
- Controller emulation: I am looking into emulating a PS1 controller & memory card using the RP2040 microcontroller. I'm basing the code off the [PicoMemcard](https://github.com/dangiu/PicoMemcard) firmware, which is a fully featured memory card emulator for the PS1. I've done all the coding for it but I have yet to properly test it.
- Audio DAC & Amp: I will be using the [4LayerTech U-AMP 2](https://4layertech.com/products/u-amp-2) as the audio DAC and amplifier. This tiny board provides high quality audio output from digital & analog audio sources, however its firmware needs to be modified to support the PS1. I have made the necessary modifications to the code but have not tested it yet. If I can get this working, I will be working with the 4LayerTech team to add proper PS1 support to the product.
- Final assembly, obviously.

I'd say I'm approaching the 50% completion mark, but there still a lot more to be done.

## Repository Contents
Currently this repository contains some of the research material, documentation, and design files I've been working on, but not all, since many of them are still in a state that I don't feel comfortable sharing yet. Here is the layout of the repo as it currently stands.

### PCB files & CAD (/CAD)
This folder contains all CAD & PCB design files for this project. Please see below for more details on each subfolder.

#### PSXpress PMS (/CAD/PCB/psxpress-pms)
*All files in this folder are licensed under the TAPR Open Hardware License. Please see /CAD/PCB/psxpress-pms/LICENSE.txt for more information.*

This folder contains the design docs for the PSXpress Power Management System (PMS). It's functionality is as follows:
- Fully standalone design: no MCU or firmware to program, just assemble and go
- 3.9 to 13.5V input - 3A max input & charge current - good for 9/12V @ 3A USB-C PD or barrel jack input
- High efficiency 3v5 & 5V buck/boost regulators
- 2A max output on output rails
- Charge & play support
- Integrated battery management & charging
- 1S Battery Setups Only! Recommended setup is 1S2P 18650 cells or 1 21700 cell - I probably wouldn't do a singular 18650 since this will always charge the batteries at 3A no matter what
- Reverse battery polarity protection (separate packside protection circuit needed, example [here](https://www.aliexpress.us/item/3256805302254769.html))
- Soft power switch using MAX16054 - use a normally open SPST switch
- Charging LED
- Dimensions : Aprox. 25mm x 30mm

This board has been designed to be manufactured by DKRed. JLCPCB/PCBWay should also be able to manufacture these boards as well if you want to give them a try, but I cannot make any guarantees. I am not confident that OSH Park can manufacure these boards as they are quite small and detailed.

#### Video DAC Relocation (/CAD/PCB/pu-23\_VDAC\_reloc)
*All files in this folder are licensed under the TAPR Open Hardware License. Please see /CAD/PCB/pu-23_VDAC_reloc/LICENSE.txt for more information.*

This folder contains the design docs for the PSXpress Video DAX relocation board. It's functionality is as follows:
- Accepts a PU-23 Video DAC IC
- Takes in the PS1's 24bit digital video bus and outputs clean RGB with Composite Sync
- Generates a composite sync signal from the PS1's Sync on Luma signal, since tapping into the standard CSync signal the PS1 generates messes up the VDAC output.
  - The use of CSync on this board is not required, as the PS1 also outputs a H/V sync signal off the GPU if you would prefer to use that. I have not tested it, but that may be more useful if you intend on hooking up the PS1 directly to a VGA display driver board.
  - The generated CSync signal has been confirmed to work with the GBS-Control scaler (more specifically, [Shinobi Scaler by YveltalGriffin](https://github.com/mackieks/Shinobi-Scaler). I have not tested it with other scalers or directly with display drivers that accept CSync.

Please note: the PCB itself does not have any silkscreen markings for the pinout of the solder pads on it's edge. Please see the kicad pcb file for that information. I will document this more properly later on, or I might try and figure out a way to include the pinout markings on the PCB itself in the future.

This board has been designed to be manufactured by JLCPCB. PCBWay should also be able to fabricate this. DKRed cannot by default since this board is 0.8mm thick, but honestly there is no reason why this needs to be 0.8mm, I was just trying to keep the relocation stack-up as thin as possible. 1.6mm should be fine if you want to have DKRed fabricate this, assuming it's within their size tollerances. OSH Park may also be able to fabricate this using their 0.8mm 2oz service, but I cannot make any guarantees here.

### Research Documentation (/Documentation)
*All files in this folder are licensed under the public domain (Creative Commons CC0 in technicality). Please see /Documentation/LICENSE.txt for more information.*

This folder contains all the research documentation that I have created, and also will contain a list of further docs that I used to create this project once I get around to compiling that information. Currently, it contains:

- PU-18 SPU Relocation Pin Mapping (pu18-spu-mapping.png): Currently, in order to achieve the small trim, the SPU from a PU-18 motherboard will have to be frankenstein'd onto the board. I have mapped out all the required signals in order to perform this relocation. One can trim a PU-18 board to remove the section of the board that this image covers. Treat the cropping of the image as the trim lines for the board. I will eventually be looking into a SPU replacement so that sacrificing 2 PS1 systems will not be required.

### Logos & Other Artistic Works (/Logos)
*All files in this folder are licensed under the Creative Commons CC BY-SA License. Please see /Logos/LICENSE.txt for more information.*

This folder contains the logo design that I created for this project. I've included PNGs and an SVG of the logo. This logo is also used in the CAD files and other documentation.

## Special Thanks
I'd like to give a special thanks to the following communities and persons for all their support with this project so far:

- [BitBuilt]() & its [Discord server](https://discord.com/invite/6z8FSv8B)
- [PSX.dev](https://discord.gg/QByKPpH)
- [ReSwitched](https://discord.gg/ZdqEhed)
- [YveltalGriffin](https://github.com/mackieks)
- [Gmanmodz](https://github.com/Gmanmodz)
- [Aurelio](https://github.com/Aurelio92)
