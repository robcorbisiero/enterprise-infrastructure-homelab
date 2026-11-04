# Cisco Catalyst Switch Recovery

## What I was doing

My Cisco 3750G stopped at the `switch:` prompt instead of loading IOS. I used the temporary console cable to inspect flash and get it booting again.

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

## How it turned out

The switch booted successfully into Cisco IOS. The hostname returned to the default `Switch`, and privileged EXEC mode was accessible without an existing password.
