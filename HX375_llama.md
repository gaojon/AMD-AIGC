# Installing AMD GPU Drivers, ROCm, and LLaMA.cpp on Ubuntu

This guide outlines the process to install AMD GPU drivers, ROCm (Radeon Open Compute), and LLaMA.cpp with Vulkan support on Ubuntu, including verification and benchmarking steps. Pleaes refer to the official ROCm installation guide for more details
https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/quick-start.html

## Pre-requisite
HW: AI300 HX375 or 8845H
SW: Ubuntu 24.02.2 LTS


## Step 1: Install AMD GPU Drivers and ROCm

1. **Download the AMDGPU installer**:
   ```bash
   wget https://repo.radeon.com/amdgpu-install/6.4.2/ubuntu/noble/amdgpu-install_6.4.60402-1_all.deb
   ```

2. **Install the AMDGPU installer**:
   ```bash
   sudo apt install ./amdgpu-install_6.4.60402-1_all.deb
   ```

3. **Update the package list**:
   ```bash
   sudo apt update
   ```

4. **Install Python dependencies**:
   ```bash
   sudo apt install python3-setuptools python3-wheel
   ```

5. **Add the current user to the render and video groups**:
   ```bash
   sudo usermod -a -G render,video $LOGNAME
   ```

6. **Install ROCm**:
   ```bash
   sudo apt install rocm
   ```

7. **Install GPU drivers**:
   ```bash
   sudo apt update
   sudo apt install "linux-headers-$(uname -r)" "linux-modules-extra-$(uname -r)"
   sudo apt install amdgpu-dkms
   ```

8. **Verify the installation**:
   Run the following command to check the GPU status:
   ```bash
   rocm-smi
   ```
   Expected output:
   ```
   ======================================== ROCm System Management Interface ========================================
   ================================================== Concise Info ==================================================
   Device  Node  IDs              Temp    Power     Partitions          SCLK  MCLK  Fan  Perf  PwrCap  VRAM%  GPU%
                 (DID,     GUID)  (Edge)  (Socket)  (Mem, Compute, ID)
   ==================================================================================================================
   0       1     0x150e,   58242  32.0°C  3.086W    N/A, N/A, 0         N/A   N/A   0%   auto  N/A     7%     0%
   ==================================================================================================================
   ```

## Step 2: Download and Run LLaMA.cpp

1. **Download LLaMA.cpp**:
   ```bash
   wget -c https://github.com/ggml-org/llama.cpp/releases/download/b5904/llama-b5904-bin-ubuntu-vulkan-x64.zip
   ```

2. **Extract the downloaded file**:
   ```bash
   unzip llama-b5904-bin-ubuntu-vulkan-x64.zip
   cd build
   ```

3. **Download the Qwen3 model**:
   ```bash
   wget -c https://huggingface.co/Qwen/Qwen3-0.6B-GGUF/resolve/main/Qwen3-0.6B-Q8_0.gguf
   ```

4. **Run LLaMA.cpp in interactive mode**:
   ```bash
   ./bin/llama-cli -m ./Qwen3-0.6B-Q8_0.gguf
   ```
   Expected output:
   ```
   == Running in interactive mode. ==
    - Press Ctrl+C to interject at any time.
    - Press Return to return control to the AI.
    - To return control without starting a new line, end your input with '/'.
    - If you want to submit another line, end your input with '\'.
    - Not using system message. To change it, set a different value via -sys PROMPT
   >
   ```
   You could input some questions and see the response from terminal

## Step 3: Benchmark LLaMA.cpp

1. **Run the benchmark**:
   ```bash
   ./bin/llama-bench -m ./Qwen3-0.6B-Q8_0.gguf -pg 1000,1000 -ngl 99
   ```
   Expected output:
   ```
   load_backend: loaded RPC backend from /home/jon/llama/bin/libggml-rpc.so
   ggml_vulkan: Found 1 Vulkan devices:
   ggml_vulkan: 0 = AMD Radeon Graphics (RADV GFX1150) (radv) | uma: 1 | fp16: 1 | warp size: 64 | shared memory: 65536 | int dot: 1 | matrix cores: KHR_coopmat
   load_backend: loaded Vulkan backend from /home/jon/llama/bin/libggml-vulkan.so
   load_backend: loaded CPU backend from /home/jon/llama/bin/libggml-cpu-icelake.so
   | model                          |       size |     params | backend    | ngl |            test |                  t/s |
   | ------------------------------ | ---------: | ---------: | ---------- | --: | --------------: | -------------------: |
   | qwen3 0.6B Q8_0                | 604.15 MiB |   596.05 M | RPC,Vulkan |  99 |           pp512 |       3041.19 ± 9.11 |
   | qwen3 0.6B Q8_0                | 604.15 MiB |   596.05 M | RPC,Vulkan |  99 |           tg128 |        117.09 ± 0.02 |
   | qwen3 0.6B Q8_0                | 604.15 MiB |   596.05 M | RPC,Vulkan |  99 |   pp1000+tg1000 |        145.51 ± 0.32 |

   build: 79e0b68c (5904)
   ```

## Step 4: Monitor GPU Utilization

1. **During the benchmark is running, check GPU utilization in another terminal**:
   ```bash
   rocm-smi
   ```
   Expected output and you may notice the GPU utilization rate is 93%.
   ```
   ========================================= ROCm System Management Interface =========================================
   =================================================== Concise Info ===================================================
   Device  Node  IDs              Temp    Power     Partitions          SCLK  MCLK    Fan  Perf  PwrCap  VRAM%  GPU%
                 (DID,     GUID)  (Edge)  (Socket)  (Mem, Compute, ID)
   ====================================================================================================================
   0       1     0x150e,   58242  52.0°C  48.029W   N/A, N/A, 0         N/A   937Mhz  0%   auto  N/A     35%    93%
   ====================================================================================================================
   =============================================== End of ROCm SMI Log ================================================
   ```
