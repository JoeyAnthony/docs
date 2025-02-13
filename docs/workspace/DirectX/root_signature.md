---
tags:
  - DirectX12
---


# Root Signature
[Microsoft resource](https://learn.microsoft.com/en-us/windows/win32/direct3d12/root-signature-version-1-1#data_static)

## FLAGS
Can have multiple flags you have to watch out for. `DATA_STATIC` for example These flags are set on the descriptor ranges.


### DATA_STATIC

If this flag is set, the data pointed to by descriptors has been initialized by the time a root descriptor or descriptor table referencing the memory has been set on a command list / bundle during recording, and the data cannot be changed until the command list / bundle has finished executing for the last time.

For bundles, the static duration starts at root descriptor or descriptor table setting during the recording of the bundle, as opposed to recording of a calling command list. In addition, a descriptor table pointing to static data must be set in the bundle and not inherited. It is valid for a command list to use a descriptor table pointing to static data that has been set in a bundle and returned back to the command list.

The flag is available in both descriptor range flags and root descriptor flags.
