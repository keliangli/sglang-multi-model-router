# SGLang Multi-Model Router

An experimental orchestration layer that serves different SGLang model
runtimes behind one resource-aware OpenAI-compatible endpoint.

> 中文简介：这个仓库的重点不是重复介绍 SGLang，而是在 SGLang 之上实现“多个模型、
> 多组 GPU、按实时资源状态选模型”的运行时路由能力。

## Why This Repository Exists

Production inference clusters often need to run model pools with different
model types, quantization formats, GPU allocations, and latency/capacity
profiles. A client should be able to address a logical model such as
`general-chat`; the router should then select a suitable concrete runtime,
without mixing heterogeneous models inside SGLang's native data-parallel
controller.

This project is an independent repository built from an SGLang source snapshot.
It keeps the upstream Apache-2.0 license but is not a GitHub fork and does not
automatically sync or submit changes upstream. See
[Upstream SGLang Reference](docs/UPSTREAM_SGLANG.md) for attribution, snapshot
details, and upstream documentation links.

## How Requests Flow

```text
OpenAI client: model=general-chat
             |
             v
Resource-aware ModelResolver
  health + KV pressure + token capacity + queues
             |
             v
Concrete model ID (for example, qwen-int4 or qwen-fp16)
             |
             v
SGLang router -> one independent SGLang runtime replica
```

Each configured `gpu_group` starts an isolated runtime. The resolver chooses a
concrete candidate for a virtual public model; the existing SGLang router then
selects a healthy replica of that concrete model.

## What This Project Adds

- **Static multi-model supervisor**: one JSON configuration launches isolated
  runtime replicas for distinct model types and replicas.
- **Model-aware GPU placement**: validates TP/PP group size and rejects GPU
  overlap by default; explicit sharing is limited to development experiments.
- **Resource-aware `ModelResolver`**: resolves virtual model IDs using fresh
  `/v1/loads` data: health, KV-cache pressure, token capacity, and queue state.
- **Transparent OpenAI-compatible proxying**: supports
  `/v1/chat/completions` and `/v1/completions`, and returns
  `X-SGLang-Requested-Model` and `X-SGLang-Resolved-Model` headers.
- **Operational endpoints**: `/v1/models`, `/health`, `/ready`,
  `/health/verbose`, and `/v1/runtime-loads` expose model discovery, readiness,
  and per-runtime state.

## Get Started

- [Configuration and request examples](examples/multi_model_router/README.md)
- [Static multi-model example](examples/multi_model_router/static_multi_model.json)
- [Resource-aware example](examples/multi_model_router/resource_aware_multi_model.json)
- [Detailed MVP plan](MULTI_MODEL_RUNTIME_MVP_PLAN.md)

## Validation and Current Boundaries

The parser, launch supervisor, model resolution algorithm, and HTTP proxy are
covered by focused tests. A real dual-Qwen experiment also validated that a
virtual request can be redirected away from a busy runtime to a healthy model
candidate.

This remains an experiment rather than a complete cluster manager:

- it currently routes chat-completions and completions traffic only;
- it uses SGLang runtime load metrics, not NVML physical-GPU telemetry;
- it does not yet load/sleep runtimes, reallocate GPUs, or synchronize upstream
  automatically.

## License

This repository retains the upstream [Apache-2.0 license](LICENSE). Refer to
[Upstream SGLang Reference](docs/UPSTREAM_SGLANG.md) for source attribution.
