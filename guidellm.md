# LLM Inference Benchmark Setup with GuideLLM

I’d like to evaluate a standalone tool for benchmarking large language model (LLM) inference performance across various frameworks. One promising option appears to be GuideLLM, which facilitates such comparisons.

## Reference

[GuideLLM GitHub Repository](https://github.com/vllm-project/guidellm/blob/main/docs/install.md)

## Install GuideLLM

To set up GuideLLM, create a Python virtual environment and install the necessary dependencies as follows:

```bash
sudo apt install python3.12-venv
python3 -m venv ~/llm_bench_env
source ~/llm_bench_env/bin/activate

cd ~/llm_bench_env

echo -e "[global]\nindex-url = https://pypi.tuna.tsinghua.edu.cn/simple/\ntrusted-host = pypi.tuna.tsinghua.edu.cn\n\n[install]\ntrusted-host = pypi.tuna.tsinghua.edu.cn" > pip.conf

pip install --upgrade pip
pip install guidellm
```

## Ollama Tests

### Verify Ollama Functionality

Ensure the Ollama service is operational by listing available models:

```bash
ollama list
NAME            ID              SIZE      MODIFIED
qwen2.5:14b     7cdf5a0187d5    9.0 GB    8 days ago
qwen2.5:7b      845dbda0ea48    4.7 GB    8 days ago
qwen2.5:1.5b    65ec06548149    986 MB    8 days ago
```

If the desired model (e.g., `qwen2.5:1.5b`) is not listed, download it from the server:

```bash
ollama pull qwen2.5:1.5b
```

Verify that the Ollama API is functioning correctly:

```bash
curl http://localhost:11434/v1/models
{"object":"list","data":[{"id":"qwen2.5:14b","object":"model","created":1758880151,"owned_by":"library"},{"id":"qwen2.5:7b","object":"model","created":1758879831,"owned_by":"library"},{"id":"qwen2.5:1.5b","object":"model","created":1758877705,"owned_by":"library"}]}
```

### Prepare the Dataset

Reference: [GuideLLM Example - Practice on vLLM Simulator](https://github.com/vllm-project/guidellm/blob/main/docs/examples/practice_on_vllm_simulator.md)

To ensure accurate comparisons, use the tokenizer specific to each model. Download the tokenizer files for the Qwen2.5-1.5B-Instruct model from ModelScope:

```bash
mkdir Qwen2.5-1.5B-Instruct
cd Qwen2.5-1.5B-Instruct
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/merges.txt
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/tokenizer.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/tokenizer_config.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/vocab.json

ls ./Qwen2.5-1.5B-Instruct
merges.txt  tokenizer.json  tokenizer_config.json  vocab.json
```

## Run GuideLLM Benchmarks

### Sweep Concurrency Levels

Run a benchmark that sweeps through different concurrency levels to evaluate performance:

```bash
guidellm benchmark \
--target "http://localhost:11434/" \
--model "qwen2.5:1.5b" \
--processor "./Qwen2.5-1.5B-Instruct" \
--rate-type sweep \
--max-seconds 10 \
--max-requests 10 \
--data "prompt_tokens=256,output_tokens=128"
```

### Test Specific Concurrency Levels

Run a benchmark with predefined concurrency levels (1, 2, 4, 8) for the Qwen2.5-1.5B model:

```bash
guidellm benchmark \
--target "http://localhost:11434/" \
--model "qwen2.5:1.5b" \
--processor "./Qwen2.5-1.5B-Instruct" \
--rate-type concurrent \
--rate 1,2,4,8 \
--max-seconds 10 \
--max-requests 10 \
--data "prompt_tokens=256,output_tokens=128"
```

### Qwen2.5-7B Model

Download the tokenizer files for the Qwen2.5-7B-Instruct model and run the benchmark:

```bash
mkdir Qwen2.5-7B-Instruct
cd Qwen2.5-7B-Instruct
wget https://modelscope.cn/models/Qwen/Qwen2.5-7B-Instruct/resolve/master/merges.txt
wget https://modelscope.cn/models/Qwen/Qwen2.5-7B-Instruct/resolve/master/tokenizer.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-7B-Instruct/resolve/master/tokenizer_config.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-7B-Instruct/resolve/master/vocab.json

cd ..

guidellm benchmark \
--target "http://localhost:11434/" \
--model "qwen2.5:7b" \
--processor "./Qwen2.5-7B-Instruct" \
--rate-type concurrent \
--rate 1,2,4,8 \
--max-seconds 10 \
--max-requests 5 \
--data "prompt_tokens=256,output_tokens=128"
```

### Qwen2.5-14B Model

Download the tokenizer files for the Qwen2.5-14B-Instruct model and run the benchmark:

```bash
mkdir Qwen2.5-14B-Instruct
cd Qwen2.5-14B-Instruct
wget https://modelscope.cn/models/Qwen/Qwen2.5-14B-Instruct/resolve/master/merges.txt
wget https://modelscope.cn/models/Qwen/Qwen2.5-14B-Instruct/resolve/master/tokenizer.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-14B-Instruct/resolve/master/tokenizer_config.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-14B-Instruct/resolve/master/vocab.json

cd ..

guidellm benchmark \
--target "http://localhost:11434/" \
--model "qwen2.5:14b" \
--processor "./Qwen2.5-14B-Instruct" \
--rate-type concurrent \
--rate 1,2,4,8 \
--max-seconds 10 \
--max-requests 5 \
--data "prompt_tokens=256,output_tokens=128"
```

## Benchmark Results

### Qwen2.5:1.5B

```plaintext
Benchmark Stats:
======================================================================================================================================================
Metadata    | Request Stats         || Out Tok/sec | Tot Tok/sec | Req Latency (sec)  ||| TTFT (ms)           ||| ITL (ms)        ||| TPOT (ms)       ||
Benchmark   | Per Second | Concurrency | mean        | mean        | mean | median | p99  | mean   | median | p99   | mean | median | p99 | mean | median | p99
------------|------------|-------------|-------------|-------------|------|--------|------|--------|--------|-------|------|--------|-----|------|--------|-----
concurrent@1| 0.51       | 1.00        | 52.4        | 197.3       | 1.97 | 2.38   | 2.38 | 262.1  | 257.0  | 272.8 | 16.6 | 16.7   | 16.7| 16.4 | 16.5   | 16.6
concurrent@2| 0.47       | 1.73        | 52.4        | 186.1       | 3.69 | 3.55   | 4.97 | 1778.9 | 1455.9 | 2878.6| 17.1 | 16.6   | 18.8| 17.0 | 16.4   | 18.7
concurrent@4| 0.45       | 2.48        | 55.8        | 183.4       | 5.55 | 4.42   | 8.94 | 3536.3 | 2359.8 | 6886.0| 16.1 | 16.2   | 16.2| 16.0 | 16.0   | 16.1
concurrent@8| 0.44       | 2.42        | 53.8        | 179.3       | 5.48 | 4.28   | 9.08 | 3448.0 | 2175.1 | 6821.5| 16.5 | 16.3   | 17.3| 16.3 | 16.2   | 17.1
======================================================================================================================================================
```

### Qwen2.5:7B

```plaintext
Benchmark Stats:
======================================================================================================================================================
Metadata    | Request Stats         || Out Tok/sec | Tot Tok/sec | Req Latency (sec)  ||| TTFT (ms)           ||| ITL (ms)        ||| TPOT (ms)       ||
Benchmark   | Per Second | Concurrency | mean        | mean        | mean | median | p99  | mean   | median | p99   | mean | median | p99 | mean | median | p99
------------|------------|-------------|-------------|-------------|------|--------|------|--------|--------|-------|------|--------|-----|------|--------|-----
concurrent@1| 0.13       | 1.00        | 15.9        | 51.7        | 7.97 | 7.97   | 7.97 | 968.5  | 968.5  | 968.5 | 55.1 | 55.1   | 55.1| 54.7 | 54.7   | 54.7
concurrent@2| 0.13       | 1.00        | 16.0        | 51.8        | 7.96 | 7.96   | 7.96 | 945.9  | 945.9  | 945.9 | 55.2 | 55.2   | 55.2| 54.8 | 54.8   | 54.8
concurrent@4| 0.13       | 1.00        | 15.9        | 51.7        | 7.97 | 7.97   | 7.97 | 954.0  | 954.0  | 954.0 | 55.2 | 55.2   | 55.2| 54.8 | 54.8   | 54.8
concurrent@8| 0.12       | 1.00        | 15.0        | 48.7        | 8.46 | 8.46   | 8.46 | 1006.5 | 1006.5 | 1006.5| 58.7 | 58.7   | 58.7| 58.2 | 58.2   | 58.2
======================================================================================================================================================
```

### Qwen2.5:14B

```plaintext
Benchmark Stats:
============================================================================================================================================================
Metadata    | Request Stats         || Out Tok/sec | Tot Tok/sec | Req Latency (sec) ||| TTFT (ms)              ||| ITL (ms)          ||| TPOT (ms)         ||
Benchmark   | Per Second | Concurrency | mean        | mean        | mean | median | p99  | mean    | median  | p99    | mean  | median | p99  | mean  | median | p99
------------|------------|-------------|-------------|-------------|------|-------|------|---------|---------|--------|-------|--------|------|-------|--------|------
concurrent@1| 0.06       | 1.00        | 8.3         | 26.8        | 15.40| 15.40 | 15.41| 1686.5  | 1685.4  | 1694.1 | 108.0 | 108.0  | 108.1| 107.1 | 107.1  | 107.2
concurrent@2| 0.06       | 1.67        | 8.3         | 26.9        | 25.66| 30.78 | 30.81| 11911.1 | 17024.9 | 17034.2| 108.2 | 108.2  | 108.5| 107.4 | 107.3  | 107.6
concurrent@4| 0.06       | 1.99        | 8.2         | 26.6        | 30.92| 30.79 | 46.53| 17065.6 | 17055.8 | 32455.1| 109.0 | 108.2  | 110.8| 108.2 | 107.4  | 110.0
concurrent@8| 0.06       | 2.01        | 7.9         | 25.6        | 32.39| 32.52 | 48.36| 17990.1 | 18017.2 | 34189.6| 113.4 | 114.1  | 114.3| 112.5 | 113.3  | 113.4
============================================================================================================================================================
```
