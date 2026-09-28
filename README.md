<div align="center">

# ConThink

**서로 다른 전공·직무가 함께 일할 때 생기는 지식 격차를, 회의 중 개인 맞춤 해설로 줄이는 AI 회의 지원 도구**

![React](https://img.shields.io/badge/React_19-61DAFB?logo=react&logoColor=black)
![Vite](https://img.shields.io/badge/Vite_7-646CFF?logo=vite&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-5FA04E?logo=nodedotjs&logoColor=white)
![Go](https://img.shields.io/badge/Go-00ADD8?logo=go&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![Deepgram](https://img.shields.io/badge/Deepgram-STT-13EF93?logo=deepgram&logoColor=black)
![OpenAI](https://img.shields.io/badge/OpenAI-412991?logo=openai&logoColor=white)

[![Live Demo](https://img.shields.io/badge/Live_Demo-unithon.ssu--on.com-2EA44F?style=for-the-badge)](https://unithon.ssu-on.com/)
[![Demo Video](https://img.shields.io/badge/Demo_Video-Google_Drive-4285F4?style=for-the-badge&logo=googledrive&logoColor=white)](https://drive.google.com/file/d/1VZfHy4HK9wv9EMxKpC-AFgqU2IpSpt9g/view)
[![Team GitHub](https://img.shields.io/badge/Team-BeUnicorn2026-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/BeUnicorn2026)

<sub>2026 숭실대학교 교내 연합 해커톤 UNITHON 참가작 · 5인 팀 유니콘될꺼야</sub>

</div>

---

> 본인 담당: **시스템 아키텍처 설계 · PostgreSQL DB 설계**

## 문제

서로 다른 전공과 직무가 참여하는 회의에서는 같은 발언도 각자의 경험과 배경지식에 따라 다르게 이해될 수 있습니다. 이 과정에서 용어 검색, 재질문, 반복 설명과 맥락 재확인이 발생하고 논의의 흐름이 끊깁니다.

ConThink는 AI가 회의의 결론을 대신 정하는 서비스가 아닙니다. AI는 이해를 위한 반복 작업을 줄이고, 사람에게는 아이디어 제시, 의견 조율, 판단과 최종 결정을 남깁니다.

## 핵심 기능

| 기능 | 설명 |
|---|---|
| **실시간 회의 자막** | 참여자별 마이크 입력을 STT로 변환하고 사용자 ID와 함께 처리해, 별도의 화자 분리 없이 발언자를 구분합니다. |
| **사용자 지식 프로필** | 사용자가 입력한 전공, 경험과 보유 지식을 LLM이 구조화해 개인화 기준으로 활용합니다. |
| **개인 맞춤형 지식 해설** | 같은 발언도 청자의 지식 프로필과 회의 맥락에 따라 서로 다른 난이도와 표현으로 설명합니다. 원문 발언은 그대로 보존됩니다. |
| **대화 맥락 트리** | 실시간 발언의 관계를 트리 형태로 구조화해 현재 논점과 앞선 대화의 연결 관계를 보여줍니다. |
| **회의 기록 보관** | 회의가 끝난 뒤 전체 발언, 개인화 해설과 맥락 트리를 다시 확인할 수 있습니다. |

## 아키텍처

<p align="center">
  <img src="https://github.com/user-attachments/assets/b6e9d752-7157-48de-82d3-65b4bc765d56" alt="ConThink 아키텍처" width="100%">
</p>

**처리 흐름**

```
참여자별 음성 입력
        ↓
실시간 STT 및 발언자 식별
        ↓
확정 발화 저장
        ↓
사용자 지식 프로필 + 회의 맥락 분석
        ↓
개인 맞춤 해설 ───── 대화 맥락 트리
        ↓
실시간 사이드 패널 및 회의 기록
```

Node.js 서버는 인증, STT, WebSocket 통신과 데이터 저장을 담당하고, Go 서비스는 대화 구조화를 위한 비동기 AI 파이프라인을 담당하도록 분리했습니다. AI 처리에 문제가 생겨도 실시간 자막은 계속 동작합니다.

## 기술적 의사결정

**모델 선택**
고성능 모델은 품질이 높지만 비용과 응답 지연이 크고, 저비용 모델은 설명 품질이 떨어졌습니다. 속도·비용·품질 세 요소를 비교해 절충안을 선택했습니다.

**설명 필터링**
모든 전문용어를 설명하면 사용자가 이미 아는 내용까지 반복되어 오히려 회의를 방해할 수 있습니다. 그래서 2단계 파이프라인으로 구성했습니다.
1. 발언에 포함된 주요 전문용어와 맥락 해설 생성
2. 사용자 지식 프로필을 기준으로 불필요한 설명 필터링

**파이프라인 최적화**
초기 대화 구조화 과정은 AI를 세 번 순차 호출해서 응답 지연이 생겼습니다. 중복 호출을 하나로 통합해 3단계를 2단계로 줄였습니다.

## 기술 스택

| 분야 | 기술 |
|---|---|
| 프론트엔드 | React 19, Vite 7, StyleX, Electron (데스크톱 셸) |
| 백엔드 | Node.js (Express, WebSocket), Go (비동기 AI 파이프라인) |
| 데이터베이스 | PostgreSQL |
| AI | Deepgram (실시간 STT), OpenAI (해설·필터링), OpenRouter (대화 구조화) |
| 배포 | Docker Compose, Cloudflare |

## 저장소 구성

`front/`, `back/`는 git submodule로 연결되어 있습니다. 원본 저장소의 특정 커밋을 가리키는 참조이며, 코드는 복제되어 있지 않습니다. 각 폴더를 누르면 실제 저장소로 이동해 전체 커밋 이력을 볼 수 있습니다.

| 폴더 | 설명 |
|---|---|
| [front](https://github.com/BeUnicorn2026/front) | React + Vite 웹 클라이언트 |
| [back](https://github.com/BeUnicorn2026/back) | Node.js API 서버 + Go 비동기 AI 파이프라인 |

## 팀

| 이름 | 역할 |
|---|---|
| 홍서진 | S/W 개발 총괄, 대화 내용 구조화 AI |
| **박소정** | **시스템 아키텍처, PostgreSQL DB 설계** |
| 이시온 | 문제 정의, 서비스 플로우 및 사업화 전략 기획 |
| 이정훈 | 프론트엔드·백엔드 개발 |
| 김영서 | UI/UX 디자인, 발표자료 |

---

UNITHON 기간에 구현한 해커톤 MVP입니다. 실시간 STT, 개인 맞춤 해설, 지식 도메인 분석, 대화 내용 구조화, 회의 기록 저장 기능을 구현했습니다.
