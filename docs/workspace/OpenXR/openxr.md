# OpenXR
## Loader
The loader loads a manifest file from the registry key `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Khronos\OpenXR\1` with value `ActiveRuntime`

It seems to load dependency dlls next to the manifest file instead of next to the dll.

## Testing applications
- [Open Brush](https://github.com/icosa-foundation/open-brush)
- [V-Sekai](https://github.com/V-Sekai/v-sekai-game)
- [Mine Test](https://github.com/DonFlymoor/minetest-openXR)


## Runtime problems
-   Single fence per proxy swapchain (and window swapchain). Both framebuffered resources use the same fence..
-   Proxy swapchian (and window swapchain) Heap usage migth be wrong. Not using an heap offset to use the second image perhaps..
