# Qwen3.6 27B PrismaQuant vLLM Notes

This directory contains the Spark vLLM recipe notes for `rdtand/Qwen3.6-27B-PrismaQuant-5.5bit-vllm`, including the FlashQLA mod setup and the custom Qwen3.6 chat-template fix.

## Recipe Matrix

| Recipe | Container | Served model name | Mods |
|---|---|---|---|
| [qwen3.6-27b-pq.yaml](qwen3.6-27b-pq.yaml) | `vllm-node-dflash` | `gx10 qwen3.6-27b` | `mods/flashqla`, `mods/fix-qwen3.6-chat-template` |
| [qwen3.6-27b-pq-20260515.yaml](qwen3.6-27b-pq-20260515.yaml) | `vllm-node-dflash-20260515` | `gx10 qwen3.6-27b` | `mods/flashqla-20260514`, `mods/fix-qwen3.6-chat-template` |
| [qwen3.6-27b-pq-20260519.yaml](qwen3.6-27b-pq-20260519.yaml) | `vllm-node-dflash` | `gx10 qwen3.6-27b` | `mods/flashqla-20260514`, `mods/fastokens`, `mods/fix-qwen3.6-chat-template` |

## Build Image

Build the vLLM image with PR `40898` and TF5 enabled:

```bash
./build-and-copy.sh --apply-vllm-pr 40898 --tf5 -t vllm-node-dflash
```

## FlashQLA-Blackwell Mod

Install FlashQLA-Blackwell from the upstream repository, then copy the mod into `spark-vllm-docker`.

### Steps

1. Clone the FlashQLA-Blackwell repository if it is not already present:

   ```bash
   git clone https://github.com/Plaaasma/FlashQLA-Blackwell.git
   ```

2. Update an existing checkout:

   ```bash
   cd FlashQLA-Blackwell
   git pull
   ```

3. Let the mod apply the v2 patch automatically when the vLLM version matches the updated upstream signature.

4. Copy the mod into `spark-vllm-docker`:

   ```bash
   SPARK_DIR=~/Development/ai-tools/spark-vllm-docker
   cp -r vllm $SPARK_DIR/mods/flashqla
   cp -r flash_qla setup.py LICENSE $SPARK_DIR/mods/flashqla/
   ```

5. Ensure the recipe includes the FlashQLA mod:

   ```yaml
   mods:
     - mods/flashqla
   ```

6. Run the recipe:

   ```bash
   ./run-recipe.sh qwen3.6-27b-pq --solo
   ```

### Verification

Look for this startup log line:

```text
INFO [gdn_linear_attn.py:245] Using FlashQLA TileLang GDN prefill kernel (Blackwell)
```

## Fastokens Wheel Mod

Install `fastokens` as a `spark-vllm-docker` runtime mod using a prebuilt wheel. This avoids Rust compilation during container startup and enables the `fastokens` tokenizer mode used by [qwen3.6-27b-pq-20260519.yaml](qwen3.6-27b-pq-20260519.yaml).

### Steps

1. Build the wheel from the `fastokens` checkout:

   ```bash
   cd ~/Development/ai-tools/tmp/fastokens
   uv build --wheel
   ```

2. Copy the wheel into the Spark vLLM mod directory:

   ```bash
   SPARK_DIR=~/Development/ai-tools/spark-vllm-docker
   mkdir -p $SPARK_DIR/mods/fastokens
   cp dist/*.whl $SPARK_DIR/mods/fastokens/
   ```

3. Create the mod installer script at `$SPARK_DIR/mods/fastokens/run.sh`:

   ```bash
   cat > $SPARK_DIR/mods/fastokens/run.sh <<'SCRIPT'
   #!/usr/bin/env bash
   set -euo pipefail

   cd "$(dirname "$0")"

   echo "[fastokens] checking installation..."

   if python3 - <<'PY' >/dev/null 2>&1
   import fastokens
   PY
   then
     echo "[fastokens] already installed, skipping"
     exit 0
   fi

   echo "[fastokens] installing wheel..."

   if command -v uv >/dev/null 2>&1; then
     uv pip install --system ./*.whl
   else
     python3 -m pip install ./*.whl
   fi

   python3 - <<'PY'
   import fastokens
   print("[fastokens] import OK:", fastokens)
   PY
   SCRIPT
   ```

4. Make the installer executable:

   ```bash
   chmod +x $SPARK_DIR/mods/fastokens/run.sh
   ```

5. Ensure the recipe includes the Fastokens mod and tokenizer mode:

   ```yaml
   mods:
     - mods/fastokens

   defaults:
     tokenizer_mode: fastokens
   ```

6. Ensure the vLLM command includes:

   ```bash
   --tokenizer-mode {tokenizer_mode}
   ```

### Verification

The mod directory should contain at least:

```text
run.sh
fastokens-*.whl
```

Look for one of these startup paths:

```text
[fastokens] installing wheel...
[fastokens] import OK: <module 'fastokens' ...>
```

or:

```text
[fastokens] already installed, skipping
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
| Tokenizer mode | `fastokens` in [qwen3.6-27b-pq-20260519.yaml](qwen3.6-27b-pq-20260519.yaml) |
| Tool parser | `qwen3_xml` |
| Reasoning parser | `qwen3` |
| Speculative decoding | DFlash, `z-lab/Qwen3.6-27B-DFlash`, 15 tokens |

## Recommendation

Use [qwen3.6-27b-pq-20260519.yaml](qwen3.6-27b-pq-20260519.yaml) when testing Fastokens. It keeps the latest FlashQLA mod path and adds `mods/fastokens` plus `--tokenizer-mode fastokens`. Keep [fixed_chat_template-v5.jinja](fix-qwen3.6-chat-template/fixed_chat_template-v5.jinja) as the active chat template unless a later benchmark run improves on its current zero-failure result.
