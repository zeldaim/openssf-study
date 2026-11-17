<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:FF6B6B,25:FFD93D,50:6BCB77,75:4D96FF,100:9B5DE5&height=170&section=header&text=OpenSSF%20Study&fontColor=ffffff&fontSize=50&fontAlignY=38&desc=concept%20%E2%86%92%20my%20project%20%E2%86%92%20verified%20before%20%2F%20after&descSize=16&descAlignY=60" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/OpenSSF-free%20courses-6BCB77?style=flat-square" />
  <img src="https://img.shields.io/badge/method-apply%20%2B%20verify-4D96FF?style=flat-square" />
  <img src="https://img.shields.io/badge/notes-in%20my%20own%20words-FFB703?style=flat-square" />
</p>

OpenSSF 무료 과정을 들으며 **개념을 내 말로 정리하고, 내 프로젝트에 실제로 적용하고, 전후를 검증해 비교**한 기록이다.
강의 요약만 쌓지 않는다 — 각 글에는 **바꾼 코드 링크와 검증 결과**가 있다.

## 🔁 방법

```mermaid
flowchart LR
    A["① 개념<br/>내 말로 3줄"] --> B["② 내 프로젝트<br/>원래 상태 (파일:줄)"]
    B --> C["③ 적용<br/>코드 변경"]
    C --> D["④ 검증<br/>시험 · 도구"]
    D --> E["⑤ 전후 비교"]
    E --> F["⑥ 한계 · 다음"]
    style A fill:#FF6B6B,color:#fff
    style B fill:#FFB703,color:#000
    style C fill:#6BCB77,color:#fff
    style D fill:#4D96FF,color:#fff
    style E fill:#9B5DE5,color:#fff
    style F fill:#8B949E,color:#fff
```

새 글은 [`TEMPLATE.md`](TEMPLATE.md) 형식으로 쓴다.

## 📊 진행 현황

| 과정 | 장 | 개념 | 적용한 프로젝트 | 상태 |
|---|---|---|---|---|
| **LFD121** Developing Secure Software | Ch.4 | [최소 권한](LFD121/ch04-least-privilege.md) | multicloud-dr-petclinic — 관리 서버 IAM | ![](https://img.shields.io/badge/-applied%20%26%20verified-2DA44E?style=flat-square) |
| | Ch.5 · next | 외부 소프트웨어를 안전하게 쓰기 | API 보안 · 공급망 (SBOM · VEX) | ![](https://img.shields.io/badge/-studying-FFB703?style=flat-square) |

## 🏁 지금까지 전후 비교 한눈에

| 개념 | 전 | 후 | 근거 |
|---|---|---|---|
| 최소 권한 | ![](https://img.shields.io/badge/-AdministratorAccess-E5534B?style=flat-square) | ![](https://img.shields.io/badge/-%ED%8F%89%EC%86%8C%2F%EB%B0%B0%ED%8F%AC%20%EC%97%AD%ED%95%A0%20%EB%B6%84%EB%A6%AC%20%C2%B7%201%EC%8B%9C%EA%B0%84-2DA44E?style=flat-square) | test 4/4 · 검증기 ERROR 0 · Checkov `CKV_AWS_274` FAILED→PASSED |

## 📚 과정 출처

- [OpenSSF Training](https://openssf.org/training/courses/) — Linux Foundation 무료 과정. 글은 강의 원문이 아니라 **내 말로 쓴 정리**다.
