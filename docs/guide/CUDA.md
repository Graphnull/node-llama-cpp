# Enabling CUDA support
## Prerequisites
* [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) 12.0 or higher
* [`cmake-js` dependencies](https://github.com/cmake-js/cmake-js#:~:text=projectRoot/build%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5Bstring%5D-,Requirements%3A,-CMake)
* [CMake](https://cmake.org/download/) 3.26 or higher (optional, recommended if you have build issues)

## Building `node-llama-cpp` with CUDA support
Run this command inside of your project:
```bash
npx --no node-llama-cpp download --cuda
```

> If `cmake` is not installed on your machine, `node-llama-cpp` will automatically download `cmake` to an internal directory and try to use it to build `llama.cpp` from source.

> If you see the message `cuBLAS not found` during the build process,
> it means that CUDA Toolkit is not installed on your machine or that it is not detected by the build process.

### Custom `llama.cpp` cmake options
`llama.cpp` has some options you can use to customize your CUDA build, you can find these [here](https://github.com/ggerganov/llama.cpp/tree/master#cublas).

To build `node-llama-cpp` with any of these options, set an environment variable of an option prefixed with `NODE_LLAMA_CPP_CMAKE_OPTION_`.

### Fix the `Failed to detect a default CUDA architecture` build error
To fix this issue you have to set the `CUDACXX` environment variable to the path of the `nvcc` compiler.

For example, if you installed CUDA Toolkit 12.2 on Windows, you have to run the following command:
```bash
set CUDACXX=C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.2\bin\nvcc.exe
```

On Linux, it would be something like this:
```bash
export CUDACXX=/usr/local/cuda-12.2/bin/nvcc
```

Then run the build command again to check whether setting the `CUDACXX` environment variable fixed the issue.

## Using `node-llama-cpp` with CUDA
After you build `node-llama-cpp` with CUDA support, you can use it normally.

To configure how much layers of the model are run on the GPU, configure `gpuLayers` on `LlamaModel` in your code:
```typescript
const model = new LlamaModel({
    modelPath,
    gpuLayers: 64 // or any other number of layers you want
});
```

You'll see logs like these in the console when the model loads:
```
llm_load_tensors: ggml ctx size =    0.09 MB
llm_load_tensors: using CUDA for GPU acceleration
llm_load_tensors: mem required  =   41.11 MB (+ 2048.00 MB per state)
llm_load_tensors: offloading 32 repeating layers to GPU
llm_load_tensors: offloading non-repeating layers to GPU
llm_load_tensors: offloading v cache to GPU
llm_load_tensors: offloading k cache to GPU
llm_load_tensors: offloaded 35/35 layers to GPU
llm_load_tensors: VRAM used: 4741 MB
```

On Linux, you can monitor GPU usage with this command:
```bash
watch -d nvidia-smi
```
