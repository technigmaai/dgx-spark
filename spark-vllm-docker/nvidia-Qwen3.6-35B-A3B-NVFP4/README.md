# Qwen3.6 35B A3B NVIDIA NVFP4 vLLM Notes

This directory contains the Spark vLLM recipe files and chat-template notes for `nvidia/Qwen3.6-35B-A3B-NVFP4`.

The main recipe, chat-template file, and one benchmark report are present.

## Recipe Matrix

| Recipe | Container | Served model name | Mods |
|---|---|---|---|
| [qwen3.6-35b-a3b-nvfp4-nvidia.yaml](qwen3.6-35b-a3b-nvfp4-nvidia.yaml) | `vllm-node` | `gx10 qwen3.6-35b-a3b` | `mods/fix-qwen3.6-chat-template` |
| [qwen3.6-35b-a3b-nvfp4-nvidia-v2.yaml](qwen3.6-35b-a3b-nvfp4-nvidia-v2.yaml) | `vllm-node` | `gx10 qwen3.6-35b-a3b` | `mods/fix-qwen3.6-chat-template` |

## llama-benchy
### v1 - num_speculative_tokens: 2
```bash
| model   |             test |              t/s |       peak t/s |          ttfr (ms) |       est_ppt (ms) |      e2e_ttft (ms) |
|:--------|-----------------:|-----------------:|---------------:|-------------------:|-------------------:|-------------------:|
| gx10    |   pp2048 @ d4096 |  6461.11 ± 32.60 |                |     935.10 ± 11.68 |     860.58 ± 11.68 |     935.10 ± 11.68 |
| gx10    |    tg512 @ d4096 |     90.61 ± 3.64 |  102.33 ± 6.13 |                    |                    |                    |
| gx10    |   pp2048 @ d8192 |   6546.57 ± 9.27 |                |     1500.51 ± 6.32 |     1425.99 ± 6.32 |     1500.51 ± 6.32 |
| gx10    |    tg512 @ d8192 |     89.97 ± 4.74 |  107.00 ± 2.16 |                    |                    |                    |
| gx10    |  pp2048 @ d16384 |  6242.43 ± 20.70 |                |    2746.19 ± 17.68 |    2671.67 ± 17.68 |    2746.19 ± 17.68 |
| gx10    |   tg512 @ d16384 |     94.44 ± 5.10 |  113.67 ± 5.91 |                    |                    |                    |
| gx10    |  pp2048 @ d32768 | 5336.06 ± 175.45 |                |   6001.39 ± 213.23 |   5926.86 ± 213.23 |   6001.91 ± 212.87 |
| gx10    |   tg512 @ d32768 |     91.36 ± 3.14 | 121.00 ± 11.78 |                    |                    |                    |
| gx10    |  pp2048 @ d65536 |   4268.82 ± 6.95 |                |   14410.07 ± 52.26 |   14335.55 ± 52.26 |   14410.07 ± 52.26 |
| gx10    |   tg512 @ d65536 |     86.26 ± 0.53 |  101.67 ± 0.47 |                    |                    |                    |
| gx10    | pp2048 @ d131072 |  2955.67 ± 14.37 |                |  40905.73 ± 277.92 |  40831.20 ± 277.92 |  40905.73 ± 277.92 |
| gx10    |  tg512 @ d131072 |     73.94 ± 6.99 |   98.67 ± 1.70 |                    |                    |                    |
| gx10    | pp2048 @ d256000 |   1878.20 ± 8.39 |                | 124687.27 ± 565.42 | 124612.75 ± 565.42 | 124687.27 ± 565.42 |
| gx10    |  tg512 @ d256000 |     67.09 ± 2.57 |   87.67 ± 4.92 |                    |                    |                    |
```

### v2 - num_speculative_tokens: 3
```bash
| model   |             test |              t/s |       peak t/s |          ttfr (ms) |       est_ppt (ms) |      e2e_ttft (ms) |
|:--------|-----------------:|-----------------:|---------------:|-------------------:|-------------------:|-------------------:|
| gx10    |   pp2048 @ d4096 | 6271.23 ± 162.69 |                |     960.60 ± 14.79 |     889.22 ± 14.79 |     960.60 ± 14.79 |
| gx10    |    tg512 @ d4096 |     96.76 ± 5.59 | 126.00 ± 15.25 |                    |                    |                    |
| gx10    |   pp2048 @ d8192 |  6398.93 ± 75.99 |                |    1524.43 ± 27.51 |    1453.05 ± 27.51 |    1524.43 ± 27.51 |
| gx10    |    tg512 @ d8192 |     91.97 ± 2.37 |  114.33 ± 4.50 |                    |                    |                    |
| gx10    |  pp2048 @ d16384 |  6103.91 ± 94.35 |                |    2808.98 ± 29.12 |    2737.60 ± 29.12 |    2808.98 ± 29.12 |
| gx10    |   tg512 @ d16384 |     93.13 ± 4.43 |  114.33 ± 0.94 |                    |                    |                    |
| gx10    |  pp2048 @ d32768 |  4967.28 ± 27.16 |                |     6406.93 ± 5.91 |     6335.55 ± 5.91 |     6406.93 ± 5.91 |
| gx10    |   tg512 @ d32768 |     87.64 ± 4.58 | 140.00 ± 13.93 |                    |                    |                    |
| gx10    |  pp2048 @ d65536 |   3947.49 ± 1.75 |                |   15604.70 ± 38.36 |   15533.33 ± 38.36 |   15604.70 ± 38.36 |
| gx10    |   tg512 @ d65536 |     87.26 ± 0.77 |  117.33 ± 4.64 |                    |                    |                    |
| gx10    | pp2048 @ d131072 |  2786.68 ± 16.59 |                |  43325.74 ± 227.17 |  43254.36 ± 227.17 |  43325.74 ± 227.17 |
| gx10    |  tg512 @ d131072 |     81.11 ± 3.19 | 126.00 ± 20.99 |                    |                    |                    |
| gx10    | pp2048 @ d256000 |   1813.23 ± 7.15 |                | 129048.25 ± 577.40 | 128976.87 ± 577.40 | 129048.25 ± 577.40 |
| gx10    |  tg512 @ d256000 |     78.09 ± 3.46 |  109.33 ± 4.11 |                    |                    |                    |
```

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
