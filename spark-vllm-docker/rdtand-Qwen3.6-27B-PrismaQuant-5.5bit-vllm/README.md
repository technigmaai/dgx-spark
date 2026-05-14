## **Build Image with PR** 40898

`./build-and-copy.sh --apply-vllm-pr 40898 --tf5 -t vllm-node-dflash`


---

## Applying FlashQLA-Blackwell to spark-vllm-docker

### **Steps**


1. **Pull the PR branch** from [FlashQLA-Blackwell PR #3](https://github.com/Plaaasma/FlashQLA-Blackwell/pull/3):

   `cd FlashQLA-Blackwell`
2. The mod applies the **v2 patch** automatically when your vLLM version matches the updated upstream signature
3. **Re-copy the mod** into spark-vllm-docker:

   `SPARK_DIR=~/Development/ai-tools/spark-vllm-docker`

   `cp -r vllm $SPARK_DIR/mods/flashqla`

   `cp -r flash_qla setup.py LICENSE $SPARK_DIR/mods/flashqla/`
4. **Ensure your recipe YAML has the mod listed** under `mods:`:

   `mods:`

   `  - mods/flashqla`
5. **Run the recipe** as normal:

   `./run-recipe.sh qwen3.6-27b-pq --solo`

### **Verification**

Look for this line in startup logs:

`INFO [gdn_linear_attn.py:245] Using FlashQLA TileLang GDN prefill kernel (Blackwell)`


---

## Chat Template

`/mods/fix-qwen3.6-chat-template`

* `fixed_chat_template.jinja`
* `run.sh`