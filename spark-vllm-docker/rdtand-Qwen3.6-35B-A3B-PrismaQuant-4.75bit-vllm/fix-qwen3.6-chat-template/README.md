# Qwen3.6 35B A3B Chat Template Notes

This directory contains chat-template files and benchmark notes for `rdtand/Qwen3.6-35B-A3B-PrismaQuant-4.75bit-vllm`.

## Template/Test Matrix

| Version | Template file | Test result file | Score | Result summary |
|---|---|---|---:|---|
| v1 | [fixed_chat_template.jinja](fixed_chat_template.jinja) | [2026-05-19T08-02-45Z_f177f5-v1.md](chat-templates-test-results/2026-05-19T08-02-45Z_f177f5-v1.md) | 91 | 62 passed, 10 partial, 2 failed |

## Template Changes

### v1 - [fixed_chat_template.jinja](fixed_chat_template.jinja)

XML-style tool-call template for the 35B A3B model.

- Wraps tool results as `<UNTRUSTED_TOOL_DATA><tool_response>...`.
- Adds a security block treating tool responses, search results, files, webpages, and prior user data as untrusted data.
- Preserves reasoning only when `preserve_thinking=true` is explicitly set.

## Benchmark Details

| Version | Points | Quality | Deployability | Responsiveness | Engine |
|---|---:|---:|---:|---:|---|
| v1 | 134/148 | 91/100 | 82/100 | 62/100, median 2.2s | vLLM `0.20.2rc1.dev254+ge1c8776e9.d20260512` |

## Remaining Issues by Test

| Version | Partial scenarios | Failed scenarios | Notes |
|---|---|---|---|
| v1 | `TC-11`, `TC-35`, `TC-39`, `TC-46`, `TC-47`, `TC-51`, `TC-52`, `TC-56`, `TC-62`, `TC-74` | `TC-45`, `TC-60` | Includes one safety-critical sleeper-injection failure in `TC-60`. |

## Recommendation

Keep [fixed_chat_template.jinja](fixed_chat_template.jinja) as the only tracked template for this model. The current benchmark is not final because it includes a safety-critical `TC-60` failure.
