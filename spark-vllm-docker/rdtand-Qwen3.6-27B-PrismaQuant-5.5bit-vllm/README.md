# Qwen3.6 27B PrismaQuant vLLM Notes

This directory contains the Spark vLLM recipe notes for `rdtand/Qwen3.6-27B-PrismaQuant-5.5bit-vllm`, including the FlashQLA mod setup and the custom Qwen3.6 chat-template fix.

## Recipe Matrix

| Recipe | Container | Served model name | Mods |
|---|---|---|---|
| [qwen3.6-27b-pq.yaml](qwen3.6-27b-pq.yaml) | `vllm-node-dflash` | `gx10 qwen3.6-27b` | `mods/flashqla`, `mods/fix-qwen3.6-chat-template` |
| [qwen3.6-27b-pq-20260515.yaml](qwen3.6-27b-pq-20260515.yaml) | `vllm-node-dflash-20260515` | `gx10 qwen3.6-27b` | `mods/flashqla-20260514`, `mods/fix-qwen3.6-chat-template` |

## Build Image

Build the vLLM image with PR `40898` and TF5 enabled:

```bash
./build-and-copy.sh --apply-vllm-pr 40898 --tf5 -t vllm-node-dflash
```

## FlashQLA-Blackwell Mod

Apply FlashQLA-Blackwell from [PR #3](https://github.com/Plaaasma/FlashQLA-Blackwell/pull/3), then copy the updated mod into `spark-vllm-docker`.

### Steps

1. Pull the PR branch in the FlashQLA-Blackwell checkout:

   ```bash
   cd FlashQLA-Blackwell
   ```

2. Let the mod apply the v2 patch automatically when the vLLM version matches the updated upstream signature.

3. Copy the mod into `spark-vllm-docker`:

   ```bash
   SPARK_DIR=~/Development/ai-tools/spark-vllm-docker
   cp -r vllm $SPARK_DIR/mods/flashqla
   cp -r flash_qla setup.py LICENSE $SPARK_DIR/mods/flashqla/
   ```

4. Ensure the recipe includes the FlashQLA mod:

   ```yaml
   mods:
     - mods/flashqla
   ```

5. Run the recipe:

   ```bash
   ./run-recipe.sh qwen3.6-27b-pq --solo
   ```

### Verification

Look for this startup log line:

```text
INFO [gdn_linear_attn.py:245] Using FlashQLA TileLang GDN prefill kernel (Blackwell)
```

## Chat Template Mod

| Item | Path |
|---|---|
| Mod directory | [fix-qwen3.6-chat-template](fix-qwen3.6-chat-template) |
| Template notes | [fix-qwen3.6-chat-template/README.md](fix-qwen3.6-chat-template/README.md) |
| Active template | [fixed_chat_template-v5.jinja](fix-qwen3.6-chat-template/fixed_chat_template-v5.jinja) |
| Install script | [run.sh](fix-qwen3.6-chat-template/run.sh) |
| Benchmark reports | [chat-templates-test-results](fix-qwen3.6-chat-template/chat-templates-test-results) |

The chat-template mod installs `fixed_chat_template-v5.jinja` as `$WORKSPACE_DIR/chat_template.jinja`. The recipes then pass it to vLLM with:

```bash
--chat-template {chat_template}
```

## Runtime Defaults

| Setting | Value |
|---|---|
| Model | `rdtand/Qwen3.6-27B-PrismaQuant-5.5bit-vllm` |
| Port | `8000` |
| Tensor parallel | `1` |
| Max model length | `262144` |
| Max batched tokens | `16384` |
| Max sequences | `3` |
| GPU memory utilization | `0.85` |
| Tool parser | `qwen3_xml` |
| Reasoning parser | `qwen3` |
| Speculative decoding | DFlash, `z-lab/Qwen3.6-27B-DFlash`, 15 tokens |

## Recommendation

Use [qwen3.6-27b-pq-20260515.yaml](qwen3.6-27b-pq-20260515.yaml) for the newer `vllm-node-dflash-20260515` container and the latest FlashQLA mod path. Keep [fixed_chat_template-v5.jinja](fix-qwen3.6-chat-template/fixed_chat_template-v5.jinja) as the active chat template unless a later benchmark run improves on its current zero-failure result.
