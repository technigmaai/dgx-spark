# Qwen3.6 Chat Template Test Notes

This directory contains iterative Jinja chat templates for `rdtand/Qwen3.6-27B-PrismaQuant-5.5bit-vllm` and the matching `tool-eval-bench` runs under `chat-template-tests/`.

`run.sh` currently installs `fixed_chat_template-v5.jinja` as `$WORKSPACE_DIR/chat_template.jinja`.

## Template/Test Matrix

| Version | Template file | Test result file | Score | Result summary |
|---|---|---|---:|---|
| v1 | [fixed_chat_template.jinja](fixed_chat_template.jinja) | [2026-05-14T19-09-15Z_f177f5-v1.md](chat-template-tests/2026-05-14T19-09-15Z_f177f5-v1.md) | 90 | 62 passed, 9 partial, 3 failed |
| v2 | [fixed_chat_template-v2.jinja](fixed_chat_template-v2.jinja) | [2026-05-15T07-22-23Z_f177f5-v2.md](chat-template-tests/2026-05-15T07-22-23Z_f177f5-v2.md) | 91 | 63 passed, 8 partial, 3 failed |
| v3 | [fixed_chat_template-v3.jinja](fixed_chat_template-v3.jinja) | [2026-05-15T08-00-54Z_f177f5-v3.md](chat-template-tests/2026-05-15T08-00-54Z_f177f5-v3.md) | 92 | 64 passed, 8 partial, 2 failed |
| v4 | [fixed_chat_template-v4.jinja](fixed_chat_template-v4.jinja) | [2026-05-15T10-54-39Z_f177f5-v4.md](chat-template-tests/2026-05-15T10-54-39Z_f177f5-v4.md) | 92 | 63 passed, 10 partial, 1 failed |
| v5 | [fixed_chat_template-v5.jinja](fixed_chat_template-v5.jinja) | [2026-05-15T11-40-26Z_f177f5-v5.md](chat-template-tests/2026-05-15T11-40-26Z_f177f5-v5.md) | 96 | 68 passed, 6 partial, 0 failed |

## Template Changes

### v1 - [fixed_chat_template.jinja](fixed_chat_template.jinja)

Baseline XML-style tool-call template.

- Emits tools inside the system message with `<tools>...</tools>`.
- Requires tool calls as `<tool_call><function=...><parameter=...>...`.
- Preserves reasoning broadly unless `preserve_thinking` disables it.
- Wraps tool results as plain `<tool_response>` blocks.

### v2 - [fixed_chat_template-v2.jinja](fixed_chat_template-v2.jinja)

Adds stronger tool-use and safety instructions.

- Adds explicit sections for security, tool selection, restraint, parameter discipline, task completion, multi-turn state, recovery, execution, and output discipline.
- Wraps tool results as `<UNTRUSTED_TOOL_DATA><tool_response>...`.
- Tightens reasoning preservation so old reasoning is not carried into unrelated turns by default.
- Recognizes both `<tool_response>` and `<UNTRUSTED_TOOL_DATA>` while tracking active tool chains.

### v3 - [fixed_chat_template-v3.jinja](fixed_chat_template-v3.jinja)

Improves required tool-use and research/comparison behavior.

- Updates tool restraint for explicit runtime/user-required tool use.
- Strengthens task completion rules for downstream actions such as notify, email, schedule, send, compare, and summarize.
- Adds research discipline for market, benchmark, latest/current, comparison, and open-ended information requests.
- Keeps untrusted tool-data wrapping and safer reasoning preservation from v2.

### v4 - [fixed_chat_template-v4.jinja](fixed_chat_template-v4.jinja)

Experiments with a more explicit action-ledger style prompt.

- Adds `<ACTION_LEDGER>` requiring an internal checklist for information, transformations, decisions, and external side effects.
- Expands execution, recovery, research, security, and output discipline wording.
- Keeps untrusted tool-data wrapping and explicit `preserve_thinking=true` behavior.
- Reduces hard failures to one, but increases partial results compared with v3.

### v5 - [fixed_chat_template-v5.jinja](fixed_chat_template-v5.jinja)

Best tested template so far.

- Keeps the v3 instruction layout and avoids the heavier v4 action-ledger wording.
- Further tightens task completion around exact channel selection: email means email, reminder means reminder, calendar means calendar.
- Requires scheduling/execution tools when the requested side effect exists.
- Adds async polling/script-execution discipline.
- Achieves the best benchmark result: 96/100 with no failed scenarios.

## Benchmark Details

| Version | Points | Quality | Deployability | Responsiveness | Engine |
|---|---:|---:|---:|---:|---|
| v1 | 133/148 | 90/100 | 73/100 | 33/100, median 4.9s | vLLM `0.20.2rc1.dev301+g38482c2d4.d20260513` |
| v2 | 134/148 | 91/100 | 72/100 | 27/100, median 5.8s | vLLM `0.20.2rc1.dev373+g88a57ac9a.d20260514` |
| v3 | 136/148 | 92/100 | 72/100 | 25/100, median 6.3s | vLLM `0.20.2rc1.dev373+g88a57ac9a.d20260514` |
| v4 | 136/148 | 92/100 | 72/100 | 24/100, median 6.5s | vLLM `0.21.1rc1.dev13+g9d49cc59e.d20260515` |
| v5 | 142/148 | 96/100 | 74/100 | 24/100, median 6.6s | vLLM `0.21.1rc1.dev13+g9d49cc59e.d20260515` |

## Remaining Issues by Test

| Version | Partial scenarios | Failed scenarios | Notes |
|---|---|---|---|
| v1 | `TC-35`, `TC-39`, `TC-46`, `TC-47`, `TC-51`, `TC-52`, `TC-56`, `TC-57`, `TC-74` | `TC-60`, `TC-61`, `TC-72` | Includes one safety-critical sleeper-injection failure in `TC-60`. |
| v2 | `TC-14`, `TC-35`, `TC-46`, `TC-51`, `TC-52`, `TC-56`, `TC-57`, `TC-62` | `TC-45`, `TC-47`, `TC-72` | Safety failure is removed, but required-tool and correction handling regressions remain. |
| v3 | `TC-35`, `TC-46`, `TC-47`, `TC-51`, `TC-56`, `TC-57`, `TC-62`, `TC-74` | `TC-17`, `TC-61` | Better overall quality, but still has two failures. |
| v4 | `TC-32`, `TC-34`, `TC-35`, `TC-46`, `TC-47`, `TC-51`, `TC-52`, `TC-56`, `TC-57`, `TC-62` | `TC-45` | Fewer failures, more partials. |
| v5 | `TC-35`, `TC-46`, `TC-47`, `TC-51`, `TC-56`, `TC-57` | none | Best current candidate. |

## Recommendation

Use `fixed_chat_template-v5.jinja` unless a future run beats its `96/100` score and zero-failure result. The remaining partials are mostly around restraint on trivial calculator use, long multi-step state tracking, correction handling, planning completion, and exact notification channel selection.
