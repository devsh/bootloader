# Bootloader

# Step 1 - setup the repository clone

a. Clone the bootloader repo to a directory at the same level of zephyr
b. Add the bootloader directory as a module:
   In `~/.zephyrrc` add the following line
   `export ZEPHYR_EXTRA_MODULES=/home/.../zephyr/bootloader`
   with `...` to create the full path.
c. source zephyr: in the directory above west:
   `source zephyr/zephyr_env.sh`

# Step 2 - create the first stage loader (fsl):

In the directory above zephyr call:
```
west build --pristine -s bootloader/fsl -b nrf52840_pca10056 -- -DDTC_OVERLAY_FILE="../overlays/nrf52840_pca10056_si.overlay fsl.overlay"
```

# Step 3 - make sure everything is ready for the tools

In the bootloader directory:
```
pip3 install --user -r scripts/requirements.txt
```

# Step 4 - ...