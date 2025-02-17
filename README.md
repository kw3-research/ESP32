## ESP32 grblHAL driver

__Important:__  
If enabling ftp upload to the SD card then _ffconf.h_ in the ESP SDK has to be edited, `#define FF_FS_RPATH` must be changed to 2 or you will get a compiler error.  
_ffconf.h_ is located in the subfolder _esp-idf\components\fatfs\src_ in the ESP32 SDK installation. The ESP32 SDK is typically installed in the user folder.

### How to build using ESP-IDF v4.3:

While this manual briefly describes basic build process on Linux OS, you can find more details
as well as differences for building on other OS at this webpage:

https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html#step-2-get-esp-idf

First you have to prepare esp-idf v4.3:

```bash
#Create directory and clone esp-idf into it:
mkdir -p ~/esp
cd ~/esp
git clone -b release/v4.3 --recursive --shallow-submodules https://github.com/espressif/esp-idf.git

#Prepare build environment and toolchain:
cd ~/esp/esp-idf
./install.sh
. ~/esp/esp-idf/export.sh
```

Then get the grblHAL driver code:

```bash
git clone --recursive git://github.com/grblHAL/ESP32
git pull --recurse-submodules
git submodule update --remote
```

Once you have ESP-IDF prepared, go back to grblHAL directory and copy `grbl/*` to `drivers/ESP32/grbl/` in grblHAL repo
as well as desired plugins into the respective folders. eg. `plugins/spindle/*` to `drivers/ESP32/spindle/` and so on...

Modify settings in `grbl/config.h` and `CMakeLists.txt` as needed.
Pin assignments and board specific config is in `*_map.h` files for each individual board.

Go into the `drivers/ESP32/` directory and run `idf.py build`.
This will build the firmware image which can be later flashed into ESP32 device.

Note that `idf.py` command is only available in terminal window which was previously configured
using the `. ~/esp/esp-idf/export.sh` command.

After build is completed you will be instructed on how to flash firmware into the device.
Typicaly you can use command similar to this: `idf.py -p /dev/ttyUSB0 flash`

Once flashing is complete, your CNC controller is ready to be configured and used.


### Using Docker

If you're familiar with [Docker](https://docker.io), you can use it to build grblHAL in a self-contained environment without installing the complete toolchain on your system:

- prepare and configure the codebase as described above
- build with `docker run -it --rm -v $(pwd):/grbl -w /grbl/drivers/ESP32 espressif/idf:release-v4.3 idf.py build`
- flash with `docker run -it --rm -v $(pwd):/grbl --privileged -v /dev:/dev -w /grbl/drivers/ESP32 espressif/idf:release-v4.3 idf.py -p /dev/ttyUSB0 flash`

### Changelog/Notes:

---

__NOTE:__ `grbl/config.h` or `CMakeLists.txt` may need modification before compilation. If needed an `#error` (with instructions) will be generated when compiling.


__NOTE:__ Configuration has been simplified a bit, primarily change options in [`CMakeLists.txt`](https://github.com/grblHAL/ESP32/blob/master/main/CMakeLists.txt) to enable/disable.
Configuration of options in _my_machine.h_ is turned off in CMakeLists.txt by default due to this.


---

### Credits:

index.htm.gz is Copyright (c) 2019 Luc Lebosse - from his [ESP3D-WEBUI](https://github.com/luc-github/ESP3D-webui), I may have pulled a few lines from his backend code too.

dns_server.c is Copyright (c) 2019 Tony Pottier - from his [ESP32 WiFi Manager](https://github.com/tonyp7/esp32-wifi-manager) 

Snippets of code is extracted from Espressif ESP-IDF examples which are public domain.

---
2021-11-24
