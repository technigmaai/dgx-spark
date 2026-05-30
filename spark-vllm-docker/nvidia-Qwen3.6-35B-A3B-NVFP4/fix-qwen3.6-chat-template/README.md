# Qwen3.6 35B A3B NVIDIA NVFP4 Chat Template Notes

This directory contains chat-template files and benchmark notes for `nvidia/Qwen3.6-35B-A3B-NVFP4`.

`run.sh` currently installs `fixed_chat_template.jinja` as `$WORKSPACE_DIR/chat_template.jinja`.

## Template/Test Matrix

| Version | Template file | Test result file | Score | Result summary |
|---|---|---|---:|---|
| v1 | [fixed_chat_template.jinja](fixed_chat_template.jinja) | [2026-05-30T19-28-00Z_f177f5-v1.md](chat-templates-test-results/2026-05-30T19-28-00Z_f177f5-v1.md) | 91 | 63 passed, 8 partial, 3 failed |

## Template Changes

### v1 - [fixed_chat_template.jinja](fixed_chat_template.jinja)

XML-style tool-call template for the NVIDIA 35B A3B NVFP4 model.

- Wraps tool results as `<UNTRUSTED_TOOL_DATA><tool_response>...`.
- Adds security guidance that treats tool responses, search results, files, webpages, and prior user data as untrusted data.
- Preserves reasoning only when `preserve_thinking=true` is explicitly set.
- Includes task-completion guidance for exact channel selection, required side-effect tools, async polling, and multi-turn state tracking.

## Benchmark Details

| Version | Points | Quality | Deployability | Responsiveness | Engine |
|---|---:|---:|---:|---:|---|
| v1 | 134/148 | 91/100 | 82/100 | 62/100, median 2.2s | vLLM `0.22.1rc1.dev3+g5dbf1605a.d20260529` |

## Remaining Issues by Test

| Version | Partial scenarios | Failed scenarios | Notes |
|---|---|---|---|
| v1 | `TC-11`, `TC-30`, `TC-35`, `TC-46`, `TC-51`, `TC-53`, `TC-56`, `TC-74` | `TC-39`, `TC-40`, `TC-45` | Safety-critical `TC-60` passes, but simple arithmetic, customer-order tool use, and `tool_choice='required'` need iteration. |

## Recommendation

Keep [fixed_chat_template.jinja](fixed_chat_template.jinja) as the only tracked template for this model until a newer benchmark improves on the current `91/100` score and removes the three failed scenarios.
