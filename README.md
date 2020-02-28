# Bootloader

# Step 1 - setup the repository clone

a. Clone the bootloader repo to a directory at the same level of zephyr
b. Add the bootloader directory as a module:
   In `~/.zephyrrc` add the following line
   `export ZEPHYR_EXTRA_MODULES=/home/.../zephyr/custom-bootloader`
   with `...` to create the full path.
c. source zephyr: in the directory above west:
   `source zephyr/zephyr_env.sh`

# Step 2 - create the first stage loader (fsl):

In the directory above zephyr call:
```
west build --pristine -s custom-bootloader/fsl -b nrf52840_pca10056 -- -DDTC_OVERLAY_FILE="../overlays/nrf52840_pca10056_si.overlay fsl.overlay"
```

upload the fsl:
```
nrfjprog --program build/zephyr/zephyr_signed.hex --sectorerase --verify
```

# Step 3 - make sure everything is ready for the tools

In the bootloader directory:
```
pip3 install --user -r scripts/requirements.txt
```

# Step 4 - make a bootloader with bledfu support

In the directory above zephyr call:
```
west build --pristine -s custom-bootloader/bledfu_bootloader -b nrf52840_pca10056 -- -DDTC_OVERLAY_FILE="../overlays/nrf52840_pca10056_si.overlay boot.overlay"
```

sign the bootloader:
```
./custom-bootloader/scripts/zb8tool.py sign -v 0.0.1 -h 512 -sk ./custom-bootloader/root-ec256.pem build/zephyr/zephyr.hex build/zephyr/zephyr_signed.hex
```

upload the bootloader:
```
nrfjprog --program build/zephyr/zephyr_signed.hex --sectorerase --verify
```

# Step 5 - make a app:

In the directory above zephyr call:
```
west build --pristine -s custom-bootloader/app -b nrf52840_pca10056 -- -DDTC_OVERLAY_FILE="../overlays/nrf52840_pca10056_si.overlay"
```

sign the bootloader:
```
./custom-bootloader/scripts/zb8tool.py sign -v 0.0.1 -h 512 -la 0x1F000 -sk ./custom-bootloader/root-ec256.pem build/zephyr/zephyr.hex build/zephyr/zephyr_signed.hex
```

copy the generated zephyr_signed.hex to the download folder of your phone, use nrfConnect to upload:

select dfu
select Application
select zephyr_signed.hex
select NO for the init packet
wait for the upload to finish
hit reset
