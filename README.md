# AI Tutor GitOps

Helm charts và production values cho hệ thống AI Tutor.

## Kiến trúc GitOps

```
AI-Tutor (source code)
  └─ GitHub Actions CI → Build images → Push ACR
       └─ Write-back imageTag → AI-Tutor-GitOps/charts/ai-tutor/values-prod.yaml

AI-Tutor-GitOps (config repo)  ← ArgoCD watches this
  └─ charts/ai-tutor/
       ├── templates/       # K8s manifests (Deployments, Services, NetworkPolicies, ...)
       ├── values.yaml      # Default values
       ├── values-prod.yaml # Production overrides (imageTag updated by CI bot)
       └── Chart.yaml
```

## Tại sao tách repo?

- **Tách biệt source code và config**: CI bot chỉ commit vào repo config, không làm ô nhiễm lịch sử commit của source code.
- **ArgoCD best practice**: ArgoCD khuyến nghị dùng repo riêng cho GitOps config.
- **Bảo mật**: Có thể phân quyền riêng cho config repo.

## ArgoCD

ArgoCD Application CRD trỏ tới repo này:
- **Source**: `https://github.com/Sang20225664/AI-Tutor-GitOps.git`
- **Path**: `charts/ai-tutor`
- **Values**: `values-prod.yaml`
- **Target Revision**: `main`
