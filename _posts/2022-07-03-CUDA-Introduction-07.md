---
title: "[NVIDIA CUDA编程教程]07 CUDA编程(1)"
date: 2022-07-03
categories:
  - Notes
tags:
  - CUDA
classes: wide
---

{% include video id="BV1kx411m7Fk" provider="bilibili" danmaku="1" %}

## 笔记
- CUDA术语:
  - `Host`主机端, 通常指CPU, `Device`设备端, 通常指GPU, 强调区分两者主要是因为目前物理存储仍然不同.
  - `Kernel`指GPU上数据并行处理的函数.
  - `Grid`指一维/二维线程块(block).
  - `Block`指一组线程, 可以为一维/二维/三维. 线程块内同步执行, 可以使用共享内存.
  - 线程层次: `blockIdx`块索引, `threadIdx`线程索引.
  - CUDA内存传输: `cudaMalloc`, `cudaFree`控制申请和释放.

## Example: Naive MatMul
{% highlight c linenos %}
#include <bits/stdc++.h>
using namespace std;

__global__ void matmul(float *A, float *B, float *C, int N) {
    int tx = blockIdx.x * blockDim.x + threadIdx.x;
    int ty = blockIdx.y * blockDim.y + threadIdx.y;
    if(tx < N && ty < N) {
        float c = 0;
        for(int i = 0; i < N; ++i) c += A[N * tx + i] * B[N * i + ty];
        C[N * tx + ty] = c;
    }
}

int main() {
    int N = 1 << 8;
    float *A, *B, *C;
    cudaMallocManaged(&A, N * N * sizeof(float));
    cudaMallocManaged(&B, N * N * sizeof(float));
    cudaMallocManaged(&C, N * N * sizeof(float));
    for(int i = 0; i < N; ++i) {
        for(int j = 0; j < N; ++j) {
            A[N * i + j] = 1.0 * rand() / RAND_MAX;
            B[N * i + j] = 1.0 * rand() / RAND_MAX;
            C[N * i + j] = 0;
        }
    }

    dim3 numBlocks(16, 16);
    dim3 blockSize(16, 16);
    matmul<<<numBlocks, blockSize>>>(A, B, C, N);
    cudaDeviceSynchronize();

    float maxError = 0.0f;
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < N; ++j) {
            float c = 0;
            for(int k = 0; k < N; ++k) c += A[N * i + k] * B[N * k + j];
            maxError = fmax(maxError, fabs(C[N * i + j] - c));
        }
    }
    std::cout << "Max error: " << maxError << std::endl;

    cudaFree(A), cudaFree(B), cudaFree(C);
    return 0;
}
{% endhighlight %}

## 补充材料
- [An Even Easier Introduction to CUDA](https://developer.nvidia.com/blog/even-easier-introduction-cuda/)