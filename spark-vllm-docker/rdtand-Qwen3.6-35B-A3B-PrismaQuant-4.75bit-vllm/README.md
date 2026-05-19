# Qwen3.6 35B A3B PrismaQuant vLLM Notes

This directory contains Spark vLLM recipe files and notes for `rdtand/Qwen3.6-35B-A3B-PrismaQuant-4.75bit-vllm`.

The main recipe, chat-template file, and one benchmark report are present.

## Recipe Matrix

| Recipe | Container | Served model name | Mods |
|---|---|---|---|
| [qwen3.6-35b-a3b-pq.yaml](qwen3.6-35b-a3b-pq.yaml) | `vllm-node-dflash` | `gx10 qwen3.6-35b` | `mods/fix-qwen3.5-autoround`, `mods/fix-qwen3-coder-next`, `mods/flashqla-20260514`, `mods/fastokens`, `mods/fix-qwen3.6-chat-template` |

## Run Recipe

```bash
./run-recipe.sh qwen3.6-35b-a3b-pq --solo
```

## Chat Template Mod

| Item | Path |
|---|---|
| Mod directory | [fix-qwen3.6-chat-template](fix-qwen3.6-chat-template) |
| Template notes | [fix-qwen3.6-chat-template/README.md](fix-qwen3.6-chat-template/README.md) |
| Active template | [fixed_chat_template.jinja](fix-qwen3.6-chat-template/fixed_chat_template.jinja) |
| Benchmark reports | [chat-templates-test-results](fix-qwen3.6-chat-template/chat-templates-test-results) |

## Runtime Defaults

| Setting | Value |
|---|---|
| Model | `rdtand/Qwen3.6-35B-A3B-PrismaQuant-4.75bit-vllm` |
| Port | `8000` |
| Host | `0.0.0.0` |
| GPU memory utilization | `0.8` |
| Max model length | `262144` |
| Max batched tokens | `32768` |
| Max sequences | `4` |
| Tokenizer mode | `fastokens` |
| Tool parser | `qwen3_coder` |
| Reasoning parser | `qwen3` |
| Speculative decoding | DFlash, `z-lab/Qwen3.6-35B-A3B-DFlash`, 15 tokens |
| Quantization | `compressed-tensors` |

## Current Benchmark

| Template | Report | Score | Result summary |
|---|---|---:|---|
| [fixed_chat_template.jinja](fix-qwen3.6-chat-template/fixed_chat_template.jinja) | [2026-05-19T08-02-45Z_f177f5-v1.md](fix-qwen3.6-chat-template/chat-templates-test-results/2026-05-19T08-02-45Z_f177f5-v1.md) | 91 | 62 passed, 10 partial, 2 failed |

The first benchmark has one safety-critical failure in `TC-60` and a required-tool failure in `TC-45`.

## Next Steps

1. Iterate on [fixed_chat_template.jinja](fix-qwen3.6-chat-template/fixed_chat_template.jinja) to remove the `TC-60` safety failure.
2. Update this README when a newer benchmark beats the current result.
