## 실제 IDC 물리적 서버 AI/ML Kubernetes 환경 구축

### * 구축 도구 *
#### 클러스터 토대 및 네트워크 (Foundation & Network) 도구: Bastion Host, Master, Worker 설정, MetalLB
#### 스토리지 및 모니터링 (Storage & Observability) 도구: CSI 구성 (Local Path Provisioner), MinIO, 모니터링 레이어 (Prometheus + Grafana):
#### MLOps 파이프라인 및 모델 관리 (MLOps Core) 도구: MLOps 플랫폼 (Kubeflow) 배포, MLflow, 엔터프라이즈 MLOps 서빙 (KServe, Seldon Core)
#### DevSecOps 및 배포 자동화 (Security & GitOps) 도구: Trivy & Kyverno, ArgoCD
#### 대규모 LLM 최적화 (Advanced AI Optimization) 도구: Ray & vLLM, Fluid & Alluxio

### * ESXi 가상환경 구축 참고사항 *
#### 1. 제한적으로 테스트 가능한 영역 (Workaround 필요)
#### -> 실제 대규모 데이터 연산이 들어가는 부분은 기능의 ‘흐름’만 검증하는 방식으로 우회해야 합니다.
#### -> GPU 기반 모델 학습 및 스케줄링
#### -> ESXi 호스트에 실제 물리 GPU가 꽂혀 있다면, PCIe Passthrough 기능을 이용해 특정 VM에 통째로 할당하여 nvidia-device-plugin과 드라이버 설치를 테스트해 볼 수 있습니다. (단, vGPU로 쪼개는 것은 엔터프라이즈 라이선스가 필요합니다.)
#### -> GPU가 전혀 없다면, Kubeflow Pipelines(KFP)에서 모델을 학습시킬 때 CPU 기반 학습으로 설정하거나 아주 가벼운 샘플 데이터만 태워, "데이터 전처리 → 파드 생성(TFJob/PyTorchJob) → 모델 저장"이라는 파이프라인의 파드 오케스트레이션 흐름 자체를 검증하는 데 집중할 수 있습니다.
#### 2. ESXi VM 환경에서 테스트 불가능한 영역
#### -> 물리 서버(Baremetal) OOB 관리 및 펌웨어 제어
#### -> 예를 들어, 실제 물리 환경에서는 자동화 스크립트를 통해 Dell R620은 iDRAC7, R630은 iDRAC8, R640은 iDRAC9 버전에 맞춰 펌웨어를 제어하고 RAID를 묶는 작업이 필요하지만, 가상화된 ESXi 환경 내부에서는 이러한 OOB(Out-of-Band) 관리 인터페이스 통신을 테스트할 수 없습니다.
#### -> 고성능 스토리지 및 네트워크 I/O 성능 테스트
#### -> NVMe-oF 브릿지 구성이나 InfiniBand 기반의 RDMA 네트워크 테스트는 물리적인 하드웨어 스위치와 NIC가 필요하므로 가상 환경에서는 구성이 어렵습니다.
