아래 내용을 `README.md`에 그대로 붙여넣어줘요.

```md
# ING Factory Cost Calculator

정적 PWA 공장도착 원가 계산기입니다. 이 버전은 견적과 HS CODE 데이터를 localStorage가 아니라 Firebase Firestore 공용 DB에 저장하고, 모든 직원 브라우저에 실시간 동기화합니다.

## 주요 변경 사항

- 견적 저장/수정/삭제/조회: Firestore quotes 컬렉션 사용
- HS CODE 저장/수정/삭제/조회: Firestore hsCodes 컬렉션 사용
- 날짜별 조회, 전체 포함검색, 정확일치 검색 유지
- 기존 계산식, PDF 내보내기, 제품 단위 직접입력 유지
- 계약금/잔금 % 입력 시 총 공장도착가 기준 금액 자동 계산
- 필수 입력값 누락 시 견적 저장과 PDF 내보내기 차단
- 모바일 Safari/Chrome 클릭 안정성을 위해 버튼 type="button" 적용

## Firebase 설정

1. Firebase Console에서 프로젝트를 생성합니다.
2. 프로젝트에서 웹 앱을 추가합니다.
3. Firebase가 제공하는 웹 앱 설정값을 복사합니다.
4. index.html의 firebaseConfig 값을 실제 값으로 교체합니다.

```js
const firebaseConfig = {
  apiKey: '...',
  authDomain: '...',
  projectId: '...',
  storageBucket: '...',
  messagingSenderId: '...',
  appId: '...'
};
```

5. Firestore Database를 생성합니다.
6. 테스트 또는 사내 제한 환경에 맞게 보안 규칙을 설정합니다.

초기 테스트용 규칙 예시:

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /quotes/{quoteId} {
      allow read, write: if true;
    }
    match /hsCodes/{code} {
      allow read, write: if true;
    }
  }
}
```

운영 환경에서는 Firebase Authentication 또는 Netlify 접근 제한과 함께 직원만 읽고 쓸 수 있도록 규칙을 좁히는 것을 권장합니다.

## Firestore 데이터 구조

quotes/{quoteId}

- date: 견적일자
- customer: 거래처명
- title: 견적명
- memo: 비고
- costs: 수입비용 입력값
- payment: 계약금/잔금 조건
- items: 제품 목록
- summary: 계산 결과
- updatedAt, updatedAtServer: 수정 시각

hsCodes/{code}

- code: HS CODE
- itemName: 아이템명
- origin: 원산지/메모
- dutyRate: 관세율
- updatedAt, updatedAtServer: 수정 시각

## Netlify 배포

1. GitHub 저장소에 index.html, manifest.json, sw.js, icons, ing_logo.jpg를 포함합니다.
2. Netlify에서 Add new site → Import an existing project를 선택합니다.
3. 이 GitHub 저장소를 연결합니다.
4. 빌드 명령은 비워둡니다.
5. Publish directory는 저장소 루트로 설정합니다.
6. Deploy를 실행합니다.

정적 HTML에서 Firebase CDN 모듈을 불러오기 때문에 별도 빌드 과정은 필요하지 않습니다.

## 필수 입력값

견적 저장과 PDF 내보내기는 아래 값이 모두 있어야 실행됩니다.

- 거래처명
- 견적명
- 비고
- 아이템명
- 단가
- 수량
- 환율
```
