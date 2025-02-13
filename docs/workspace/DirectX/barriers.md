---
tags:
  - DirectX12
---


# Barriers
[Microsoft resource](https://learn.microsoft.com/en-us/windows/win32/direct3d12/using-resource-barriers-to-synchronize-resource-states-in-direct3d-12) 

## Split Barriers

A resource transition barrier with the D3D12\_RESOURCE\_BARRIER\_FLAG\_BEGIN\_ONLY flag begins a split barrier and the transition barrier is said to be pending. While the barrier is pending the (sub)resource cannot be read or written by the GPU. The only legal transition barrier that can be applied to a (sub)resource with a pending barrier is one with the same _before_ and _after_ states and the D3D12\_RESOURCE\_BARRIER\_FLAG\_END\_ONLY flag, which barrier completes the pending transition.

Split barriers provide hints to the GPU that a resource in state _A_ will next be used in state _B_ sometime later. This gives the GPU the option to optimize the transition workload, possibly reducing or eliminating execution stalls. Issuing the end-only barrier guarantees that all GPU transition work is finished before moving onto the next command.

Using split barriers can help to improve performance, especially in multi-engine scenarios or where resources are read/write transitioned sparsely throughout one or more command lists.

## Example of split barriers
The following example shows how to use a split barrier to reduce pipeline stalls. The code that follows does not use split barriers:

```c++ 
D3D12_RESOURCE_BARRIER BarrierDesc = {};
BarrierDesc.Type = D3D12_RESOURCE_BARRIER_TRANSITION; 
BarrierDesc.Flags = D3D12_RESOURCE_BARRIER_NONE; 
BarrierDesc.Transition.pResource = pResource; 
BarrierDesc.Transition.Subresource = 0; 
BarrierDesc.Transition.StateBefore = D3D12_RESOURCE_STATE_COMMON; BarrierDesc.Transition.StateAfter = D3D12_RESOURCE_STATE_RENDER_TARGET; 

pCommandList->ResourceBarrier( 1, \&BarrierDesc ); 

Write(pResource); // ... render to pResource 
OtherStuff(); // .. other gpu work 

// Transition pResource to PIXEL\_SHADER\_RESOURCE 
BarrierDesc.Transition.StateBefore = D3D12_RESOURCE_STATE_RENDER_TARGET; BarrierDesc.Transition.StateAfter = D3D12_RESOURCE_STATE_PIXEL_SHADER_RESOURCE; 

pCommandList->ResourceBarrier( 1, &BarrierDesc ); Read(pResource); // ... read from pResource
```

The following code uses split barriers:
```c++
D3D12_RESOURCE_BARRIER BarrierDesc = {}; 
BarrierDesc.Type = D3D12_RESOURCE_BARRIER_TRANSITION; 
arrierDesc.Flags = D3D12_RESOURCE_BARRIER_NONE; 
BarrierDesc.Transition.pResource = pResource; 
BarrierDesc.Transition.Subresource = 0; 
BarrierDesc.Transition.StateBefore = D3D12_RESOURCE_STATE_COMMON; BarrierDesc.Transition.StateAfter = D3D12_RESOURCE_STATE_RENDER_TARGET; 

pCommandList->ResourceBarrier( 1, \&BarrierDesc ); Write(pResource); // ... render to pResource 

// Done writing to pResource. Start barrier to PIXEL_SHADER_RESOURCE and 
// then do other work 
BarrierDesc.Flags = D3D12_RESOURCE_BARRIER_BEGIN_ONLY; 
BarrierDesc.Transition.StateBefore = D3D12_RESOURCE_STATE_RENDER_TARGET; BarrierDesc.Transition.StateAfter = D3D12_RESOURCE_STATE_PIXEL_SHADER_RESOURCE;
pCommandList->ResourceBarrier( 1, &BarrierDesc ); 
OtherStuff(); // .. other gpu work 

// Need to read from pResource so end barrier 
BarrierDesc.Flags = D3D12_RESOURCE_BARRIER_END_ONLY; 

pCommandList->ResourceBarrier( 1, &BarrierDesc );
Read(pResource); // ... read from pResource
```