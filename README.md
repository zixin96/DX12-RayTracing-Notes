# DX12 RayTracing Study Notes

1. Create raytracing interfaces

- DXR has its own devices (`ID3D12Device5`) and graphics command lists (`ID3D12GraphicsCommandList4`). 

2. Create root signatures

- This is created in similar ways as in regular DX12 apps, which makes sense to me. 
- Two kinds of root signatures here:
  - global: "This is a root signature that is shared across all raytracing shaders invoked during a DispatchRays() call." 
    - what's `DispatchRays`?  It is the entry point for executing a raytracing pipeline, similar to how Draw or Dispatch are used for rasterization and compute shaders, respectively.
  - local: "This is a root signature that enables a shader to have unique arguments that come from shader tables."
    - what's `shader tables`? A shader table is a GPU buffer that describes which shaders to invoke for each raytracing stage (ray generation, miss, hit group, etc.) and provides the local root arguments (data) for each shader.
    - also need to specify a raytracing-specific root signature flag: `D3D12_ROOT_SIGNATURE_FLAG_LOCAL_ROOT_SIGNATURE`. This is new. 

3. Create raytracing pipeline state object (RTPSO)

- `CD3DX12_STATE_OBJECT_DESC raytracingPipeline{ D3D12_STATE_OBJECT_TYPE_RAYTRACING_PIPELINE };` is new. 
- You combine multiple "subobjects" into your RTPSO, via calls to `raytracingPipeline.CreateSubobject`
  - `CD3DX12_DXIL_LIBRARY_SUBOBJECT` is your shaders (e.g. `g_pRaytracing`) and entry points (e.g `c_raygenShaderName`). 
  - `CD3DX12_HIT_GROUP_SUBOBJECT` A hit group specifies closest hit, any hit and intersection shaders to be executed when a ray intersects the geometry's triangle/AABB.
  - `CD3DX12_RAYTRACING_SHADER_CONFIG_SUBOBJECT` Defines the maximum sizes in bytes for the ray payload and attribute structure.
  - ...

```
--------------------------------------------------------------------
| D3D12 State Object 0x00000039C40FF070: Raytracing Pipeline
| [0]: DXIL Library 0x00007FF7B8615FA0, 17876 bytes
|  [0]: MyRaygenShader
|  [1]: MyClosestHitShader
|  [2]: MyMissShader
|--------------------------------------------------------------------
| [1]: Hit Group (MyHitGroup)
|  [0]: Any Hit Import: [none]
|  [1]: Closest Hit Import: MyClosestHitShader
|  [2]: Intersection Import: [none]
|--------------------------------------------------------------------
| [2]: Raytracing Shader Config
|  [0]: Max Payload Size: 16 bytes
|  [1]: Max Attribute Size: 8 bytes
|--------------------------------------------------------------------
| [3]: Local Root Signature 0x000001F4F28E6CA0
|--------------------------------------------------------------------
| [4]: Subobject to Exports Association (Subobject [3])
|  [0]: MyRaygenShader
|--------------------------------------------------------------------
| [5]: Global Root Signature 0x000001F4F28E5B60
|--------------------------------------------------------------------
| [6]: Raytracing Pipeline Config
|  [0]: Max Recursion Depth: 1
|--------------------------------------------------------------------
```



