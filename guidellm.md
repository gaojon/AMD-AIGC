
Would like to see a standalone tool to do the LLM inference benchmark crossing different frameworks. 
It looks like the guidellm is one of the options.

## Reference

https://github.com/vllm-project/guidellm/blob/main/docs/install.md

## Install Guidellm
```
sudo apt install python3.12-venv
python3 -m venv ~/llm_bench_env
source ~/llm_bench_env/bin/activate

cd ~/llm_bench_env

echo -e "[global]\nindex-url = https://pypi.tuna.tsinghua.edu.cn/simple/\ntrusted-host = pypi.tuna.tsinghua.edu.cn\n\n[install]\ntrusted-host = pypi.tuna.tsinghua.edu.cn" > pip.conf

pip install --upgrade pip
pip install guidellm
```
## ollama tests

### make sure ollama is working 

```
ollama list
NAME            ID              SIZE      MODIFIED
qwen2.5:14b     7cdf5a0187d5    9.0 GB    8 days ago
qwen2.5:7b      845dbda0ea48    4.7 GB    8 days ago
qwen2.5:1.5b    65ec06548149    986 MB    8 days ago

```
If the target model is not in the list, pull it from server
```
ollama pull qwen2.5:1.5b
```

Make sure the ollama API is working
```
curl http://localhost:11434/v1/models
{"object":"list","data":[{"id":"qwen2.5:14b","object":"model","created":1758880151,"owned_by":"library"},{"id":"qwen2.5:7b","object":"model","created":1758879831,"owned_by":"library"},{"id":"qwen2.5:1.5b","object":"model","created":1758877705,"owned_by":"library"}]}
```

### Get dataset 

Reference:
https://github.com/vllm-project/guidellm/blob/main/docs/examples/practice_on_vllm_simulator.md

In oder do apple to apply conparation, it's best to use tokenizer go together withs each specific model. 
Here is the way to download the tokenizer from huggingface

```
mkdir Qwen2.5-1.5B-Instruct
cd Qwen2.5-1.5B-Instruct
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/merges.txt
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/tokenizer.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/tokenizer_config.json
wget https://modelscope.cn/models/Qwen/Qwen2.5-1.5B-Instruct/resolve/master/vocab.json


ls ./Qwen2.5-1.5B-Instruct
merges.txt              tokenizer.json          tokenizer_config.json   vocab.json
```


### Run Guidellm

