

Reference to for more details
https://github.com/infiniflow/ragflow

## Environment
Hardware: HX370
Operating System: Ubuntu 2024.04 LTS

## Get the RAGFlow

```bash
git clone https://github.com/infiniflow/ragflow.git
```


## Embedding Models in RAGFlow
Embedding models are a core component of RAGFlow's pipeline. They convert text chunks from your documents into dense vector representations, enabling semantic similarity searches during retrieval.

If you have not external deployed embeddging model, let ragflow come with this model.

```bash
sed -i 's/^RAGFLOW_IMAGE=infiniflow\/ragflow:v0\.20\.5-slim/#RAGFLOW_IMAGE=infiniflow\/ragflow:v0\.20\.5-slim/' ./docker/.env
sed -i 's/^# *RAGFLOW_IMAGE=infiniflow\/ragflow:v0\.20\.5/RAGFLOW_IMAGE=infiniflow\/ragflow:v0\.20\.5/' ./docker/.env
```


## start ragflow

Use CPU for embedding and DeepDoc tasks:
```bash
cd ./docker
sudo docker compose -f docker-compose.yml up -d
```

It's going to take a while to download the models from internal for first time run. The log looks like:
```
[+] Running 69/71
 ✔ minio Pulled                                                                                                      117.6s
 ✔ mysql Pulled                                                                                                      214.0s
 ✔ redis Pulled                                                                                                      132.4s
 ✔ es01 Pulled                                                                                                       230.8s
 ⠦ ragflow [⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿]  7.59GB / 7.593GB Pulling                                              1725.7s
```
After downloaded successful, the status will be:

```
$ sudo docker compose -f docker-compose.yml up -d
[sudo] password for hx370:
[+] Running 5/5
 ✔ Container ragflow-mysql   Healthy                                                                                   0.5s
 ✔ Container ragflow-es-01   Running                                                                                   0.0s
 ✔ Container ragflow-redis   Running                                                                                   0.0s
 ✔ Container ragflow-minio   Running                                                                                   0.0s
 ✔ Container ragflow-server  Running                                                                                   0.0s

```



Other optionts to use GPU to accelerate embedding and DeepDoc tasks:
```bash
cd ragflow/docker
sudo docker compose -f docker-compose-gpu.yml up -d
```

Check the log to see if it's running successfully

```
2025-09-28 19:17:22,859 INFO     28 found 0 gpus
2025-09-28 19:17:31,037 INFO     28 init database on cluster mode successfully
2025-09-28 19:17:44,761 INFO     28 load_model /ragflow/rag/res/deepdoc/det.onnx uses CPU
2025-09-28 19:17:44,875 INFO     28 load_model /ragflow/rag/res/deepdoc/rec.onnx uses CPU
2025-09-28 19:18:01,924 INFO     28
        ____   ___    ______ ______ __
       / __ \ /   |  / ____// ____// /____  _      __
      / /_/ // /| | / / __ / /_   / // __ \| | /| / /
     / _, _// ___ |/ /_/ // __/  / // /_/ /| |/ |/ /
    /_/ |_|/_/  |_|\____//_/    /_/ \____/ |__/|__/


2025-09-28 19:18:01,925 INFO     28 RAGFlow version: v0.20.5 full
2025-09-28 19:18:01,925 INFO     28 project base: /ragflow
2025-09-28 19:18:01,925 INFO     28 Current configs, from /ragflow/conf/service_conf.yaml:

```


## Configuration

open a brower to access:
http://localhost:80

first time need to register and login


dataset->Create Dataset->Input Dataset Name->Add file

