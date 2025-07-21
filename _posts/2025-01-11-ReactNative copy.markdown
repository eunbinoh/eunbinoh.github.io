---
layout: post
title: "ReactNative"
date: 2025-01-11 00:00:00
post-image: "/assets/images/post_rn_expo.png"
tags: ["React Native", "Expo", "WebApp", "Cross-platform"]
---

# React Native Expo

- **크로스 플랫폼 모바일 앱 개발** 프레임워크
- **Expo SDK**: 네이티브 기능에 쉽게 접근할 수 있는 통합 개발 환경
- **Over-the-Air Updates**: 앱스토어 재배포 없이 실시간 업데이트
- **Managed Workflow**: 복잡한 네이티브 설정 없이 빠른 개발 시작
- **Universal Apps**: iOS, Android, Web을 하나의 코드베이스로 개발
- **Developer Experience**: 핫 리로딩, 디버깅, 프리뷰 등 개발자 친화적 도구

---

## 개발 워크플로우 - Managed vs Bare

프로젝트 설정과 네이티브 코드 접근 방식에 따른 **워크플로우 선택**

- **Managed Workflow**: **Expo가 모든 네이티브 코드 관리**

  - `expo init` 명령어로 즉시 시작
  - Expo SDK를 통한 네이티브 기능 접근
  - 빠른 프로토타이핑과 간단한 앱 개발에 적합

- **Bare Workflow**: **네이티브 코드 직접 제어**
  - `expo eject` 또는 `expo install --bare` 사용
  - 커스텀 네이티브 모듈과 라이브러리 자유롭게 추가
  - 복잡한 네이티브 기능이 필요한 앱 개발에 적합

---

## 네이티브 기능 - Expo SDK vs Third-party

모바일 디바이스의 하드웨어와 OS 기능 접근 방식

- **Expo SDK**: **검증된 네이티브 API 모음**

  - Camera, Location, Notifications 등 40+ 모듈 제공
  - 일관된 API와 크로스 플랫폼 호환성 보장
  - 안정성과 유지보수성이 중요한 프로젝트에 적합

- **Third-party Libraries**: **커뮤니티 기반 확장**
  - react-native-community, 개별 라이브러리 활용
  - 더 세밀한 제어와 최신 기능 접근 가능
  - 특수한 요구사항과 커스터마이징이 필요한 경우에 적합

---

## 상태 관리 - Context API vs Redux Toolkit

모바일 앱의 복잡한 상태를 효율적으로 관리하는 방법

- **Context API + useReducer**: **React 내장 상태 관리**

  - 추가 라이브러리 없이 전역 상태 관리
  - 컴포넌트 트리 전체에서 상태 공유
  - 중소규모 앱과 간단한 상태 로직에 적합

- **Redux Toolkit**: **예측 가능한 상태 컨테이너**
  - 불변성, 시간 여행 디버깅, 미들웨어 지원
  - createSlice, createAsyncThunk로 보일러플레이트 최소화
  - 복잡한 비즈니스 로직과 대규모 앱에 적합

---

## 배포 및 빌드 - EAS Build vs Classic Build

앱스토어 배포를 위한 빌드 시스템 선택

- **EAS Build**: **차세대 클라우드 빌드 서비스**

  - 클라우드에서 iOS/Android 빌드 자동화
  - 커스텀 빌드 설정과 환경 변수 지원
  - 팀 협업과 CI/CD 파이프라인 통합에 최적화

- **Classic Build**: **기존 Expo 빌드 시스템**
  - `expo build` 명령어 기반 빌드
  - 간단한 설정으로 빠른 빌드 가능
  - 개인 프로젝트와 프로토타입 배포에 적합

---
