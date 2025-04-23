# k8s-deploy-strategies

# Kubernetes 배포 전략
Argo Rollouts, Istio 및 K8s 네이티브 리소스를 사용하여 **BlueGreen**, **Canary**, **A/B 테스트** 배포 전략을 구현.

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

```
## 설치

### 1. Argo Rollouts 설치

먼저 `argo-rollouts` 네임스페이스를 생성한 후, Argo Rollouts를 설치.

```bash
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
kubectl get all -n argo-rollouts
```

Argo Rollouts가 정상적으로 설치되었는지 확인하려면, 다음 명령어로 모든 리소스를 확인
```bash
kubectl get all -n argo-rollouts
```
그 후, kubectl-argo-rollouts CLI를 다운로드하고 시스템에 설치
```bash
curl -sLO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
chmod +x kubectl-argo-rollouts-linux-amd64
mv kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
kubectl-argo-rollouts version
```
### 2. Istio 설치

Istio를 설치

```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo -y
```
설치가 완료된 후, Istio CRDs가 제대로 설치되었는지 확인
```bash
kubectl get crds | grep istio
```
기본 네임스페이스에 Istio 자동 주입을 활성화
```bash
kubectl label namespace default istio-injection=enabled
```
## 명령어

### 1. Blue-Green
```bash
# 이미지 업데이트
kubectl argo rollouts set image new-app new-app=registry.hyukjin.io:5000/nginx:v20250422123952
# 트래픽 전환
kubectl argo rollouts promote new-app
# 롤백
kubectl argo rollouts undo new-app
# 특정 리비전으로 롤백
kubectl argo rollouts undo new-app --to-revision=3
# 상태 확인
kubectl argo rollouts get rollout new-app
```

### 2. Canary
```bash
# 이미지 업데이트
kubectl argo rollouts set image my-app my-app=registry.hyukjin.io:5000/nginx:v20250423091446
# 상태 확인
kubectl argo rollouts get rollout my-app
# 롤백
kubectl argo rollouts undo my-app --to-revision=1
```

### 3. A/B 전략
```bash
# Istio Ingress Gateway가 80 포트를 NodePort (예: :32502)로 열고 있을 경우
curl -H "Cookie: abtest=true" http://ab-test.example.com:32502/
→ Preview 버전 접근
curl http://ab-test.example.com:32502/
→ Active 버전 접근
```
