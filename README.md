# openssf-study

OpenSSF 무료 과정을 들으면서 정리하는 노트입니다.

강의 내용을 요약만 하면 금방 잊어버려서, 장마다 개념 하나를 골라 **제 프로젝트에 직접 적용해 보고, 바뀌기 전과 후를 비교**하는 방식으로 쓰고 있습니다. 글은 강의 원문이 아니라 제 말로 정리한 것입니다.

## 정리 방식

각 글은 같은 순서로 씁니다. ([TEMPLATE.md](TEMPLATE.md))

1. 개념 — 제 말로 몇 줄
2. 제 프로젝트의 원래 상태 — 파일과 줄 번호까지
3. 적용 — 무엇을 어떻게 바꿨는지
4. 검증 — 시험이나 도구로 확인한 결과
5. 전후 비교
6. 한계와 다음에 할 것

## 목록

| 과정 | 장 | 주제 | 적용한 곳 | 상태 |
|---|---|---|---|---|
| LFD121 Developing Secure Software | 4장 | [최소 권한](LFD121/ch04-least-privilege.md) | multicloud-dr-petclinic 관리 서버 IAM | 적용, 검증 |
| | 5장 | 외부 소프트웨어를 안전하게 쓰기 | API 보안 · 공급망 프로젝트 (SBOM, VEX) | 공부 중 |

## 지금까지 바뀐 것

| 주제 | 전 | 후 | 확인 |
|---|---|---|---|
| 최소 권한 | 관리 서버에 `AdministratorAccess` | 평소 역할 / 배포 역할로 분리, 배포 권한은 1시간 임시 키 | terraform test 4/4, 검증 스크립트 ERROR 0, Checkov `CKV_AWS_274` 통과 |

## 출처

- [OpenSSF Training](https://openssf.org/training/courses/) (Linux Foundation 무료 과정)
