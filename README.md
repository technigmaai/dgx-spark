# DGX Spark Recipes

This repository contains small deployment notes and Spark vLLM recipe files for running optimized LLM serving setups.

## Contents

| Path | Description |
|---|---|
| [spark-vllm-docker/rdtand-Qwen3.6-27B-PrismaQuant-5.5bit-vllm](spark-vllm-docker/rdtand-Qwen3.6-27B-PrismaQuant-5.5bit-vllm) | Qwen3.6 27B PrismaQuant vLLM recipe, FlashQLA setup notes, and chat-template fix. |
| [spark-vllm-docker/rdtand-Qwen3.6-35B-A3B-PrismaQuant-4.75bit-vllm](spark-vllm-docker/rdtand-Qwen3.6-35B-A3B-PrismaQuant-4.75bit-vllm) | Qwen3.6 35B A3B PrismaQuant vLLM recipe, chat-template files, and initial benchmark report. |

## Current Recipe

The active recipe areas are for `rdtand/Qwen3.6-27B-PrismaQuant-5.5bit-vllm` and `rdtand/Qwen3.6-35B-A3B-PrismaQuant-4.75bit-vllm`.

Start with:

```bash
cd spark-vllm-docker/rdtand-Qwen3.6-27B-PrismaQuant-5.5bit-vllm
```

See the recipe README for build steps, runtime defaults, FlashQLA setup, and chat-template benchmark notes:

[Qwen3.6 27B PrismaQuant vLLM Notes](spark-vllm-docker/rdtand-Qwen3.6-27B-PrismaQuant-5.5bit-vllm/README.md)

[Qwen3.6 35B A3B PrismaQuant vLLM Notes](spark-vllm-docker/rdtand-Qwen3.6-35B-A3B-PrismaQuant-4.75bit-vllm/README.md)

## License

See [LICENSE.txt](LICENSE.txt).
