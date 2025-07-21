---
layout: post
title: "Storybook"
date: 2024-12-08 00:00:00
post-image: "/assets/images/post_storybook.png"
tags: ["Storybook", "UI", "Component", "Testing", "Documentation"]
---

# Storybook

- UI 컴포넌트를 **독립적으로 개발하고 테스트**할 수 있는 도구
- **컴포넌트 주도 개발**: 애플리케이션과 분리된 환경에서 컴포넌트 개발
- **시각적 테스팅**: 다양한 상태와 props로 컴포넌트 동작 확인
- **문서화 자동화**: 컴포넌트 사용법과 API를 자동으로 문서화
- **크로스 브라우저 테스트**: 여러 환경에서 일관된 UI 동작 보장
- **디자인 시스템 구축**: 재사용 가능한 컴포넌트 라이브러리 관리

---

## Story 작성 - CSF vs MDX

컴포넌트의 다양한 상태를 정의하고 문서화하는 **Story 시스템**

- **CSF (Component Story Format)**: JavaScript 기반 스토리 작성

  - `export default { title, component }`: 메타데이터 정의
  - `export const Primary = {}`: 개별 스토리 정의
  - args, argTypes를 통한 동적 props 제어에 적합

- **MDX**: Markdown + JSX 혼합 문서 작성
  - 풍부한 문서화와 인터랙티브 예제 결합
  - `<Story>`, `<Canvas>` 컴포넌트로 스토리 임베딩
  - 상세한 가이드라인과 사용법 문서화에 적합

---

## 애드온 시스템 - Controls vs Actions

스토리북의 기능을 확장하는 **애드온 생태계**

- **Controls**: **실시간 props 조작**

  - 브라우저에서 직접 컴포넌트 props 변경
  - 다양한 입력 타입 자동 감지 (text, boolean, select 등)
  - 디자이너와 개발자 간 협업에 최적화

- **Actions**: **이벤트 핸들러 모니터링**
  - 컴포넌트에서 발생하는 이벤트 로깅
  - 콜백 함수 호출 여부와 전달된 데이터 확인
  - 사용자 인터랙션 테스트와 디버깅에 적합

---

## 테스팅 전략 - Visual Testing vs Interaction Testing

- **Visual Testing**: **UI 회귀 테스트**

  - Chromatic, Percy 등을 통한 스크린샷 비교
  - 브랜치별 시각적 변경사항 자동 감지
  - 디자인 시스템 일관성 유지에 강력

- **Interaction Testing**: **사용자 행동 시뮬레이션**
  - `@storybook/testing-library`로 실제 사용자 시나리오 테스트
  - 클릭, 입력, 호버 등 인터랙션 자동화
  - 컴포넌트 동작 로직 검증에 최적화

---

## 배포 및 공유 - Static Build vs Chromatic

- **Static Build**: **정적 사이트 배포**

  - `storybook build`로 정적 파일 생성
  - Netlify, Vercel 등 호스팅 플랫폼에 배포
  - 팀 내부 문서화와 컴포넌트 쇼케이스에 적합

- **Chromatic**: **전문 스토리북 플랫폼**
  - 자동 시각적 회귀 테스트와 리뷰 워크플로우
  - 브랜치별 변경사항 추적과 승인 시스템
  - 엔터프라이즈급 디자인 시스템 관리에 최적화

---
