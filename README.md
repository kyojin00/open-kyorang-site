# open.kyorang.com 사이트

교랑톡 채팅방 초대 링크용 정적 사이트.

## 구조

```
open-kyorang-site/
├── index.html                    # 도메인 루트 페이지
├── join/
│   └── index.html                # /join/CODE 랜딩 페이지
├── .well-known/
│   └── assetlinks.json           # Android App Link 검증 파일 ⭐ 필수
└── vercel.json                   # Vercel 라우팅/헤더 설정
```

## 배포 (Vercel)

### 1단계: GitHub 저장소 만들기

```bash
cd open-kyorang-site
git init
git add .
git commit -m "Initial commit"

# GitHub에서 새 repo 만든 후
git remote add origin https://github.com/USERNAME/open-kyorang-site.git
git push -u origin main
```

### 2단계: Vercel에 연결

1. https://vercel.com 로그인
2. **Add New** → **Project** → 위 GitHub repo 선택
3. **Framework Preset**: **Other** 또는 **Static**
4. **Build Command**: 비워두기
5. **Output Directory**: 비워두기 (루트 그대로 배포)
6. **Deploy** 클릭

### 3단계: 커스텀 도메인 연결

1. Vercel 프로젝트 → **Settings** → **Domains**
2. `open.kyorang.com` 입력 → **Add**
3. Vercel이 보여주는 DNS 레코드를 **kyorang.com 도메인 관리자**에 추가
   - 보통 CNAME: `open` → `cname.vercel-dns.com`
4. 몇 분~몇 시간 후 SSL 자동 발급 완료

### 4단계: 검증

배포 후 다음 URL이 모두 200으로 응답하는지 확인:

- https://open.kyorang.com → 메인 페이지
- https://open.kyorang.com/join/test12345 → 초대 랜딩 페이지
- https://open.kyorang.com/.well-known/assetlinks.json → JSON 응답 (Content-Type: application/json)

## Android App Link 검증

`assetlinks.json`을 호스팅하면, 앱 설치 후 Android OS가 자동으로 검증해요.

검증 상태 확인 (앱 설치된 폰에서):

```bash
adb shell pm get-app-links com.kyorang.kyorang_talk
```

출력에 `verified` 가 떠야 카톡에서 https 링크 눌렀을 때 자동으로 앱이 열려요.

만약 `legacy_failure` 또는 `not_verified`면 `assetlinks.json`이 안 보이거나 SHA-256 불일치인 거예요.

직접 검증 도구도 있음:
- https://developers.google.com/digital-asset-links/tools/generator

## 다른 호스팅 옵션

### Cloudflare Pages
- vercel.json 대신 `_redirects` 파일 만들기:
  ```
  /join/:code  /join/index.html  200
  ```

### Firebase Hosting
- `firebase.json`에 rewrite 추가:
  ```json
  {
    "hosting": {
      "rewrites": [
        { "source": "/join/**", "destination": "/join/index.html" }
      ]
    }
  }
  ```

## 향후 작업 (Phase 2 완성 후)

1. Play Store 출시 후 → release 키 SHA-256도 `assetlinks.json`에 추가
2. iOS 추가 시 → `apple-app-site-association` 파일 추가
3. 랜딩 페이지에 채팅방 정보 미리보기 추가 (방 이름, 카테고리 등 — 공개 API 필요)
