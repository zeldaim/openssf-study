# LFD121 4장 — 최소 권한 (Least Privilege)

2026-11-17 · 적용한 곳: [multicloud-dr-petclinic](https://github.com/zeldaim/multicloud-dr-petclinic)

사용자, 프로세스, 서버는 자기 일에 필요한 권한만 가져야 한다. 그래야 뚫렸을 때 피해가 그 권한 안에서 멈춘다.

## 개념

- 권한은 "혹시 몰라서" 주는 게 아니라, 하는 일 목록에서 거꾸로 정한다.
- 자주 하는 일과 가끔 하는 위험한 일은 권한을 나눈다. 위험한 권한은 필요할 때만 잠깐 빌린다.
- 사고를 막는다기보다, 사고가 났을 때 피해 범위를 줄이는 원칙이다. 서버를 빼앗겨도 공격자는 그 서버가 가진 권한만큼만 할 수 있다.

## 원래 상태

| 프로젝트 | 위치 | 문제 |
|---|---|---|
| multicloud-dr-petclinic | [`infra/aws/environments/dev/main.tf` 87~89행](https://github.com/zeldaim/multicloud-dr-petclinic/blob/main/infra/aws/environments/dev/main.tf#L87-L89) | 관리 서버(EC2)에 `AdministratorAccess` — 모든 동작, 모든 리소스 |

코드 주석에 이유가 적혀 있었다. "EKS 클러스터 생성 및 관리를 위해 필요." 클러스터를 가끔 만들기 위해 관리자 권한을 항상 쥐고 있던 셈이다.

## 적용

서버가 실제로 하는 일을 먼저 적고, 거기에 맞춰 권한을 두 역할로 나눴다.

```
전:  관리 서버 ──(항상)──> AdministratorAccess  (계정 전체)

후:  관리 서버 ──(항상)──> 평소 역할   SSM /petclinic/* 읽기, 서브넷·클러스터 조회
                              │
                              └─(필요할 때만, 1시간)──> 배포 역할   eksctl 공식 최소 정책
```

| 하는 일 | 근거 | 권한 | 역할 |
|---|---|---|---|
| RDS 접속 정보 읽기 | `user_data_mgmt.sh` 125·126행 | `ssm:GetParameter` (`/petclinic/*` 만) | 평소 |
| 서브넷 조회 | 같은 파일 204행 | `ec2:DescribeSubnets` | 평소 |
| kubectl 접속 준비 | eksctl, kubectl 설치 | `eks:DescribeCluster` (`cluster/petclinic*` 만) | 평소 |
| 클러스터 만들기 | eksctl | EC2, CloudFormation, EKS, IAM(`eksctl-*` 만) | 배포 |

기존 코드는 건드리지 않고 별도 폴더에 대체안으로 만들었다: [`least-privilege/`](https://github.com/zeldaim/multicloud-dr-petclinic/tree/main/least-privilege)

## 검증

| 방법 | 결과 | 원본 |
|---|---|---|
| `terraform test` + mock (AWS 계정 없이) | 4/4 통과 | [test.txt](https://github.com/zeldaim/multicloud-dr-petclinic/blob/main/least-privilege/results/test.txt) |
| 일부러 틀린 값으로 다시 실행 | 2건 모두 의도대로 실패 | [SSM 전체 허용](https://github.com/zeldaim/multicloud-dr-petclinic/blob/main/least-privilege/results/test_negative_ssm_all.txt), [임시 키 12시간](https://github.com/zeldaim/multicloud-dr-petclinic/blob/main/least-privilege/results/test_negative_session.txt) |
| 권한 이름을 AWS 공식 목록과 대조하는 스크립트 | 동작 43개, ERROR 0 | [verify_actions.md](https://github.com/zeldaim/multicloud-dr-petclinic/blob/main/least-privilege/results/verify_actions.md) |
| Checkov 3.4.5 | `CKV_AWS_274`(AdministratorAccess 사용) 전: 실패 → 후: 통과 | [results/](https://github.com/zeldaim/multicloud-dr-petclinic/tree/main/least-privilege/results) |

검증하면서 내 실수도 하나 나왔다. 처음에는 `eks:DescribeCluster` 를 "조회 권한이라 대상을 지정할 수 없다"고 생각해서 `*` 로 뒀는데, 대조 스크립트가 cluster 리소스로 좁힐 수 있다고 알려 줘서 고쳤다. 정책은 쓰고 나서 기계로 한 번 더 대조해야 한다는 걸 배웠다.

## 전후 비교

| 항목 | 전 | 후 |
|---|---|---|
| 서버가 항상 가진 권한 | 전부 (`*` / `*`) | SSM 읽기, 조회, 역할 전환 |
| 클러스터 생성 권한 | 항상 | 필요할 때만, 1시간 |
| IAM 쓰기 권한 | `iam:*` | 배포 역할에서만, `eksctl-*` 리소스로 한정 |
| 서버를 빼앗기면 | 계정 전체 | `/petclinic` 설정값 읽기 정도 |

## 한계와 다음

- 정책 문서만 검증했다. 이 권한으로 스크립트와 eksctl 이 실제로 동작하는지는 AWS 계정에서 확인해야 한다.
- 배포 역할의 `AmazonEC2FullAccess` 는 아직 넓다. 실제 계정에서 CloudTrail 과 IAM Access Analyzer 로 실제로 쓴 권한만 남기는 게 다음 단계다.
- 같은 레포의 ESO 정책(`Resource = "*"`, 모든 비밀값 읽기)은 아직 그대로다.

## 같은 장의 다른 원칙 — 적용해 볼 곳

| 원칙 | 내 말로 | 후보 | 상태 |
|---|---|---|---|
| 최소 권한 | 필요한 권한만 | 관리 서버 IAM (이 글) | 완료 |
| 권한 분리 | 위험한 일은 두 단계를 거치게 | 평소 → 배포 역할 전환 | 일부 |
| 안전한 기본값 | 기본은 막고 필요한 것만 연다 | DR Terraform 의 SSH 기본값 `0.0.0.0/0` | 후보 |
| 완전한 중재 | 요청마다 권한을 확인 | API 보안 프로젝트의 BOLA | 후보 |
| 보호 · 탐지 · 대응 | 막는 것만으로는 부족 | Wazuh 탐지 → Ansible 자동 차단 | 후보 |
