# Installing Ollama on HX370 with Ubuntu 2024.04 LTS

## Environment
- **Hardware**: HX370
- **Operating System**: Ubuntu 2024.04 LTS

## Prerequisites

### Install ROCm
Follow the official AMD ROCm installation guide to set up ROCm for GPU support:
[ROCm Installation Guide](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/quick-start.html)

### Additional Reference
For detailed instructions on running LLMs locally on AMD GPUs with Ollama, refer to:
[Running LLMs Locally on AMD GPUs with Ollama](https://www.amd.com/en/developer/resources/technical-articles/running-llms-locally-on-amd-gpus-with-ollama.html)

### Install curl
Ensure `curl` is installed on your system. If not, run the following command:
```bash
sudo apt install curl
```

## Install Ollama
Run the following single command to install Ollama:
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### Installation Output
The installation process will:

```
>>> Installing ollama to /usr/local
>>> Downloading Linux amd64 bundle
######################################################################## 100.0%
>>> Creating ollama user...
>>> Adding ollama user to render group...
>>> Adding ollama user to video group...
>>> Adding current user to ollama group...
>>> Creating ollama systemd service...
>>> Enabling and starting ollama service...
Created symlink /etc/systemd/system/default.target.wants/ollama.service ? /etc/systemd/system/ollama.service.
>>> Downloading Linux ROCm amd64 bundle
######################################################################## 100.0%
>>> The Ollama API is now available at 127.0.0.1:11434.
>>> Install complete. Run "ollama" from the command line.
>>> AMD GPU ready.
>>> Install complete. Run "ollama" from the command line.
>>> AMD GPU ready.
```



## Change AMD GFX setting and Ollama model location
The default models path is :
/usr/share/ollama/.ollama/models

```bash
sudo systemctl stop ollama.service
sudo vi /etc/systemd/system/ollama.service
```

It looks like following:
The env OLLAMA_MODELS could point to anywhere you want to store your models. 
enable the internet access from anywhere to port 11434

```
[Service]
Environment="HSA_OVERRIDE_GFX_VERSION=11.0.0"
Environment="OLLAMA_MODELS=/home1/jon/ollama/.ollama"
Environment="OLLAMA_HOST=0.0.0.0"
```

Reload service configuration and check the log to make sure it started successfully
```bash
systemctl daemon-reload

sudo journalctl --unit=ollama.service --vacuum-time=1s
sudo systemctl start ollama.service
sudo journalctl -u ollama.service --no-pager --follow --pager-end

```

Notes: Ollama will start service with ollama user, so make sure the ollama user has the full access permission of models directory. 

## Post-Installation Commands
1. List available models:
```bash
ollama list
```

2. Pull the TinyLLaMA model:
```bash
ollama pull tinyllama
```

3. Run the TinyLLaMA model:
```bash
ollama run tinyllama
```

## Make sure it's running with GPU not CPU

Make sure your GPU is busy and vram utilization rate increased
```
$radeontop

           Graphics pipe  97.50% │
─────────────────────────────────────────────────┼───────────────────────────────────────────────
                            Event Engine   0.00% │
                                                 │
             Vertex Grouper + Tesselator   0.00% │
                                                 │
                       Texture Addresser   0.00% │
                           Texture Cache   0.00% │
                                                 │
                           Shader Export   0.00% │
             Sequencer Instruction Cache   0.00% │
                     Shader Interpolator  81.67% │
                  Shader Memory Exchange   0.00% │
                                                 │
                          Scan Converter   0.00% │
                      Primitive Assembly   0.00% │
                                                 │
                             Depth Block   0.00% │
                             Color Block   0.00% │
                          Clip Rectangle  97.50% │
                                                 │
                     1278M / 24409M VRAM   5.23% │
                         28M / 3752M GTT   0.74% │
              0.94G / 0.94G Memory Clock  99.81% │
              2.59G / 2.90G Shader Clock  89.24% │
```



## Debug commands

reference:

https://github.com/ollama/ollama/blob/main/docs/gpu.md

https://github.com/ollama/ollama/blob/main/docs/troubleshooting.md

Debug commands
```bash
sudo systemctl restart ollama
sudo systemctl daemon-reload  
sudo systemctl stop ollama
sudo systemctl start ollama
sudo systemctl status ollama
ollama serve
export HSA_OVERRIDE_GFX_VERSION=11.0.0
sudo journalctl --unit=ollama --vacuum-time=1s
sudo journalctl -u ollama --no-pager --follow --pager-end
sudo vi /etc/systemd/system/ollama.service
```



## Issues:
```
pulling manifest
Error: max retries exceeded: Get "https://dd20bb891979d25aebc8bec07b2b3bbc.r2.cloudflarestorage.com/ollama/docker/registry/v2/blobs/sha256/2a/2af3b81862c6be03c769683af18efdadb2c33f60ff32ab6f83e42c043d6c7816/data?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=66040c77ac1b787c3af820529859349a%2F20250814%2Fauto%2Fs3%2Faws4_request&X-Amz-Date=20250814T020831Z&X-Amz-Expires=86400&X-Amz-SignedHeaders=host&X-Amz-Signature=cad15a19c608bb6b0f22c7c0fc210459cfaf30d6f243ca2cbb4a10ea3985f9f8": tls: failed to verify certificate: x509: certificate signed by unknown authority
```
Solution is to use direct connection to internet instead of company firewall which will terminate the secure https connection.



