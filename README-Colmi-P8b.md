# Install Wasp-OS on Colmi P8b smart watch

## Install bootloader

> https://wasp-os.readthedocs.io/en/latest/install.html#daflasher-for-android

* Download and install DaFlasher app and copy the DaFlasher bootloaders to your Android device.
  You will need
  [DaFitBootloader23Hacked.bin](https://github.com/atc1441/DaFlasherFiles/blob/master/DaFitBootloader23Hacked.bin)
  and [FitBootloaderDFU2.0.1.zip](https://github.com/atc1441/DaFlasherFiles/blob/master/FitBootloaderDFU2.0.1.zip).
* Copy [bootloader-daflasher.zip](https://github.com/wasp-os/wasp-os/releases/download/v0.4/wasp-os-0.4.1.zip)
  to your Android device.
* Open the app and connect to the device ("P8").
* Click Select file and choose DaFitBootloader23Hacked.bin, then wait for the
  payload to be transferred and for the install process to complete on the watch
  (leaving three coloured squares on the display).
* Press the Back button to return to the scanner and connect to the device.
  The device name will have changed to "ATCdfu".
* Click Do DFU Update.
* Click Select DFU file and select FitBootloaderDFU2.0.1.zip, then wait for the
  payload to transfer and the update to take place.  The watch should be showing
  a single red square which is captioned ATCnetz.de.
* Click Select DFU file again and select bootloader-daflasher.zip.  Once the
  update is complete the watch will show the Wasp-OS logo and some additional
  on-screen prompt.

> If file upload with DaFlasher app does not work, force-close the app, remove
> its cache and storage data, disable and re-enable Bluetooth and Position and
> try again.

## Install Wasp-OS

> https://github.com/wasp-os/wasp-os/issues/496#issuecomment-2212666500

> https://github.com/wasp-os/wasp-os/issues/222#issuecomment-1007006772

```sh
git clone https://github.com/wasp-os/wasp-os.git
cd wasp-os
make submodules
make build-docker-image
make run-docker-image
echo 'CFLAGS += -DBLUETOOTH_LFCLK_RC' > ./micropython/ports/nrf/boards/p8/mpconfigboard.mk
make -j `nproc` BOARD=p8 all
./tools/ota-dfu/dfu.py -z ./build-p8/micropython.zip -a <BLE_ADDRESS> --legacy # E.g. `C6:D6:D0:C4:24:9F`
```

> https://github.com/wasp-os/wasp-os/issues/343#issuecomment-1636811359

```sh
nvim ./wasp/main.py
```

Add

```py
watch.display.invert(False);
watch.display.write_cmd(0x36); watch.display.write_data(b'\x40');
```

before `wasp.system.schedule()` line.

```sh
./tools/wasptool --upload wasp/main.py
./tools/wasptool --reset
./tools/wasptool --rtc
```

## Upload files

```sh
./tools/wasptool --upload source.py --as path/target.py
```
