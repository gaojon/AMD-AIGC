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
