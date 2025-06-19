# DX12 RayTracing Notes

1. Create raytracing interfaces

- DXR has its own devices (`ID3D12Device5`) and graphics command lists (`ID3D12GraphicsCommandList4`). 

2. Create root signatures

- This is created in similar ways as in regular DX12 apps, which makes sense to me. 
- Two kinds of root signatures here:
  - global: "This is a root signature that is shared across all raytracing shaders invoked during a DispatchRays() call." 
    - what's `DispatchRays`?  It is the entry point for executing a raytracing pipeline, similar to how Draw or Dispatch are used for rasterization and compute shaders, respectively.
  - local: "This is a root signature that enables a shader to have unique arguments that come from shader tables."
    - what's `shader tables`? A shader table is a GPU buffer that describes which shaders to invoke for each raytracing stage (ray generation, miss, hit group, etc.) and provides the local root arguments (data) for each shader.

