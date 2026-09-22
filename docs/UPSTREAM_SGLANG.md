# Upstream SGLang Reference

This document separates the upstream SGLang background from this repository's
multi-model routing work.

## Source and Attribution

`sglang-multi-model-router` was initialized from a source snapshot of
[sgl-project/sglang](https://github.com/sgl-project/sglang) at
[`b63f841`](https://github.com/sgl-project/sglang/commit/b63f8416b3b73bafdec029005c5db36bad207b44).
The local source was a shallow clone, so this independent repository begins
with a new root commit rather than the full upstream Git history.

The imported SGLang code, its notices, and the [Apache-2.0 license](../LICENSE)
remain in this repository. This is an independent project, not a GitHub fork:
it will not automatically receive upstream changes, and changes here are not
sent upstream unless maintainers deliberately prepare an upstream contribution.

## What SGLang Provides

SGLang is the inference framework beneath this project. It provides the model
runtime, scheduler, KV cache, OpenAI-compatible serving APIs, distributed
execution primitives, and the existing router used after a concrete model has
been selected. This project adds a model dimension above those mechanisms; it
does not replace SGLang's scheduler or model execution engine.

## Upstream Resources

- [Official SGLang repository](https://github.com/sgl-project/sglang)
- [Official README](https://github.com/sgl-project/sglang#readme)
- [SGLang documentation](https://docs.sglang.io/)
- [SGLang website](https://www.sglang.io/)
- [Upstream roadmap](https://roadmap.sglang.io/)

For this repository's own architecture, configuration, and operational scope,
return to the [project README](../README.md).
