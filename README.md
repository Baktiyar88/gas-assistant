## Open WebUI — DevOps notes

### Stack
- **Services**:
  - `ollama` — LLM backend, listens on `11434`
  - `open-webui` — Web UI, exposed as `3000` (host) → `8080` (container)
- **Artifacts**: single `Dockerfile` + `docker-compose.yaml` in repo root

### Build & run
```bash
docker compose build
docker compose up -d
```

Management:
```bash
docker compose ps
docker compose logs -f open-webui
docker compose logs -f ollama
docker compose down
```

### Access
- Web UI: `http://localhost:3000` or `http://<host>:3000`
- Ollama from inside the compose network: `http://ollama:11434`

### GPU configuration (required)
- `docker-compose.yaml` (current defaults):
  - `ollama` uses GPU via `deploy.resources.reservations.devices` (NVIDIA, `count: all`)
  - `open-webui` build args:
    - `USE_CUDA=true`
    - `USE_CUDA_VER=cu121`

- Assumes **NVIDIA GPU is present** on the host:
  - NVIDIA drivers installed on the host
  - container runtime with GPU support (e.g. Docker + nvidia-container-toolkit)
- CPU-only deployment is **not** considered in this configuration and would require explicit changes to `docker-compose.yaml` and potentially image parameters.

### Data
- Persistent application data is mounted to `/app/backend/data` (see `volumes` section in `docker-compose.yaml`).

