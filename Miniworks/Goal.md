
ECS: https://github.com/NateTheGreatt/bitECS?tab=readme-ov-file

## Phase 2.5: First glTF Integration
**Duration**: 3-5 days
**Goal**: Load simple external models (validation & motivation)

### Tasks
1. **Minimal glTF Loader**
   - Parse JSON structure
   - Load binary buffers
   - Extract accessors for:
     - Positions
     - Normals
     - UVs
     - Indices

2. **Load Simple Models**
   - Start with **Box.gltf** (single mesh, single texture)
   - Then **BoxTextured.gltf**
   - Then **Duck.gltf** (more complex geometry)

3. **Hybrid Rendering**
   ```typescript
   const SCENE = [
     ...PROCEDURAL_ENTITIES,  // Your cubes for quick iteration
     ...GLTF_ENTITIES,        // Loaded models for validation
   ];
   ```

### Validation
- ✅ Box.gltf renders with correct proportions
- ✅ Textures load and display correctly
- ✅ Can toggle between procedural and glTF models

### Why Now?
- You have everything needed for basic models
- Early validation prevents architectural issues
- Keeps you motivated with real assets
- Still keep procedural geometry for fast shader iteration

---

## Phase 3: PBR Materials
**Duration**: 2-3 weeks
**Goal**: Physically-based rendering with metallic-roughness workflow

### Tasks
1. **Extended Material Format**
   ```typescript
   interface PBRMaterial {
     baseColorTexture: GPUTexture;
     metallicRoughnessTexture: GPUTexture;  // R=unused, G=roughness, B=metallic
     normalTexture: GPUTexture;
     occlusionTexture?: GPUTexture;
     emissiveTexture?: GPUTexture;

     baseColorFactor: [number, number, number, number];
     metallicFactor: number;
     roughnessFactor: number;
     emissiveFactor: [number, number, number];
   }
   ```

2. **Tangent Space Normals**
   - Add tangent + bitangent to vertex format
   - Calculate TBN matrix in vertex shader
   - Sample normal map and transform to world space

3. **Cook-Torrance BRDF**
   Implement in fragment shader:
   - **Diffuse**: Lambert or Disney diffuse
   - **Normal Distribution**: GGX (Trowbridge-Reitz)
   - **Geometry**: Smith's Schlick-GGX
   - **Fresnel**: Schlick approximation
   - Combine based on metallic value

   ```wgsl
   fn distributionGGX(NdotH: f32, roughness: f32) -> f32 {
       let a = roughness * roughness;
       let a2 = a * a;
       let denom = NdotH * NdotH * (a2 - 1.0) + 1.0;
       return a2 / (PI * denom * denom);
   }

   fn geometrySchlickGGX(NdotV: f32, roughness: f32) -> f32 {
       let r = roughness + 1.0;
       let k = (r * r) / 8.0;
       return NdotV / (NdotV * (1.0 - k) + k);
   }

   fn geometrySmith(NdotV: f32, NdotL: f32, roughness: f32) -> f32 {
       let ggx1 = geometrySchlickGGX(NdotV, roughness);
       let ggx2 = geometrySchlickGGX(NdotL, roughness);
       return ggx1 * ggx2;
   }

   fn fresnelSchlick(cosTheta: f32, F0: vec3<f32>) -> vec3<f32> {
       return F0 + (1.0 - F0) * pow(1.0 - cosTheta, 5.0);
   }
   ```

4. **Tone Mapping**
   - Add simple Reinhard or ACES tone mapping
   - Gamma correction (sRGB)

### Validation with glTF
- ✅ Load **DamagedHelmet.gltf** - the PBR reference model
- ✅ Metallic surfaces reflect like metal
- ✅ Rough surfaces diffuse light correctly
- ✅ Compare with reference renders

---

## Phase 4: Advanced Lighting
**Duration**: 2-3 weeks
**Goal**: Multiple lights and environment lighting

### Tasks
1. **Multiple Light Types**
   ```typescript
   interface Light {
     type: 'directional' | 'point' | 'spot';
     position: vec3;
     direction: vec3;
     color: vec3;
     intensity: number;
     range?: number;        // for point/spot
     innerConeAngle?: number;  // for spot
     outerConeAngle?: number;  // for spot
   }
   ```
   - Store lights in storage buffer array
   - Loop through lights in fragment shader
   - Handle attenuation for point/spot lights

