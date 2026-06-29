# Cisco Catalyst Switch Recovery

## Objective

Recover a Cisco Catalyst 3750G switch that was stuck at the `switch:` bootloader prompt and return it to a usable IOS state for homelab switching exercises.

## Initial State

- Console access worked through PuTTY.
- The switch displayed the `switch:` prompt.
- A normal `boot` attempt tried to load an IOS image but returned an `invalid argument` error.
- The referenced IOS image was under a flash directory similar to:

```text
flash:c3750-ipbasek9-mz.122-55.SE4/c3750-ipbasek9-mz.122-55.SE4.bin
```

## Recovery Commands Used

```text
switch: flash_init
switch: dir flash:
switch: unset BOOT
switch: dir flash:c3750-ipbasek9-mz.122-55.SE4/
switch: boot flash:/c3750-ipbasek9-mz.122-55.SE4/c3750-ipbasek9-mz.122-55.SE4.bin
```

## Troubleshooting Notes

- The switch was able to read flash, which meant the IOS image was present.
- The bootloader appeared sensitive to the image path format.
- Clearing the BOOT variable removed the stale or broken boot path.
- Manually booting with the full image path allowed the switch to continue.

## Result

The switch booted successfully into Cisco IOS. The hostname returned to the default `Switch`, and privileged EXEC mode was accessible without an existing password.

## Portfolio Value

This recovery demonstrated:

- Serial console access
- Cisco bootloader navigation
- Flash file inspection
- BOOT variable troubleshooting
- Manual IOS boot recovery
- Hardware reuse instead of e-waste
