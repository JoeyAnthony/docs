# Delayed Library Loading

## In CMake
Add delay load linker options through CMake. linking with `delayimp` add standard functions for handling delay loaded dlls from Microsoft.
```cmake
# Delay loading of Simulated Reality
 target_link_libraries(${PROJECT_NAME} PRIVATE delayimp)
 target_link_options(${PROJECT_NAME} PRIVATE "/DELAYLOAD:$<TARGET_FILE_BASE_NAME:3DGameBridge>.dll")
 target_link_options(${PROJECT_NAME} PRIVATE "/DELAYLOAD:$<TARGET_FILE_BASE_NAME:srDirectX::srDirectX>.dll")
```
[Source Delayed Loading CMake](https://www.scivision.dev/cmake-msvc-delayload/)

## In C++
Sources:
- [Source](https://learn.microsoft.com/en-us/cpp/build/reference/understanding-the-helper-function?view=msvc-170#structure-and-constant-definitions
)

- [Extra in depth source](https://learn.microsoft.com/en-us/cpp/build/reference/linker-support-for-delay-loaded-dlls?view=msvc-170#constraints-on-delay-load-dlls)

Then a hook function can be added to handle delay loading manually. You can implement custom behaviour for each event.

```c++
FARPROC WINAPI delayHook(unsigned dliNotify, PDelayLoadInfo pdli)
{
    switch (dliNotify) {
        case dliStartProcessing :

            // If you want to return control to the helper, return 0.
            // Otherwise, return a pointer to a FARPROC helper function
            // that will be used instead, thereby bypassing the rest
            // of the helper.

            break;

        case dliNotePreLoadLibrary :

            // If you want to return control to the helper, return 0.
            // Otherwise, return your own HMODULE to be used by the
            // helper instead of having it call LoadLibrary itself.

            break;

        case dliNotePreGetProcAddress :

            // If you want to return control to the helper, return 0.
            // If you choose you may supply your own FARPROC function
            // address and bypass the helper's call to GetProcAddress.

            break;

        case dliFailLoadLib :

            // LoadLibrary failed.
            // If you don't want to handle this failure yourself, return 0.
            // In this case the helper will raise an exception
            // (ERROR_MOD_NOT_FOUND) and exit.
            // If you want to handle the failure by loading an alternate
            // DLL (for example), then return the HMODULE for
            // the alternate DLL. The helper will continue execution with
            // this alternate DLL and attempt to find the
            // requested entrypoint via GetProcAddress.

            break;

        case dliFailGetProc :

            // GetProcAddress failed.
            // If you don't want to handle this failure yourself, return 0.
            // In this case the helper will raise an exception
            // (ERROR_PROC_NOT_FOUND) and exit.
            // If you choose, you may handle the failure by returning
            // an alternate FARPROC function address.

            break;

        case dliNoteEndProcessing :

            // This notification is called after all processing is done.
            // There is no opportunity for modifying the helper's behavior
            // at this point except by longjmp()/throw()/RaiseException.
            // No return value is processed.

            break;

        default :

            return NULL;
    }

    return NULL;
}

/*
and then at global scope somewhere:
ExternC const PfnDliHook __pfnDliNotifyHook2 = delayHook;
ExternC const PfnDliHook __pfnDliFailureHook2 = delayHook;
*/
```
