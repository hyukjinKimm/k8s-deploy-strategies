# k8s-deploy-strategies

# Kubernetes 배포 전략
Argo Rollouts, Istio 및 Kubernetes 네이티브 리소스를 사용하여 **BlueGreen**, **Canary**, **A/B 테스트** 배포 전략을 구현.

## 디렉토리 구조

```bash
.
├── ab
│   ├── istio-gateway.yaml        # A/B 테스트를 위한 Istio Gateway 구성
│   ├── istio-virtualsvc.yaml     # A/B 테스트를 위한 Istio VirtualService 구성
│   ├── rollout.yaml              # A/B 테스트를 위한 Argo Rollout 구성
│   └── service.yaml              # A/B 테스트를 위한 Kubernetes Service 구성
├── bluegreen
│   ├── new-app-ingress-preview.yaml  # BlueGreen 배포 미리보기용 Ingress
│   ├── new-app-ingress.yaml         # BlueGreen 배포 활성화용 Ingress
│   ├── new-app-service.yaml         # BlueGreen 배포용 Service 구성
│   └── rollout.yaml                # BlueGreen 배포를 위한 Argo Rollout 구성
├── canary
│   ├── ingress.yaml              # Canary 배포를 위한 Ingress 구성
│   ├── rollout.yaml              # Canary 배포를 위한 Argo Rollout 구성
│   └── service.yaml              # Canary 배포를 위한 Service 구성
├── configure
│   ├── argo-rollouts-rolebinding.yaml  # Argo Rollouts를 위한 RBAC RoleBinding
│   └── argo-rollouts-role.yaml        # Argo Rollouts를 위한 RBAC Role
└── README.md
