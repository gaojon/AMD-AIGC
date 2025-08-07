# AMD GPU Setup and Benchmarking Guide

## Environment
- **Hardware**: HX370
- **Operating System**: Ubuntu 2024.04 LTS

## Prerequisites

### Install ROCm
Follow the official AMD ROCm installation guide to set up the ROCm platform for GPU computing.

[ROCm Installation Guide](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/quick-start.html)

### Install MIGraphX
Install MIGraphX, AMD's graph inference engine, by following the official documentation.

[MIGraphX Installation Guide](https://rocm.docs.amd.com/projects/AMDMIGraphX/en/latest/index.html)

### Install radeontop
Monitor AMD GPU utilization with `radeontop`.

```bash
sudo apt install radeontop
```

### Install MIGraphX Binary
Install the MIGraphX binary package for inference tasks.

```bash
sudo apt install -y migraphx
```

## Verify Installation
Check if MIGraphX is installed correctly by listing available operations.

```bash
/opt/rocm/bin/migraphx-driver op --list
........
softmax
split_fused_reduce
sqdiff
sqrt
squeeze
step
sub
tan
tanh
topk
transpose
undefined
unique
unknown:
unpack_int4
unsqueeze
where
[ MIGraphX Version: 2.12.0.db302aeb ] Complete: /opt/rocm/bin/migraphx-driver op --list

```

## Run Performance Benchmark
Execute a performance benchmark using an ONNX model with MIGraphX.

### Command
```bash
/opt/rocm/bin/migraphx-driver perf resnet50_v1p5_64_3_224_224.onnx --batch 64 --fp16
................................
Batch size: 64
Rate: 893.571 inferences/sec
Total time: 71.6228ms (Min: 69.616ms, Max: 72.7298ms, Mean: 71.4244ms, Median: 72.2723ms)
Percentiles (90%, 95%, 99%): (72.6301ms, 72.6594ms, 72.7098ms)
Total instructions time: 73.2245ms
Overhead time: 0.0267628ms, -1.60172ms
Overhead: 0%, -2%
[ MIGraphX Version: 2.12.0.db302aeb ] Complete: /opt/rocm/bin/migraphx-driver perf resnet50_v1p5_64_3_224_224.onnx --batch 64 --fp16

```

You could check the rate to see the performance. 
