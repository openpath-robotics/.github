# OpenPath Robotics GitHub Organization 운영 매뉴얼 (v1.0)

> 회사용 GitHub Organization을 안정적이고 일관되게 운영하기 위한 **레포 네이밍·브랜치·권한·문서화·자동화** 원칙을 정리했습니다. 필요시 회사 실정에 맞게 커스터마이즈하세요.

---

## 0) 빠른 시작 체크리스트

* [ ] Organization 2FA 필수화
* [ ] 팀(Teams) 생성 및 최소권한(Least Privilege) 적용
* [ ] 기본 템플릿 리포(`org-templates`) 생성
* [ ] 기본 이슈/PR 템플릿·라벨 세트·CODEOWNERS 배포
* [ ] 보호 브랜치(`main`) 정책 적용 & 필수 리뷰 1+ 설정
* [ ] GitHub Actions: Lint/Test/Build CI 파이프라인
* [ ] Dependabot & Secret Scanning 활성화
* [ ] 저장소 네이밍 규칙 공지 및 준수 모니터링

---

## 1) Repository 네이밍 규칙

**형식**

```
<team>-<project>[-<submodule>]
```

**가이드**

* **소문자 + 하이픈(`-`)** 고정 (대소문자 혼용 지양)
* **팀/도메인 접두사** 권장: `robotics-`, `platform-`, `apps-`, `docs-`, `design-`, `infra-`, `mkt-`
* **역할/목적**이 드러나게: `-control`, `-sim`, `-dashboard`, `-firmware`, `-website`, `-docs`
* **아카이브**는 `archive-` 접두사 사용

**예시**

* `robotics-canine-control`
* `robotics-canine-sim`
* `infra-ci-workflows`
* `mkt-website`
* `docs-internal-rnd`
* `archive-robotics-old-gaitlib`

---

## 2) 브랜치 전략

**권장: GitHub Flow (+ 보호 브랜치)**

* **`main`**: 항상 배포 가능한 안정 버전 (Protected)
* **`dev`**: 통합 테스트용(선택)
* **기능/수정 브랜치**: `feature/<name>`, `fix/<issue>`, `chore/<task>`, `refactor/<scope>`

**예시**

```
main
dev
feature/add-legged-kinematics
fix/torque-limit-boundary
refactor/mpc-loop
```

**운영 원칙**

* 모든 변경은 **PR 경유**, `main` 직접 푸시 금지
* **필수 리뷰어 ≥ 1**, 상태 체크(Lint/Test) 통과 필수
* **Release 태깅**: `vMAJOR.MINOR.PATCH` (예: `v1.2.0`)
* 긴 기능은 Draft PR로 조기 공유

---

## 3) 권한 및 접근 정책

| 역할         | 권한                | 용도                  |
| ---------- | ----------------- | ------------------- |
| **Owner**  | 전체 관리             | 1~2명 (CTO/엔지니어링 총괄) |
| **Admin**  | repo 설정·보호 브랜치 관리 | 팀 리드(도메인 책임자)       |
| **Write**  | push/PR 작성        | 정식 팀원               |
| **Triage** | 이슈/PR 정리, 라벨      | PM/리포 매니저           |
| **Read**   | 열람만               | 외부 협력/인턴            |

**원칙**

* **Team 단위**로 권한 부여 (개별 인원 권한 지양)
* 외부 협력사는 **Read/Triage**까지만
* Organization 레벨 **2FA Mandatory**
* 중요 리포는 내부 전용(Private) 기본, 공개는 승인 절차

---

## 4) 필수 문서 & 리포 구조

**필수 파일**

* `README.md` — 개요/설치/실행/담당자
* `CONTRIBUTING.md` — 브랜치·커밋·PR·코딩 규칙
* `CHANGELOG.md` — 버전별 변경 요약
* `LICENSE` — 내부/외부 배포 정책에 따름
* `.gitignore` — 빌드 산출물 제외
* `.github/` — 이슈/PR 템플릿, 워크플로, CODEOWNERS

**권장 구조**

```
repo-name/
 ├─ src/
 ├─ test/
 ├─ docs/
 ├─ .github/
 │   ├─ ISSUE_TEMPLATE/
 │   │   ├─ bug_report.md
 │   │   └─ feature_request.md
 │   ├─ PULL_REQUEST_TEMPLATE.md
 │   ├─ CODEOWNERS
 │   └─ workflows/
 │       └─ ci.yml
 ├─ README.md
 ├─ CONTRIBUTING.md
 ├─ CHANGELOG.md
 ├─ LICENSE
 └─ .gitignore
```

---

## 5) 커밋 & PR 규칙

**Conventional Commits**

```
feat: add leg inverse kinematics
fix: clamp torque limit in stance phase
docs: update README with build steps
refactor: simplify MPC cost function
test: add unit tests for foot planner
chore: bump dependencies
```

> 필요 시 **범위(scope)** 사용: `feat(control): ...`

**PR 원칙**

* 제목: `[feat|fix|refactor|docs] 핵심 요약`
* 본문: **배경/변경사항/테스트/리스크/롤백**
* Reviewer 1명 이상, 라벨 지정(`enhancement`, `bug`, `breaking`, `urgent`)
* 스크린샷/로그/성능 수치 첨부 권장

---

## 6) 이슈 라벨 세트 (추천)