2. **Image-Based Lighting (IBL)**
   - Load environment map (cubemap)
   - Precompute offline:
     - Diffuse irradiance map
     - Specular prefiltered environment map (multiple roughness levels)
     - BRDF integration lookup table (2D texture)
   - Sample in fragment shader:
     ```wgsl
     let irradiance = textureSample(irradianceMap, sampler, N);
     let prefilteredColor = textureSampleLevel(prefilterMap, sampler, R, roughness * maxLOD);
     let brdf = textureSample(brdfLUT, sampler, vec2(NdotV, roughness));
     ```

3. **Shadow Mapping** (Optional but recommended)
   - Render depth from light's perspective
   - Compare fragment depth against shadow map
   - PCF for soft shadows

### Validation with glTF
- ✅ Load **WaterBottle.gltf** - tests various material properties
- ✅ Load **MetalRoughSpheres.gltf** - tests full parameter range
- ✅ IBL provides realistic ambient lighting

---

## Phase 5: Complete glTF 2 Support
**Duration**: 2-3 weeks
**Goal**: Full-featured asset loading

### Tasks
1. **Complete Material Loading**
   - Parse all glTF material properties
   - Handle missing textures (use default white/normal/etc)
   - Support texture transforms (offset, scale, rotation)
   - Handle different texture coordinate sets

2. **Scene Graph & Transforms**
   - Implement node hierarchy
   - Handle local-to-world transforms
   - Support multiple meshes per node
   - Handle mesh primitives with different materials

3. **Advanced Features**
   - Multiple scenes
   - Cameras (use glTF camera definitions)
   - Morph targets (blend shapes)
   - Skins (skeletal animation setup)

4. **Robust Loading**
   - Handle embedded base64 buffers
   - Support external .bin files
   - Load images (PNG, JPEG)
   - Generate mipmaps
   - Handle different buffer component types (byte, short, float, etc)
   - Proper error handling and validation

5. **Texture Optimization**
   - Automatic mipmap generation
   - Texture compression support (BC7/ASTC)
   - Lazy loading for large scenes

### Test with Complex Scenes
- ✅ **Sponza.gltf** - architectural scene, many materials
- ✅ **BoomBox.gltf** - complex PBR showcase
- ✅ **FlightHelmet.gltf** - extensive material variety
- ✅ **SciFiHelmet.gltf** - tests emissive materials

---

## Phase 6: Polish & Optimization (Optional)
**Duration**: Ongoing
**Goal**: Production-ready renderer

### Tasks
1. **Animation Support**
   - Parse glTF animations
   - Interpolate keyframes
   - Apply to transforms
   - Skinning/skeletal animation

2. **Post-Processing**
   - Bloom
   - Screen-space ambient occlusion (SSAO)
   - Temporal anti-aliasing (TAA)

3. **Performance**
   - Frustum culling
   - LOD system
   - Instanced rendering for repeated objects
   - Render bundle optimization

4. **Extensions**
   - KHR_lights_punctual
   - KHR_materials_clearcoat
   - KHR_materials_transmission
   - KHR_materials_volume

---

## Development Best Practices

### Always Keep a Debug Scene
```typescript
const DEBUG_SCENE = {
  proceduralCube: true,    // Fast iteration
  simpleGltf: true,        // Basic validation (Box.gltf)
  complexGltf: false,      // Full test (DamagedHelmet.gltf)

  showNormals: false,
  showTangents: false,
  showUVs: false,
};
```

### Incremental Testing Pattern
1. Make shader change on procedural cube (instant feedback)
2. Validate on Box.gltf (ensures glTF compatibility)
3. Test on DamagedHelmet.gltf (confirms PBR correctness)

### Key Resources
- [LearnOpenGL PBR Theory](https://learnopengl.com/PBR/Theory)
- [glTF 2.0 Specification](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html)
- [Khronos Sample Models](https://github.com/KhronosGroup/glTF-Sample-Models)
- [Filament PBR Guide](https://google.github.io/filament/Filament.html)

---

## Timeline Summary

| Phase | Duration | Milestone |
|-------|----------|-----------|
| 1: Lighting | 1-2 weeks | Surfaces respond to light |
| 2: Textures | 1-2 weeks | Textured cubes |
| 2.5: Basic glTF | 3-5 days | Box.gltf renders |
| 3: PBR | 2-3 weeks | DamagedHelmet.gltf looks correct |
| 4: Advanced Lighting | 2-3 weeks | IBL + multiple lights |
| 5: Full glTF | 2-3 weeks | Complex scenes load |
| **Total** | **8-12 weeks** | **Production renderer** |

Start with Phase 1 and build incrementally. Each phase should produce visible, testable results!
