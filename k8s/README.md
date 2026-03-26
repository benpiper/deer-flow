# DeerFlow Kubernetes Deployment

## Prerequisites

1. A Kubernetes cluster with `kubectl` configured
2. Container images built and available (local registry or remote)
3. API keys for your LLM providers

## Build & Push Images

Build the three application images from the project root:

```bash
# Build images
docker build -f backend/Dockerfile -t your-registry/deer-flow-gateway:latest .
docker build -f backend/Dockerfile -t your-registry/deer-flow-langgraph:latest .
docker build -f frontend/Dockerfile --target prod -t your-registry/deer-flow-frontend:latest .

# Push to your registry
docker push your-registry/deer-flow-gateway:latest
docker push your-registry/deer-flow-langgraph:latest
docker push your-registry/deer-flow-frontend:latest
```

Then update the `image:` fields in `frontend.yaml`, `gateway.yaml`, and `langgraph.yaml`.

## Configure Secrets

Edit `secrets.yaml` with your real API keys before deploying:

```bash
# Generate a BETTER_AUTH_SECRET
openssl rand -hex 32
```

## Deploy

```bash
kubectl apply -k k8s/
```

## Access

The nginx service is exposed as a `LoadBalancer` on port 2026.
Find the external IP:

```bash
kubectl -n deer-flow get svc nginx
```

Then access: `http://<EXTERNAL-IP>:2026`

## Customization

- **NodePort instead of LoadBalancer:** Edit `ingress.yaml`, change `type: LoadBalancer` to `type: NodePort`
- **Ingress with domain:** Uncomment the Ingress resource in `ingress.yaml`
- **Ollama access:** Ensure your cluster can reach `192.168.88.86:11434` (update the model `base_url` in `configmap.yaml` if needed)
