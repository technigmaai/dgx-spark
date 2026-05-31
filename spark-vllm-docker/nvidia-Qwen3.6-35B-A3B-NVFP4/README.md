# Qwen3.6 35B A3B NVIDIA NVFP4 vLLM Notes

This directory contains the Spark vLLM recipe files and chat-template notes for `nvidia/Qwen3.6-35B-A3B-NVFP4`.

The main recipe, chat-template file, and one benchmark report are present.

## Recipe Matrix

| Recipe | Container | Served model name | Mods |
|---|---|---|---|
| [qwen3.6-35b-a3b-nvfp4-nvidia.yaml](qwen3.6-35b-a3b-nvfp4-nvidia.yaml) | `vllm-node` | `gx10 qwen3.6-35b-a3b` | `mods/fix-qwen3.6-chat-template` |
| [qwen3.6-35b-a3b-nvfp4-nvidia.yaml](qwen3.6-35b-a3b-nvfp4-nvidia-v2.yaml) | `vllm-node` | `gx10 qwen3.6-35b-a3b` | `mods/fix-qwen3.6-chat-template` |

## Run Recipe

```bash
./run-recipe.sh qwen3.6-35b-a3b-nvfp4-nvidia --solo
```

## Runtime Profile

This recipe targets the NVIDIA NVFP4 checkpoint with native MTP speculation, FP8 KV cache, FlashInfer attention, and a long-context Spark profile.

Unlike the PrismaQuant recipes, this recipe does not currently use the shared FlashQLA or Fastokens mods. The tokenizer mode is left at `auto`, and the vLLM command passes:

```bash
--tokenizer-mode {tokenizer_mode}
```

## Chat Template Mod

| Item | Path |
|---|---|
| Mod directory | [fix-qwen3.6-chat-template](fix-qwen3.6-chat-template) |
| Template notes | [fix-qwen3.6-chat-template/README.md](fix-qwen3.6-chat-template/README.md) |
| Active template | [fixed_chat_template.jinja](fix-qwen3.6-chat-template/fixed_chat_template.jinja) |
| Install script | [run.sh](fix-qwen3.6-chat-template/run.sh) |
| Benchmark reports | [chat-templates-test-results](fix-qwen3.6-chat-template/chat-templates-test-results) |

The chat-template mod installs `fixed_chat_template.jinja` as `$WORKSPACE_DIR/chat_template.jinja`. The recipe then passes it to vLLM with:

```bash
--chat-template {chat_template}
```

## Runtime Defaults

| Setting | Value |
|---|---|
| Model | `nvidia/Qwen3.6-35B-A3B-NVFP4` |
| Port | `8000` |
| Host | `0.0.0.0` |
| GPU memory utilization | `0.7` |
| Max model length | `262144` |
| Max batched tokens | `32768` |
| Max sequences | `2` |
| Tokenizer mode | `auto` |
| Tool parser | `qwen3_coder` |
| Reasoning parser | `qwen3` |
| Speculative decoding | Native MTP, 2 tokens |
| Quantization | `compressed-tensors` |
| KV cache dtype | `fp8` |
| Attention backend | `flashinfer` |
| Load format | `instanttensor` |

## Current Benchmark

| Template | Report | Score | Result summary |
|---|---|---:|---|
| [fixed_chat_template.jinja](fix-qwen3.6-chat-template/fixed_chat_template.jinja) | [2026-05-30T19-28-00Z_f177f5-v1.md](fix-qwen3.6-chat-template/chat-templates-test-results/2026-05-30T19-28-00Z_f177f5-v1.md) | 91 | 63 passed, 8 partial, 3 failed |

The first benchmark is strong overall, but it still has three hard failures: `TC-39`, `TC-40`, and `TC-45`.

## Next Steps

1. Iterate on [fixed_chat_template.jinja](fix-qwen3.6-chat-template/fixed_chat_template.jinja) to reduce over-tooling on simple arithmetic and improve required-tool behavior.
2. Re-run `tool-eval-bench` after each template change and add the report under [chat-templates-test-results](fix-qwen3.6-chat-template/chat-templates-test-results).
3. Update this README when a newer benchmark beats the current result.
