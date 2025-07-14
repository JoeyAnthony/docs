---
tags:
  - ADB
  - Android
  - C++ Android
---

# Useful ADB tools
[ADB Source](https://developer.android.com/tools/adb#directingcommands)

### Sending commands to a specific device
1. Use the devices command to get the serial number of the target.
2. Once you have the serial number, use the -s option with the adb commands to specify the serial number.
    - If you're going to issue a lot of adb commands, you can set the $ANDROID_SERIAL environment variable to contain the serial number instead.
    - If you use both -s and $ANDROID_SERIAL, -s overrides $ANDROID_SERIAL.

```pwsh
# Get list of devices
abd devices

# Set default serial
$ANDROID_SERIAL="SomeSerial"

```

### ADB commands
```pwsh
# Using abd commands. See ADB Source link for more info.
adb [-d | -e | -s serial_number] command

# Start interactive shell
# Exit with ctrl+D or type `exit`
adb shell

# Run single shell command
adb shell ls

# Install an apk
adb install path_to_apk

# Copy file or directory from a device
adb pull <remote path> <local path>

# Copy file or diretory to a device
# Note: You can't run executable from the sdcard since it's mounted with `noexec`
adb push <local path> <remote path>

```

# Running native C++ code on Android

This can be done with ADB with the following steps.

## Build executable for Android

## Copy the executable to the phone
Copy the executable to a folder like `data/local/myprog`


### Copy exe to the phone



## Run it through the ADB terminal

- Copy the executable to `/data/local/tmp` with `adb push`
- Then call `chmod +x <executable name>`
- You should be able to run it