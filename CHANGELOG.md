<!--
This changelog should always be read on `master` branch. Its contents on other branches
does not necessarily reflect the changes.
-->

# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).


## [v0.0.2] - 2026-09-24

### Added
- Support local config file mode: scheduling config can be loaded from a local JSON file via a generic `LocalFileSource` instead of the InnerAPI, enabling standalone operation and local testing without a control plane (design docs under `docs/`, SC15 integration scenario)
- Add release packaging: `make release` cross-compiles linux/amd64 and linux/arm64 tarballs, plus `make clean` for build artifacts

### Changed
- Improve request tracing: per-request log correlation via `x-request-id`, read from incoming headers or generated and written back so the llm-d engine and EPP logs share the same request ID
- Pin llm-d-router to v0.10.0-rc.1.0.20260904224622-4f8e57118894 and drop the local replace directive
- Documentation: session-affinity-scorer `strategy=session_id` parameter shape; priorityBands note on the compiler's explicit priority-0 band

## [v0.0.1] - 2026-09-13

### Added
- Initial release of ai-gateway-epp: a multi-cluster, config-driven recomposition of the llm-d EPP that consumes the ai-gateway-api InnerAPI instead of Kubernetes CRDs.
- One process serves multiple clusters: each assigned cluster runs a Cell with a resident datastore/metrics layer and a hot-swappable scheduling engine; ext-proc requests are routed to Cells by the `llm-d.ai/inference-pool` metadata injected by BFE.
- Scheduling config and primary/backup roles are consumed from the merged `epp_data/config` InnerAPI endpoint (`epp_config` + full `assignment` view) with self-matching; config changes compile a new engine that is swapped atomically, draining the old engine (FC queue eviction + in-flight request wait cap), and a compile failure on one cluster does not affect the others.
- Generic poller framework for InnerAPI sync (cluster-table discovery + EPP data watcher) with version-increment fetch and token auth; unassigned clusters are kept in the discovery hub so a late assignment does not require re-discovery.
- gRPC ext-proc and health servers with optional server-side TLS, a Kubernetes-style readiness gate, Prometheus metrics, and optional pprof on the metrics port.
- End-to-end integration tests against a fake InnerAPI.

[v0.0.2]: https://github.com/rainway-ai-gateway/ai-gateway-epp/releases/tag/v0.0.2
[v0.0.1]: https://github.com/rainway-ai-gateway/ai-gateway-epp/releases/tag/v0.0.1
