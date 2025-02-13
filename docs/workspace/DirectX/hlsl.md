---
tags:
  - DirectX12
---


# HLSL

## Registers
[Registers docs](https://learn.microsoft.com/en-us/windows/win32/direct3d12/resource-binding-in-hlsl)

Appearently registers are stored in a register space. Note the word `space` in the register field: `int variable : register(t1, space0)`

From the docs:

```
Texture2D<float4> tex1 : register(t3,  space0)
Texture2D<float4> tex2[4] : register(t10)
Texture2D<float4> tex3[7][5][3] : register(t20, space1)
```
SM5.1 declaration limits are more flexible, and constrained only by the runtime/hardware limits. 
`The space keyword specifies to which logical register space the declared variable is bound.`
If the space keyword is omitted, then the default space index of 0 is implicitly assigned to the range (so the tex2 range above resides in space0). 
`register(t3, space0)` will never conflict with register(t3, space1), nor with any array in another space that might include t3.
