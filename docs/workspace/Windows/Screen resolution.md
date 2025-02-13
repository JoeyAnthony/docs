# Screen Resolution

## DPI awareness
[Source Set DPI Awareness](https://learn.microsoft.com/en-us/windows/win32/hidpi/setting-the-default-dpi-awareness-for-a-process?source=recommendations)

Either use

```c++
// Preferred, also has more features
BOOL SetProcessDpiAwarenessContext(
  [in] DPI_AWARENESS_CONTEXT value
);

or 

// Shcore.lib may have to be linked as well
HRESULT SetProcessDpiAwareness(
  [in] PROCESS_DPI_AWARENESS value
);
```

## Get the native screen resolution (Stack Overflow)
[Source Stack Overflow](https://stackoverflow.com/questions/70976583/get-real-screen-resolution-using-win32-api)

The following answer was tested with `SetProcessDPIAware()` and without. In all cases, it gave the correct values.

I did not found it anywhere on the internet so I am glad to share my knowledge with the world.

### To get real monitor resolution
```c++
void GetMonitorRealResolution(HMONITOR monitor, int* pixelsWidth, int* pixelsHeight)
{
    MONITORINFOEX info = { sizeof(MONITORINFOEX) };
    winrt::check_bool(GetMonitorInfo(monitor, &info));
    DEVMODE devmode = {};
    devmode.dmSize = sizeof(DEVMODE);
    winrt::check_bool(EnumDisplaySettings(info.szDevice, ENUM_CURRENT_SETTINGS, &devmode));
    *pixelsWidth = devmode.dmPelsWidth;
    *pixelsHeight = devmode.dmPelsHeight;
}
```
It will return that native resolution in any case, even if the OS tries to lie to you due to the DPI awareness of the process.

### To get the scaling ratio between the virtual resolution and real resolution
```c++
float GetMonitorScalingRatio(HMONITOR monitor)
{
    MONITORINFOEX info = { sizeof(MONITORINFOEX) };
    winrt::check_bool(GetMonitorInfo(monitor, &info));
    DEVMODE devmode = {};
    devmode.dmSize = sizeof(DEVMODE);
    winrt::check_bool(EnumDisplaySettings(info.szDevice, ENUM_CURRENT_SETTINGS, &devmode));
    return (info.rcMonitor.right - info.rcMonitor.left) / static_cast<float>(devmode.dmPelsWidth);
}
```
This will give you a ratio of the real resolution relative to the virtual resolution of the given monitor.

If the main DPI of the main monitor is 225% and on the second monitor it is 100%, and you run this function for the second monitor, you will get 2.25. because `2.25 * real resolution` = `the virtual resolution` of the monitor.

If the second monitor has 125% scaling (while the main monitor is still 225% scaling), then this function will return you `1.79999995` because 125% relative to 225% is this value (225/125 = 1.8), and again - 1.8 * real resolution=the virtual resolution of 125%`

### To get the real DPI value (not relative to anything)
Given that monitor, A has 225% DPI, and monitor B has 125% DPI, as I said above, you will not get 1.25 for the second monitor (if you run the function on the second monitor. You will get 1.8 as I said).

To overcome this, use this function:

```c++
float GetRealDpiForMonitor(HMONITOR monitor)
{
    return GetDpiForSystem() / 96.0 / GetMonitorScalingRatio(monitor);
}
```
This function depends on the previous function that I wrote above (the function `GetMonitorScalingRatio` that you need to copy)

This will give you the correct value.
