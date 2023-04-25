# pixi-study

## goal

- 다이어그램 에디터 기능을 구현해본다.
- HTML Canvas API 라이브러리가 아닌 Pixi.js를 사용한다.

---

## project spec

- Vue 3
- HTML, CSS, Javascript
- ESLint

## setup and script guide

```bash
yarn install
yarn serve
yarn build
```

---

## features

- [x] TexturePacker 이용해 스프라이트 생성하기
- [x] 사용자에게 스프라이트 객체 생성 기능 제공하기
- [ ] 사용자에게 스프라이트 객체 삭제 기능 제공하기
- [ ] 스프라이트 클릭 이벤트로 모달 띄우기
- [x] 스프라이트 드래그 이벤트로 위치 옮기기
- [ ] 스프라이트 복제 기능 제공하기
  - [ ] 수직/수평 방향으로 동일한 texture 가진 스프라이트 생성
- [x] 캔버스 영역 줌 인/아웃 기능 넣기
  - [x] Mouse Wheel 이벤트로 구현하기
  - [x] 현재 줌 scale 값 화면에 출력하기
- [ ] 캔버스 영역 패닝 기능 넣기

- [ ] import 하기
  - [ ] .txt file format 지원하기
  - [ ] .json file format 지원하기
- [ ] export 하기
  - [ ] .txt file format 지원하기
  - [ ] .json file format 지원하기
- [ ] 버전 관리
