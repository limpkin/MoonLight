# Nodes

## Emoji coding:

* 🔥 Effect
* 🚥 Layout
* 💎 Modifier
* ☸️ Supporting node
* 🎨 Using palette
* 💡 WLED origin
* 💫 MoonLight origin
* ♫ Audio reactive FFT based
* ♪ Audio reactive volume based
* 🧊 3D

## Effect 🔥 Nodes
🚧
Precompiled effects can be found in [effects](https://github.com/MoonModules/MoonLight/blob/main/src/MoonLight/Effects.h)

### Solid 🔥💡

* Sends a solid RGB to all lights
* Controls: Red, Green, Blue, Brightness
* Usage: Can be used as background to other effects, order it before other effects

### PanTilt script

* Sends a beatsin to Pan and Tilt which can be sent to Moving Heads (add a Moving head layout node to configure the MHs)
* Controls: BPM, Middle Pan and Tilt, Range and invert
* Usage: Add this effect if moving heads are configured. RGB effects can be added separately e.g. wave to light up the moving heads in wave patterns
* See [E_PanTilt](https://github.com/MoonModules/MoonLight/blob/main/misc/livescripts/E_PanTilt.sc)
* Run script see [How to run a live script](https://moonmodules.org/MoonLight/moonbase/module/liveScripts/#how-to-run-a-live-script)

## Layout 🚥 Nodes
🚧
### SE16 🚥

Layout(s) for Stephan Electronics 16-Pin ESP32-S3 board, using the pins used on the board

* ledsPerPin: the number of LEDs connected to one pin
* pinsAreColumns: are the leds on a pin a row of the effect (width is 1 (or 2) x ledsPerPin). If not set the leds are a column (height is 1 (or 2) x ledsPerPin)
* mirroredPins: If set it is assumed that LEDs are connected with increasing positions on 8 pins on one side of the board and decreasing positions on the 8 pins of the other side of the board. The resulting size will have a width of 8 and the height (or width) will be 2 * ledsPerPin. If not set, the width will be 16 and the height (or width) = ledsPerPin

## Modifier 💎 Nodes
🚧

### Pinwheel 💎💡

Projects 1D/2D effects onto 2D/3D fixtures in a pinwheel pattern.

* **Swirl** option to bend the pinwheel.
* **Reverse** option.
* **Rotation Symmetry**: Controls the rotational symmetry of the pattern.
* **Petals** option to adjust the number of petals.
* **Ztwist** option for 3D fixtures to twist the pattern along the z-axis.
* The virtual layer width is the number of petals. The height is the distance from center to corner

### RippleYZ 💎💡💫

Takes lights of an effect and copies them to other lights. E.g. 1D effect will be rippled to 2D, 2D effect will be rippled to 3D

* shrink: shrinks the original size to towardsY and Z
* towardsY: copies X into Y
* towardsZ: copies XY into Z

Note: some effects already do this theirselves e.g. FreqMatrix runs on 1D but copies to 2D and 3D if size allows.

## Supporting ☸️ Nodes
🚧

### Art-Net ☸️

This node sends the content of the Lights array in Art-Net compatible packages to an Art-Net controller specified by the IP address provided.

* Controller IP: The last segment of the IP address within your local network, of the the hardware Art-Net controller.
* Throttle Speed: set the max frames per second Art-Net packages are send out (also all the other nodes will run at this speed).

Example of compatible controllers can be found [here](https://moonmodules.org/hardware/). Both Art-Net LED controllers and Art-Net DMX controllers can be used as output.

The node supports this setup:
```cpp
    std::vector<uint16_t> hardware_outputs = {1024,1024,1024,1024,1024,1024,1024,1024};
    std::vector<uint16_t> hardware_outputs_universe_start = { 0,7,14,21,28,35,42,49 }; //7*170 = 1190 leds => last universe not completely used
```

Todo: 
* Add controls for other hardware_outputs
* MoonLight can also act as a receiving Art-Net controller 

### AudioSync ☸️ ♫

* listens to audio sent over the local network by WLED-AC or WLED-MM and allows sound reactive effects (♫) to use audio data (volume and bands (FFT))

## Creating new nodes

The nodes system is in principle a general mechanism. Currently it is implemented as a MoonLight feature, might move to a generic MoonBase feature in the future. Currently it is intertwined with MoonLight functionality supporting the Physical and Virtual layer model and supporting the 4 types of nodes: Layout, effect, modifier and supporting node.

The core node functionality supports the following

* setup() and loop()
* controls: each node has a variable number of flexible variables of different types (sliders/range, checkboxes, numbers etc). They are added with the setControl function in the setup()
    * Warning: type of controls
* Firmware or live scripts

MoonLight specific

* Node types: it is recommended that a node is one of the 4 types as described above. However each node could perform functionality of all types. To recognize what a node does the emojis 🚥, 🔥, 💎 and ☸️ are used in the name. The variables hasLayout and hasModifier indicate the specific functionality the node supports. They control when a physical to virtual mapping is recalculated
    * **hasLayout**: a layout node specify the amount of position of lights controlled. E.g. a panel of 16x16 or a cube of 20x20x20. If hasLayout is defined you should implement addLayout calling addLight(position) and addPin() for all the lights. 
      * addPin() is needed if a LED driver is used to send the output to led strips.
    * **hasModifier**: a modifier node which manipulates virtual size and positions and lights using one or more of the functions modifySize, modifyPosition and modifyXYZ.
    * if the loop() function contains setXXX functions is used it is an **effect** node. It will contain for-loops iterating over each virtual ! light defined by layout and modifier nodes. The iteration will be on the x-axis for 1D effects, but also on the y- and z-axis for 2D and 3D effects. setRGB is the default function setting the RGB values of the light. If a light has more 'channels' (e.g. Moving heads) they also can be set. 
* Moving heads
    * **addLight** will show where the moving head will be on the stage. In general only an array of a few lights e.g. 4 moving heads in a row. A moving head effect will then iterate over 4 lights where each light might do something different (e.g. implement a wave of moving head movement)
    * You need to define **channelsPerLight** in the layout node setup() - (it is default 3 to support normal LEDs). Currently MoonLight only supports identical moving heads with the same channels. The first light starts at DMX 0 (+1), the second at DMX channelsPerLight (+1) the third on DMX 2*channelsPerLight (+1) and so on. (+1): DMX typically starts at address 1 while MoonLight internal starts with 0... WIP. We are working on a solution to support different lights e.g a mix of 15 channel lights and 32 channel lights etc. You could set channelsPerLight to a higher number as the real lights channels, e.g. 32 so each lights DMX address starts at a multiple of 32.
    * **Layout**: The layout node also defines which functionality / channels the light support by defining **offsets**. Currently the following offsets are supported: offsetRGB, offsetWhite, offsetBrightness, offsetPan, offsetTilt, offsetZoom, offsetRotate, offsetGobo, offsetRGB1, offsetRGB2, offsetRGB3, offsetBrightness2 and need to be set in the setup() function.
    * The distinction between physical and virtual layer for moving heads is not useful if you have only 2-4 moving heads. However this is a standard MoonLight feature. It might become useful if you have like 8 (identical) moving heads, 4 left and 4 right of the stage, then you can add a mirror modifier and the virtual layer will only control 4 lights, which then will be mapped to 8 physical lights. In theory you can also have a cube of like 512 moving heads and then exotic modifiers like pinwheel could be used to really go crazy. Let us know when you have one of these setups 🚨
    * Moving heads will be controlled using the [ArtNed Node](https://moonmodules.org/MoonLight/moonlight/nodes/#art-net/). addPin is not needed for moving heads, although you might want to attach LEDs for a visual view of what is send to Art-Net.
    * Effect nodes **set light**: Currently setRGB, setWhite, setBrightness, setPan, setTilt, setZoom, setRotate, setGobo, setRGB1, setRGB2, setRGB3, setBrightness2 is supported. In the background MoonLight calculates which channel need to be filled with values using the offsets (using the setLight function).
    * If offsetBrightness is defined, the RGB values will not be corrected for brightness in [ArtNed](https://moonmodules.org/MoonLight/moonlight/nodes/#art-net/).