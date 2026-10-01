# policy.gformance.com

GFORMANCE 서비스의 약관·정책 문서를 게시하는 GitHub Pages 사이트입니다.

- 사이트: https://policy.gformance.com
- 이 저장소에는 **게시용 결과물(HTML·CSS·JSON)만** 있습니다.
- 내용은 앱 저장소(`gformance_ui`)의 `lib/legal/*.dart` 에서 수정하고, `tool/publish_policy.dart` 로 이 폴더에 반영합니다.
- 원칙: **정식 게시한 버전은 수정하지 않고, 개정은 새 버전으로 추가합니다.**

## 문서

| 문서 | 주소 | 분류 |
|---|---|---|
| 서비스 이용약관 | `/terms/` | 약관 (필수 동의) |
| 개인정보처리방침 | `/privacy/` | 방침 (동의 대상 아님, 상시 공개) |
| 개인정보 수집·이용 동의 | `/consents/privacy/` | 동의서 (필수) |
| 마케팅 정보 수신 동의 | `/consents/marketing/` | 동의서 (선택) |

## 구조

```text
index.html                  첫 화면 (문서 목록)
404.html
style.css
manifest.json               앱이 읽는 목록: 문서별 현재 버전, 시행 예정 버전, 버전별 SHA-256
terms/
  index.html                현재 시행 버전
  current.json              현재 시행 버전 원문
  0.1.json                  버전별 원문 (정식 게시 후 수정 금지)
  0.1/index.html            버전별 고정 페이지
  history/index.html        전체 버전 및 변경 이력
  diff/1.0...1.1/           개정 전후 비교 (개정 시 생성)
privacy/                    (같은 구조)
consents/privacy/
consents/marketing/
CNAME                       policy.gformance.com
```

## 버전 규칙

| 단계 | 버전 | 비고 |
|---|---|---|
| 임시 약관 (정식 오픈 전) | `0.1`, `0.2` … | 페이지 상단에 임시 약관 안내 표시, 수정 가능 |
| 정식 오픈 | `1.0` | 시행일과 함께 첫 정식 게시. 이후 이 파일은 수정하지 않음 |
| 개정 | `1.1`, `1.2` … | 새 버전 파일로 추가 |

정식 버전이 게시되면 임시 약관(0.x)은 사이트 페이지에서 빠지고, 원문 파일만 기록으로 남습니다.
현재 모든 문서는 **임시 약관 0.1** 입니다.

## 내용 수정 방법

앱 저장소에서:

```bash
cd ../gformance_ui
```

```bash
dart run tool/publish_policy.dart
```

```bash
dart run tool/policy_site/serve.dart
```

http://localhost:8080 에서 확인한 뒤 이 저장소에서 commit / push 하면 GitHub Pages 에 반영됩니다.

- 정식 오픈: 앱의 `LegalInfo.draft = false`, 네 문서의 `version` 을 `'1.0'` 으로, 시행일 입력 후
  `dart run tool/publish_policy.dart --effective 2026-11-01 --announced 2026-10-25`
- 개정: 앱에서 문서 `version` 을 올린 뒤
  `dart run tool/publish_policy.dart --effective 2026-12-31 --announced 2026-12-01 --summary "terms=변경 내용"`
  (회원에게 불리하거나 중대한 변경이면 `--material`, 시행 30일 전 공지)
- 개정 버전의 시행일이 되면 한 번 더 실행해서 commit / push 하면 현재 버전이 바뀝니다.

## GitHub 설정

1. Settings → Pages → Source: **GitHub Actions** (`.github/workflows/deploy.yml` 사용)
2. Settings → Pages → Custom domain: `policy.gformance.com` → 인증서 발급 후 **Enforce HTTPS**
3. Cloudflare DNS: `policy` CNAME → `runonio.github.io`, **DNS only (회색 구름)**
4. Settings → Rules → Rulesets → `main` 브랜치: 삭제 금지, force-push 금지 (변경 이력 보존)