```
guidellm benchmark \
--target "http://localhost:11434/" \
--model "qwen2.5:1.5b" \
--processor "./Qwen2.5-1.5B-Instruct" \
--rate-type sweep \
--max-seconds 10 \
--max-requests 10 \
--data "prompt_tokens=256,output_tokens=128"

╭─ Benchmarks ──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ [15:58:25] ⠸ 100% synchronous   (complete)   Req:    0.5 req/s,    2.07s Lat,     1.0 Conc,       4 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   50.8 gen/s,  188.6 tot/s, 308.2ms TTFT,   16.8ms ITL,   285 Prompt,      106 Gen                                  │
│ [15:58:40] ⠸ 100% throughput    (complete)   Req:    0.5 req/s,    4.55s Lat,     2.2 Conc,       4 Comp,        6 Inc,        0 Err                                  │
│                                              Tok:   50.8 gen/s,  190.6 tot/s, 2780.3ms TTFT,   17.1ms ITL,   286 Prompt,      104 Gen                                 │
│ [15:58:56] ⠸ 100% constant@0.48 (complete)   Req:    0.4 req/s,    2.76s Lat,     1.2 Conc,       3 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   53.3 gen/s,  172.3 tot/s, 614.0ms TTFT,   16.9ms ITL,   285 Prompt,      128 Gen                                  │
│ [15:59:56] ⠸ 100% constant@0.48 (complete)   Req:    0.5 req/s,    2.29s Lat,     1.0 Conc,       3 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   50.7 gen/s,  180.6 tot/s, 416.7ms TTFT,   16.8ms ITL,   285 Prompt,      112 Gen                                  │
│ [16:00:55] ⠸ 100% constant@0.49 (complete)   Req:    0.4 req/s,    2.71s Lat,     1.1 Conc,       3 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   53.9 gen/s,  174.7 tot/s, 580.7ms TTFT,   16.7ms ITL,   286 Prompt,      128 Gen                                  │
│ [16:01:55] ⠸ 100% constant@0.49 (complete)   Req:    0.4 req/s,    2.72s Lat,     1.1 Conc,       3 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   53.8 gen/s,  173.8 tot/s, 591.0ms TTFT,   16.7ms ITL,   285 Prompt,      128 Gen                                  │
│ [16:02:55] ⠸ 100% constant@0.49 (complete)   Req:    0.6 req/s,    2.09s Lat,     1.2 Conc,       3 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   51.6 gen/s,  216.3 tot/s, 600.7ms TTFT,   16.7ms ITL,   285 Prompt,       90 Gen                                  │
│ [16:03:54] ⠸ 100% constant@0.49 (complete)   Req:    0.5 req/s,    2.30s Lat,     1.2 Conc,       3 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   52.7 gen/s,  198.8 tot/s, 597.2ms TTFT,   16.6ms ITL,   285 Prompt,      103 Gen                                  │
│ [16:04:54] ⠸ 100% constant@0.49 (complete)   Req:    0.5 req/s,    2.49s Lat,     1.3 Conc,       4 Comp,        0 Inc,        0 Err                                  │
│                                              Tok:   52.7 gen/s,  198.8 tot/s, 765.8ms TTFT,   16.7ms ITL,   286 Prompt,      103 Gen                                  │
│ [16:05:53] ⠸ 100% constant@0.49 (complete)   Req:    0.4 req/s,    2.46s Lat,     1.1 Conc,       3 Comp,        1 Inc,        0 Err                                  │
│                                              Tok:   53.6 gen/s,  181.2 tot/s, 465.4ms TTFT,   16.6ms ITL,   286 Prompt,      120 Gen                                  │
╰───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
Generating... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ (10/10) [ 0:07:37 < 0:00:00 ]

```



Qwen 7B model

```
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
--rate-type sweep \
--max-seconds 10 \
--max-requests 10 \
--data "prompt_tokens=256,output_tokens=128"
```

Test with concurrency from 1 2 4 8
```
guidellm benchmark \
--target "http://localhost:11434/" \
--model "qwen2.5:7b" \
--processor "./Qwen2.5-7B-Instruct" \
--rate-type concurrent \
--rate 1,2,4,8 \
--max-seconds 10 \
--max-requests 5 \
--data "prompt_tokens=256,output_tokens=128"
Benchmarks Info:
==================================================================================================================================================
Metadata                                     |||| Requests Made  ||| Prompt Tok/Req ||| Output Tok/Req||| Prompt Tok Total||| Output Tok Total  ||
   Benchmark| Start Time| End Time| Duration (s)|  Comp|  Inc|  Err|  Comp|   Inc| Err|  Comp|  Inc| Err|  Comp|   Inc|  Err|   Comp|   Inc|   Err
------------|-----------|---------|-------------|------|-----|-----|------|------|----|------|-----|----|------|------|-----|-------|------|------
concurrent@1|   16:52:00| 16:52:10|         10.0|     1|    1|    0| 285.0| 256.0| 0.0| 128.0|  7.0| 0.0|   285|   256|    0|    128|     7|     0
concurrent@2|   16:52:15| 16:52:25|         10.0|     1|    2|    0| 285.0| 256.0| 0.0| 128.0| 68.0| 0.0|   285|   512|    0|    128|   136|     0
concurrent@4|   16:52:31| 16:52:41|         10.0|     1|    4|    0| 285.0| 256.0| 0.0| 128.0| 98.0| 0.0|   285|  1024|    0|    128|   392|     0
concurrent@8|   16:52:46| 16:52:56|         10.0|     1|    4|    0| 285.0| 256.0| 0.0| 128.0| 97.8| 0.0|   285|  1024|    0|    128|   391|     0
==================================================================================================================================================
```




```
