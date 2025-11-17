# GitOps Lab - Vector + ClickHouse

로컬 Kubernetes 환경에서 Vector와 ClickHouse를 사용한 로그 수집 파이프라인 실습

## 아키텍처

```
[Kubernetes Pods]
    ↓ (로그 파일)
[Vector Agent DaemonSet] - 각 노드에서 로그 수집
    ↓ (Vector protocol)
[Vector Aggregator] - 중앙 집중 처리 및 변환
    ↓ (HTTP)
[ClickHouse] - 로그 저장 및 분석
```

## 구조

```
.
├── apps/
│   ├── clickhouse/        # ClickHouse 배포
│   │   ├── namespace.yaml
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── init-job.yaml
│   └── vector/            # Vector 배포
│       ├── namespace.yaml
│       ├── agent/         # DaemonSet (각 노드)
│       └── aggregator/    # Deployment (중앙)
└── argocd/
    └── applications/      # ArgoCD Application 정의
```

## 배포 방법

1. ArgoCD에서 Application 생성:
```bash
kubectl apply -f argocd/applications/
```

2. 동기화 확인:
```bash
kubectl get applications -n argocd
```

## 운영 실습

1. 매니페스트 수정
2. Git commit & push
3. ArgoCD 자동 동기화 확인
4. 로그 확인

## ClickHouse 접속

```bash
kubectl exec -it -n clickhouse deployment/clickhouse -- clickhouse-client -u admin --password admin123
```

쿼리 예시:
```sql
SELECT * FROM logs.vector_logs ORDER BY timestamp DESC LIMIT 10;
```