* 타입: `bug`, `enhancement`, `docs`, `refactor`, `chore`, `test`
* 우선순위: `P0`, `P1`, `P2`
* 상태: `needs-info`, `blocked`, `in-review`
* 영향: `breaking-change`, `security`, `performance`

---

## 7) 자동화 (CI/CD) & 품질

**GitHub Actions**

* 이벤트: `pull_request`, `push` to `main|dev`
* 작업: Lint → Build → Test → (옵션) Package
* 캐시/매트릭스 전략으로 빌드 시간 단축

**예시: `.github/workflows/ci.yml`**

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [ main, dev ]

jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: pip install -r requirements.txt
      - run: python -m pytest -q
```

**Dependabot**

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
```

**CODEOWNERS**

```txt
# .github/CODEOWNERS
/src/robotics/   @hosunkang @robotics-lead
/docs/           @mkt-lead
```

---

## 8) 보호 브랜치 & 보안

**보호 브랜치 설정(`main`)**

* [x] **Require pull request reviews** (≥ 1)
* [x] **Require status checks to pass** (CI 성공 필수)
* [x] **Require conversation resolution** (리뷰 코멘트 해소)
* [x] **Restrict who can push** (관리자만 머지)
* [x] **Include administrators** (관리자 예외 금지 권장)

**보안 권장**

* Organization 보안: **2FA 필수**, SSO(가능시)
* **Secret Scanning/Push Protection** 활성화
* 환경변수/비밀키는 **Actions Encrypted Secrets** 사용
* 릴리즈 아티팩트에 라이선스/서드파티 고지 포함

---

## 9) 템플릿 모음

**이슈: 버그 리포트 (`.github/ISSUE_TEMPLATE/bug_report.md`)**

```md
---
name: Bug report
about: 문제를 보고합니다
labels: bug, P1
---

### 요약
- 무엇이, 언제, 어디서 발생했나요?

### 재현 절차
1. ...
2. ...
3. ...

### 기대 동작
- ...

### 실제 동작/로그/스크린샷
- ...

### 환경
- OS / Runtime / Version:
```

**이슈: 기능 요청 (`.github/ISSUE_TEMPLATE/feature_request.md`)**

```md
---
name: Feature request
about: 새로운 기능 제안
labels: enhancement, P2
---

### 배경/문제
- ...

### 제안 기능
- ...

### 수용 기준 (Acceptance Criteria)
- [ ] ...
- [ ] ...

### 영향/리스크
- ...
```

**PR 템플릿 (`.github/PULL_REQUEST_TEMPLATE.md`)**

```md
## 개요
- (왜) 어떤 문제/목표를 해결하나요?

## 변경사항
- 핵심 변경 요약

## 테스트
- [ ] Unit
- [ ] Integration
- 결과/스크린샷:

## 영향/리스크 & 롤백
- Breaking 여부:
- 롤백 방법:

## 기타
- 관련 이슈: #123
```

**CONTRIBUTING.md (요약 예)**

```md
# Contributing

## 브랜치
- main: 안정, 보호
- dev: 통합(선택)
- 작업: feature/*, fix/*

## 커밋 규칙
- Conventional Commits 사용

## PR
- 리뷰어 ≥ 1, CI 통과 필수
- 제목/본문 템플릿 준수

## 코드 스타일
- (예) Python: black/ruff, C++: clang-format

## 린트/테스트
- `make lint && make test`
```

**README.md (스켈레톤)**

````md
# Project Name

한 줄 소개.

## Features
- ...

## Quick Start
```bash
git clone ...
make setup
make run
````

## Docs

* /docs 참조

## Maintainers

* @owner1, @owner2

```

---

## 10) 저장소 라이프사이클 정책

**상태 태깅**
- `WIP` → `Active` → `Maintenance` → `Archived`

**아카이브 규칙**
- 90일 이상 커밋 없음·담당자 부재 시 검토
- `archive-<original-name>`로 **Read-only 전환**
- README 최상단에 **아카이브 사유/후속 경로** 명시

---

## 11) 릴리즈 & 변경관리

- 태깅: `vMAJOR.MINOR.PATCH`
- **CHANGELOG** 형식(권장):
  - `Added` / `Changed` / `Fixed` / `Removed` / `Security`
- 릴리즈 노트에 **마이그레이션 가이드/브레이킹 변경** 명시

---

## 12) 운영 팁

- 공용 **`org-templates` 리포** 만들어 템플릿 일괄 관리  
- 새 리포 생성 시, 템플릿 복제 + 보호 브랜치 규칙 즉시 적용  
- 분기마다 **리포 건강검진**: 이슈 적체·오래된 브랜치·보안 경고  
- 대형 바이너리는 Git LFS 고려, 가능하면 아티팩트 서버 분리

---

### 부록 A) 보호 브랜치 정책 예(체크리스트)
- [ ] Require pull request before merging
- [ ] Require reviews: 1+
- [ ] Dismiss stale approvals when new commits are pushed
- [ ] Require status checks: `lint`, `build`, `test`
- [ ] Require branches up to date before merging
- [ ] Include administrators
- [ ] Restrict who can push to matching branches

### 부록 B) 기본 라벨 세트 일괄 적용 스크립트(선택)
라벨 자동화는 GitHub CLI(gh) 또는 스크립트를 별도 리포에서 관리하세요.

---

필요하시면 위 매뉴얼을 **조직 템플릿 리포 구조**와 함께 압축 or PDF로도 만들어 드릴게요.
```
