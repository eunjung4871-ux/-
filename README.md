# 쿠지요시 몬뽈확인

닉네임으로 포인트를 조회하고, 관리자가 직접 포인트를 등록·수정·삭제할 수 있는 사이트입니다.
Next.js(App Router) + TypeScript + Tailwind CSS + Supabase로 만들었습니다.

## 페이지 구성

- `/` — 공개 조회 화면 (닉네임 검색)
- `/admin/login` — 관리자 로그인
- `/admin` — 관리자 화면 (로그인해야 접근 가능, 포인트 추가/수정/삭제)

## 1. 로컬 실행 방법

```bash
npm install
cp .env.example .env.local   # 아래 2번에서 만든 값으로 채우기
npm run dev
```

`http://localhost:3000` 에서 확인할 수 있습니다.

## 2. Supabase 설정 방법

1. https://supabase.com 에서 새 프로젝트를 만듭니다.
2. 프로젝트의 **Settings > API** 메뉴에서 `Project URL`과 `anon public` 키를 복사해
   `.env.local`에 아래처럼 채웁니다.

   ```
   NEXT_PUBLIC_SUPABASE_URL=https://xxxxx.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOi...
   ```

3. **SQL Editor** 메뉴에서 `supabase/schema.sql` 파일 내용을 그대로 붙여넣고 실행합니다.
   - `points` 테이블이 생성되고, 예시 데이터 5명이 들어갑니다.
   - RLS(행 단위 보안) 정책도 함께 설정됩니다: 조회는 누구나 가능, 등록/수정/삭제는 로그인한
     관리자만 가능합니다.

## 3. 관리자 계정 만들기

1. Supabase 대시보드 **Authentication > Users** 메뉴로 이동합니다.
2. **Add user**를 눌러 관리자용 이메일/비밀번호를 직접 만듭니다. (별도 회원가입 화면은
   없고, 관리자만 이 계정으로 `/admin/login`에서 로그인합니다.)
3. 이메일 확인이 필요하다면 Supabase의 "Auto Confirm User" 옵션을 켜거나, 확인 메일의
   링크를 눌러주세요.

## 4. Vercel 배포 방법

1. 이 프로젝트를 GitHub 저장소에 올립니다.
2. https://vercel.com 에서 New Project로 해당 저장소를 가져옵니다.
3. **Environment Variables**에 `.env.local`과 같은 값(`NEXT_PUBLIC_SUPABASE_URL`,
   `NEXT_PUBLIC_SUPABASE_ANON_KEY`)을 등록합니다.
4. Deploy를 누르면 끝입니다.

## 5. 알아두면 좋은 점

- 지금 구조는 **관리자 1명(또는 여러 명이어도 모두 동일 권한)**을 가정합니다. 관리자마다
  권한을 다르게 주고 싶다면 `profiles` 테이블을 추가해 역할(role)을 구분하고, RLS 정책에서
  그 역할을 확인하도록 확장하면 됩니다.
- 닉네임은 `points` 테이블에서 **unique**로 설정되어 있어 같은 닉네임을 두 번 등록할 수
  없습니다.
- 닉네임 검색은 대소문자를 구분하지 않는 정확히 일치 검색입니다(`ilike`).

## 폴더 구조

```
app/
  page.tsx                 # 공개 조회 화면
  admin/
    login/page.tsx         # 관리자 로그인
    page.tsx               # 관리자 화면 (서버 컴포넌트)
    AdminTable.tsx          # 관리자 CRUD 표 (클라이언트 컴포넌트)
    LogoutButton.tsx
  layout.tsx
  globals.css
components/
  CuteBackground.tsx        # 배경 몬스터 일러스트
  MonsterSvgs.tsx
  CapsuleCard.tsx           # 조회 결과 카드
  SearchForm.tsx
lib/supabase/
  client.ts                 # 브라우저용 Supabase 클라이언트
  server.ts                 # 서버 컴포넌트용 Supabase 클라이언트
middleware.ts                # 세션 갱신 + /admin 보호
supabase/schema.sql          # 테이블 + RLS 정책 SQL
```
