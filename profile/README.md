# OpenPath Robotics GitHub Organization

> 회사용 GitHub Organization을 안정적이고 일관되게 운영하기 위한 **레포 네이밍·브랜치·권한·문서화·자동화** 원칙을 정리했습니다. 필요시 회사 실정에 맞게 커스터마이즈하세요.

---

## 0) 빠른 시작 체크리스트

* [ ] Organization 2FA 필수화
> 조직의 모든 구성원이 GitHub 로그인 시, 비밀번호 외에 인증앱(예: Authy, Google Authenticator) 등을 이용해 2단계 인증(2FA) 을 반드시 거치도록 강제하는 설정입니다.
* [ ] 보호 브랜치(`main`) 정책 적용 & 필수 리뷰 1+ 설정
> main 브랜치를 실수로 덮어쓰거나 테스트 안 된 코드를 병합하지 못하도록 보호(Protected) 설정을 적용합니다.
* [ ] 저장소 네이밍 규칙 공지 및 준수 모니터링
> Organization 내 모든 리포지토리가 동일한 이름 규칙(예: <team>-<project>) 을 따르도록 규정하고, 이를 주기적으로 점검하는 것을 의미합니다.

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
