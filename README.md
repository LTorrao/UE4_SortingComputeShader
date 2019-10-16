**Parallel Bitonic Sorting on the GPU by a Compute Shader**

__Installation__

Tested on UE4.21 (in master branch). Should also work for newer versions of the engine as well. For installation, copy both folder to your Engine/Plugins/Runtime folder.

__Usage__

This is a plugin for the Unreal Engine that realises parallel sorting on the GPU via compute shaders. It sorts three-dimensional vectors according to their distance to the current camera position. Currently only textures with maximum size of 1024\*1024 are supported, that is, 1 Million points can be sorted in total at maximum.

The texture/problem size has to be set manually in "ComputeShaderUsageExample.h" and "BitonicSortingKernelComputeShader.usf":

```CPP
#define BITONIC_BLOCK_SIZE 1024
```
```CPP
const UINT NUM_ELEMENTS = 1024 * 1024;
const UINT BITONIC_BLOCK_SIZE = 1024;
```

To sort a dataset with the compute shader, the compute shader has to be used as follows:

```CPP
mComputeShader = new FComputeShader(1.0f, GetUpperPowerOfTwo(pointsPerAxis), GetUpperPowerOfTwo(pointsPerAxis), currentWorld->Scene->GetFeatureLevel());

// Send unsorted point position data and point color data to compute shader
mComputeShader->SetPointPosDataReference(mPointPosDataPointer);
mComputeShader->SetPointColorDataReference(mColorDataPointer);

// Execute parallel sorting compute shader
mComputeShader->ExecuteComputeShader(FVector4(currentCamPos));
```

Furthermore, the created textures have to be converted to usable textures via a pixel shader:
```CPP
mPixelShader = new FPixelShader(FColor::Green, currentWorld->Scene->GetFeatureLevel());

// Render sorted point positions to render target for the material shader
mPixelShader->ExecutePixelShader(mPointPosRT, mComputeShader->GetSortedPointPosTexture(), FColor::Red, 1.0f);
mSortedPointPosTex = Cast<UTexture>(mPointPosRT);

// Render sorted point colors to render target for the material shader
mPixelShader->ExecutePixelShader(mPointColorRT, mComputeShader->GetSortedPointColorsTexture(), FColor::Red, 1.0f);
mSortedPointColorTex = Cast<UTexture>(mPointColorRT);
```

If you want to sort the point positions only (without the point colors accordingly), use the "SortingPositionsOnly" branch (speeds up the computation significantly).

To see the plugin in action, see my point cloud renderer plugin for UE4:
https://github.com/ValentinKraft/UE4_GPUPointCloudRenderer/tree/WithComputeShaderSort

To an in-deep tutorial that explains the details of the implementation, see my tutorial here: http://www.valentinkraft.de/compute-shader-in-unreal-tutorial/

__License__

The plugin is free for personal and academic use. Commercial use has to be negotiated. For more information, send me an E-Mail or read the LICENSE.md.

The plugin was created in the context of a master thesis at the [CGVR institute of the University of Bremen](http://cgvr.cs.uni-bremen.de/).

__Demo__

[![Demo](https://img.youtube.com/vi/jVZaz-0Y4Ek/0.jpg)](https://www.youtube.com/watch?v=jVZaz-0Y4Ek)
