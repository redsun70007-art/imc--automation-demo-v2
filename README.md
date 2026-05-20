# IMC 캠페인 대시보드 (Private Template)

5회차 Part 2 — Cloudflare Pages 비공개 배포 시연용 template.

## 구성

| 파일 | 역할 |
|---|---|
| `index.html` | 대시보드 (KPI 카드 + 채널별 캘린더 + 매출 테이블) |
| `data.json` | 시연용 더미 데이터 (32행, 9컬럼) |
| `fetch_sheets.py` | 시트 → data.json 변환 (4회차 코드 그대로) |
| `requirements.txt` | Python 의존성 |
| `.github/workflows/sync.yml` | 매일 09:00 KST 자동 sync |

## 데이터 구조 (data.json)

9컬럼:
- 기획전: `BRAND`, `CHANNEL`, `PROMO_NAME`, `START_DATE`, `END_DATE`, `STATUS`
- 매출(더미): `SALES`, `TARGET`, `ACHIEVE`

⚠️ 매출 값은 전부 더미. 실제 매출 데이터 X. 외부 노출 시에도 보안 이슈 없음.

## 셋업 흐름 (튜티 본인 작업)

```
1. 본인 GitHub에 새 private repo 생성 (예: imc-private-dashboard)
2. 이 폴더의 모든 파일을 본인 repo에 commit + push
3. 본인 GitHub repo → Settings → Secrets and variables → Actions
   - GOOGLE_JSON_KEY: 1회차에 받은 서비스계정 JSON
   - SHEET_ID: 본인 시연용 시트 ID
4. (선택) Actions 탭 → sync 워크플로 → Run workflow → data.json 자동 갱신 확인
5. Cloudflare Pages 프로젝트 생성:
   - dash.cloudflare.com → Workers & Pages → Pages → Connect to Git
   - 본인 private repo 선택
   - Build command: (비움)
   - Build output: /
   - Deploy → <프로젝트명>.pages.dev URL 발급
6. Cloudflare Access 비공개 잠그기:
   - Zero Trust → Applications → Add → Self-hosted
   - Domain: <프로젝트명>.pages.dev
   - Policy: Allow / Emails ending in @fnfcorp.com
   - Identity: One-time PIN (이메일 OTP)
7. 시크릿 창에서 URL 열기 → 이메일 OTP → 진입 ✅
```

## 대시보드 기능

- **상단 필터**: 브랜드 / 채널 / 상태
- **KPI 카드 4개**: 총 매출, 총 캠페인, 진행 중, 평균 달성률
- **캘린더 뷰**: 채널별 row, 4월말~5월말 기간 띠 (브랜드 색상, 상태별 명도)
- **테이블**: 캠페인별 매출 진행률 바
