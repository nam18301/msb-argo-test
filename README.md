# msb-argo-test

Demo GitOps app dùng để test RHACM Application (Subscription) và
OpenShift GitOps (ArgoCD) trên cluster1.

## Cấu trúc

```
msb-argo-test/
├── base/                  # Manifest gốc, dùng chung
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── route.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dc/                # Override cho site DC (replicas: 3)
    │   └── kustomization.yaml
    └── dr/                # Override cho site DR (replicas: 2)
        └── kustomization.yaml
```

## Đặc điểm tuân thủ golden-config đã áp trên cluster1

- `resources.requests`/`limits` đã khai báo — không bị chặn bởi
  `require-resource-requests`.
- `securityContext.runAsNonRoot: true` — không bị chặn bởi
  `deny-root-containers`.
- `replicas` luôn >= 2 — không bị chặn bởi `require-min-2-replicas`.
- Image dùng tag cố định (`1-233`), không dùng `:latest` — không bị
  chặn bởi `no-latest-tag`.

## Build thử bằng kustomize (không cần cluster)

```bash
kubectl kustomize overlays/dc
kubectl kustomize overlays/dr
```

## Apply trực tiếp lên cluster1 (test nhanh, không qua GitOps)

```bash
oc apply -k overlays/dc
```
