# Governance and Execution Plan - SUSE AI Observability

## Architectural Decisions

### 1. Telemetry Naming & Semantics
- **vLLM Metrics**: We will preserve the `vllm:` prefix for metrics. This ensures compatibility with existing dashboards that expect this specific naming convention.
- **Grouping Attribute**: Instead of the custom `stackpack: suse-ai` attribute, we will adopt the standard OpenTelemetry `service.namespace` attribute, set to `suse-ai`.
- **SDK Identity**: Components developed or specifically instrumented for SUSE AI will use `telemetry.sdk.name: suse-ai`.
- **Normalization**: All custom or legacy attributes (e.g., `GenAiSystem`) will be mapped to their standardized OTel equivalents (e.g., `gen_ai.system`) using the `transform` processor.

### 2. Topology Generation
- **Promotion Logic**: To enable SUSE Observability to build an architectural map, the collector will "promote" key span/metric attributes to Resource-level attributes:
    - `gen_ai.system` -> Resource Attribute
    - `db.system` -> Resource Attribute
    - `gen_ai.request.model` -> Aggregated into `gen_ai.models` Resource Attribute.

### 3. Component Coverage
The following components are officially supported in the pipeline:
- **Search/Vector DB**: OpenSearch, Milvus, Qdrant.
- **AI Engines**: Ollama, vLLM.
- **Orchestration/Proxy**: LiteLLM, Open WebUI.

---

## Execution Plan

### Phase 1: Preparation (Completed)
- [x] Gather context from SUSE AI documentation.
- [x] Analyze `suse_ai_filter.py` for Open WebUI instrumentation patterns.
- [x] Create `knowledge/` documentation for each component.

### Phase 2: Collector Manifest Update
- [ ] Review `builder-config.yaml`.
- [ ] Ensure `transformprocessor`, `resourceprocessor`, `elasticsearchreceiver`, and `prometheusreceiver` are included.
- [ ] Regenerate the collector binary if necessary.

### Phase 3: Pipeline Configuration Implementation
- [ ] **Receivers**: Define scrape jobs for all vector databases and LLM engines.
- [ ] **Processors**:
    - Implement `k8sattributes` and `resourcedetection`.
    - Create the `transform` logic for attribute normalization and promotion.
    - Configure the `resource` processor to set `service.namespace: suse-ai` where appropriate.
- [ ] **Exporters**: Set up OTLP export to SUSE Observability with proper security headers.

### Phase 4: Validation & Testing
- [ ] Deploy the collector in a test environment.
- [ ] Verify Resource attributes using the `debug` exporter.
- [ ] Validate topology mapping in SUSE Observability.
