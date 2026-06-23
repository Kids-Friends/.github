<div align="center">
# 2026-1 한양대 에리카 모바일로봇의 이해 6조

# 🤖 Kids-Friends

### 키즈카페를 위한 테미(Temi) 안내로봇 통합 프로젝트
**놀이 · 대화 · 안전을 하나의 로봇으로.**

아이가 화면을 터치하거나 말을 걸면, 로봇이 놀이/안내 화면으로 바뀌고
캐릭터 표정과 귀여운 목소리로 응대합니다. 미세먼지·화재 같은 위험도 스스로 감지해 알립니다.

![Android](https://img.shields.io/badge/App-Android%20(Java)-3DDC84?logo=android&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Server-Spring%20Boot%203.5-6DB33F?logo=springboot&logoColor=white)
![React](https://img.shields.io/badge/Web-React%20%2B%20Vite-61DAFB?logo=react&logoColor=black)
![Raspberry Pi](https://img.shields.io/badge/HW-Raspberry%20Pi-A22846?logo=raspberrypi&logoColor=white)
![Arduino](https://img.shields.io/badge/HW-Arduino%20Mega-00979D?logo=arduino&logoColor=white)
![AI](https://img.shields.io/badge/AI-Groq%20LLaMA%203.1-F55036?logo=meta&logoColor=white)

</div>

---

## 📑 목차

- [무엇을 만드나요?](#-무엇을-만드나요)
- [시스템 한눈에 보기](#-시스템-한눈에-보기)
- [레포지토리 지도](#-레포지토리-지도)
- [레포별 상세 설명](#-레포별-상세-설명) ← 토글
- [핵심 기능 5종 + 안전](#-핵심-기능-5종--안전) ← 토글
- [데이터 흐름 & 통신 규격](#-데이터-흐름--통신-규격) ← 토글
- [빠른 시작 (전체 실행 순서)](#-빠른-시작-전체-실행-순서) ← 토글
- [기술 스택 총정리](#-기술-스택-총정리) ← 토글
- [개발 규칙 & 컨벤션](#-개발-규칙--컨벤션) ← 토글
- [보안 & 공개(public) 전환 전 체크](#-보안--공개public-전환-전-체크) ← 토글
- [후배들에게 — 학습 가이드](#-후배들에게--학습-가이드) ← 토글

---

## 🎯 무엇을 만드나요?

**Kids-Friends**는 키즈카페에 배치된 **테미(Temi) 로봇** 위에서 동작하는 안내·놀이 서비스입니다.
하나의 로봇이 아래를 모두 합니다.

- 🗣️ **대화** — 아이가 캐릭터 친구에게 "전화"를 걸면, 서버의 AI가 친근한 반말로 답합니다.
- 🎮 **놀이** — 사진 찍기, O/X 안전 퀴즈, 카페 길 안내.
- 🛡️ **안전** — 미세먼지를 실시간으로 알려주고, 화재가 감지되면 즉시 비상 안내를 합니다.

이 프로젝트는 여러 분야(앱·서버·하드웨어·웹)를 한 번에 경험할 수 있어, **로봇 수업의 실습 교보재**로도 쓰입니다.

---

## 🗺️ 시스템 한눈에 보기

5개의 독립 레포가 모여 하나의 로봇을 움직입니다.

```
        ┌──────────────────────────┐
        │     아이 / 운영자         │
        └────────────┬─────────────┘
                     │ 터치 · 음성
                     ▼
   ┌─────────────────────────────────┐        ┌──────────────────────┐
   │           KF-FE                  │        │        KF_WEB         │
   │   테미 로봇 안드로이드 앱          │        │     관리자 웹(데모)    │
   │   (얼굴 · 표정 · 음성 · 화면)      │        │   대시보드 · 로그 보기 │
   └───────▲───────────────┬─────────┘        └───────────▲──────────┘
           │ WebSocket      │ AI 질문(REST)               │ REST
           │ (센서 방송)     ▼                             │
   ┌───────┴─────────────────────────────────────────────┴──────────┐
   │                          KF-BE                                   │
   │      Spring Boot 서버  ·  AI 대화 생성  ·  센서 수신/중계         │
   └───────────────────────────▲────────────────────────────────────┘
                               │ 센서값 (HTTP POST /api/sensor-events)
              ┌────────────────┴─────────────────┐
              │                                   │
   ┌──────────┴───────────┐          ┌────────────┴───────────┐
   │        KF-HW          │  ⟷ 대체  │         KF_AD           │
   │   라즈베리파이 센서허브 │  (고장 시) │   아두이노 센서허브(백업) │
   └──────────────────────┘          └────────────────────────┘
     미세먼지·화재·거리·비전              동일 센서를 아두이노로
```

> 💡 **핵심 흐름**
> 1. 하드웨어(**KF-HW** 또는 **KF_AD**)가 센서값을 **KF-BE**로 보냄
> 2. **KF-BE**가 WebSocket으로 **KF-FE**(앱)에 실시간 방송
> 3. 아이가 질문하면 **KF-FE → KF-BE(AI) → KF-FE**로 답이 돌아옴
> 4. 운영자는 **KF_WEB**에서 현황을 봄

---

## 📦 레포지토리 지도

| 레포 | 역할 | 한 줄 설명 | 주요 기술 |
|---|---|---|---|
| **[KF-FE](https://github.com/Kids-Friends/KF-FE)** | 📱 앱 | 테미 로봇 위에서 도는 안드로이드 앱(얼굴·표정·음성·화면) | Android · Java · Temi SDK |
| **[KF-BE](https://github.com/Kids-Friends/KF-BE)** | 🧠 서버 | AI 대화 생성 + 센서 수신/중계 허브 | Spring Boot · Java 17 · Groq |
| **[KF-HW](https://github.com/Kids-Friends/KF-HW)** | 📡 하드웨어 | 라즈베리파이 센서 허브(미세먼지·화재·거리·비전) | Python · 라즈베리파이 |
| **[KF_AD](https://github.com/Kids-Friends/KF_AD)** | 🔌 하드웨어(백업) | 라파 고장 시 대체하는 아두이노 센서 허브 | Arduino · Mega 2560 |
| **[KF_WEB](https://github.com/Kids-Friends/KF_WEB)** | 🖥️ 웹 | 운영자용 관리자 대시보드(현재 목업 데모) | React · Vite |

> ⚠️ 레포 이름 표기 주의: `KF-FE/KF-BE/KF-HW`는 **하이픈**, `KF_AD/KF_WEB`은 **언더스코어**입니다.

---

## 🔍 레포별 상세 설명

<details>
<summary><b>📱 KF-FE — 테미 로봇 안드로이드 앱 (클릭)</b></summary>

<br>

아이가 직접 만지고 말을 거는 **로봇의 얼굴이자 몸**입니다. 테미 로봇 위에서 실행됩니다.

- **하는 일**: 화면 터치/음성 명령 → 놀이·안내 화면 전환 → 캐릭터 표정 + TTS로 응대.
- **서버 연동**: [KF-BE](https://github.com/Kids-Friends/KF-BE)와 ngrok 주소로 통신(REST + WebSocket).
- **⚠️ 버전 동결(절대 변경 금지)**: `Gradle 8.1 / AGP 8.1.0 / Java 11 / compileSdk 33`, Temi SDK `1.131.4`.
- **핵심 위치**: `MainActivity.java`(모든 대화/표정 흐름의 중심), `domain/`(기능별 화면), `docs/`(디자인 기준·표정 에셋).
- **실행**: Android Studio에서 열고 ▶ Run (테미 실기기 또는 에뮬레이터). 먼저 KF-BE가 켜져 있어야 함.

**기술**: Android(Java) · Temi SDK · Retrofit2 + OkHttp(WebSocket) · Media3(ExoPlayer)

</details>

<details>
<summary><b>🧠 KF-BE — 백엔드 서버 (클릭)</b></summary>

<br>

모든 데이터가 거쳐 가는 **두뇌**입니다. 딱 두 가지를 합니다.

1. **AI 대화 만들기** — 아이 질문을 Groq(`llama-3.1-8b-instant`)에 보내 뽀로로 친구처럼 반말로 답하는 문장 생성.
2. **센서 신호 중계** — 하드웨어가 올린 센서값을 받아 앱이 듣는 WebSocket 채널로 방송.

| 종류 | 주소 | 설명 |
|---|---|---|
| 헬스체크 | `GET /api/health` | 서버 생존 확인 |
| AI 대화 | `POST /api/chat/ai` | 질문 → AI 답변 |
| 센서 수신 | `POST /api/sensor-events` | HW/아두이노 입구 |
| 센서 방송 | `WebSocket /ws/sensors` | 앱이 실시간 수신 |

- **포트 8080**, 부팅 시 **ngrok 고정 도메인** 자동 실행. API 문서는 `/swagger-ui.html`.
- **설정**: `.env`에 Groq 키(`AI_API_KEY=gsk_...`) 주입. 센서값은 저장 없이 즉시 방송(휘발성, DB 불필요).

**기술**: Java 17 · Spring Boot 3.5.14 · Spring AI(Groq) · Spring WebSocket · springdoc

</details>

<details>
<summary><b>📡 KF-HW — 라즈베리파이 센서 허브 (클릭)</b></summary>

<br>

로봇의 **감각기관**. 라즈베리파이에 연결된 센서값을 읽어 서버로 보냅니다.

| 센서 | 감지 | 이벤트 |
|---|---|---|
| CubeEye S111DU (ToF) | 장애물/접근 | `OBSTACLE_DETECTED` |
| Cubic PM2008 | 미세먼지(PM2.5) | `AIR_QUALITY`, `DUST_HIGH` |
| 화재경보기(GPIO) | 불/연기 | `FIRE_ALARM` |
| WonderMV(비전) | 얼굴·O/X 포즈 | `CHILD_DETECTED`, `QUIZ_ANSWER` |
| USB 웹캠(OpenCV) | 얼굴 | `CHILD_DETECTED` |

- `src/main.py`는 **실제 라즈베리파이 + CubeEye SDK** 환경에서만 동작.
- **PC 연습**은 `tests/mock_stream.py`로 가짜 데이터를 흘려 테스트.
- 보안 하드닝됨: 과거 공개 MQTT 브로커 기본값 제거 → `localhost` + 환경변수.

**기술**: Python 3 · CubeEye ToF SDK · OpenCV · pyserial · RPi.GPIO

</details>

<details>
<summary><b>🔌 KF_AD — 아두이노 센서 허브(백업) (클릭)</b></summary>

<br>

> 📌 이름의 **AD = Arduino** (광고 아님!)

라즈베리파이([KF-HW](https://github.com/Kids-Friends/KF-HW))가 고장났을 때 **아두이노 Mega 2560**으로 동일한 센서들을 대체합니다. 보내는 데이터 형식이 같아 서버/앱은 구분할 필요가 없습니다.

- **부품**: Arduino Mega 2560 · W5500 이더넷 · PM2008 · 화재경보기 · HC-SR04(ToF 대체) · WonderMV.
- **핵심 특징 — `MOCK_MODE`**: 센서를 일부만 꽂아도 데모가 안 끊김.
  - `MOCK_AUTO`(기본): 연결된 센서는 실제값, 응답 없는 센서만 자동 가짜값.
  - `MOCK_ON`: 전부 가짜값(보드만으로 QA). `MOCK_OFF`: 실제값만.
- **설치**: `arduino_setup.txt`에 배선·설치·문제해결 정리. ArduinoJson 라이브러리 필요.

**기술**: Arduino(C/C++) · Mega 2560 · W5500 Ethernet · ArduinoJson

</details>

<details>
<summary><b>🖥️ KF_WEB — 관리자 웹 (클릭)</b></summary>

<br>

키즈카페 운영자가 **로봇을 한눈에 관리**하는 대시보드입니다.

- **기능**: 통합 대시보드(회원·로봇·호출 현황 + 그래프), 회원/로봇 관리, 호출·채팅 로그, 설정.
- 📌 **현재는 시연용 데모** — 실제 서버/DB 없이 **목업 데이터**로 동작(서버 안 켜도 화면 확인 가능).
- **실행**: `npm install` → `npm run dev` → `http://localhost:4173/`.

**기술**: React · Vite · Recharts · Framer Motion · Axios

</details>

---

## 🎬 핵심 기능 5종 + 안전

<details>
<summary><b>시연 시나리오 전체 보기 (클릭)</b></summary>

<br>

각 기능 옆 라벨은 구현 방식입니다 — **[가라]** 스크립트 시연 · **[AI]** 실제 AI 호출 · **[실제]** 센서 파이프라인 실연동.

| # | 기능 | 방식 | 동작 |
|---|---|---|---|
| 1 | 📷 **사진 찍기** | [가라] | "사진 찍자" → 3초 카운트다운 촬영 → 프레임/필터 |
| 2 | 🎮 **게임 하기** | [가라] | O/X 안전 퀴즈 (예: "미끄럼틀에서 친구 밀어도 될까?") |
| 3 | 📞 **친구에게 전화하기** | [AI] | 캐릭터 선택 → 통화 → 아이 질문을 `POST /api/chat/ai`로 보내 친구 말투로 답함 |
| 4 | 🗺️ **카페 안내** | [가라] | 지도에서 존 터치 → 로봇이 자율주행으로 이동 |
| 5 | 🌫️ **공기 확인** | [실제] | `/ws/sensors`로 받은 실시간 PM2.5 → 좋음/보통/나쁨 안내 |
| + | 🔥 **화재경보** | [실제] | `FIRE_ALARM(DETECTED)` 수신 시 즉시 "애들아 불이났어! 비상!" + 비상화면 |

</details>

---

## 🔌 데이터 흐름 & 통신 규격

<details>
<summary><b>센서 이벤트 / API 규격 보기 (클릭)</b></summary>

<br>

**① 하드웨어 → 서버** (`POST /api/sensor-events`)
```json
{
  "robotId": "TEMI_01",
  "eventType": "AIR_QUALITY",
  "payload": { "pm25": 23.0, "unit": "ug/m3", "grade": "NORMAL" }
}
```

**② 서버 → 앱** (`WebSocket /ws/sensors` 방송, envelope)
```json
{ "robotId": "TEMI_01", "eventType": "AIR_QUALITY", "payload": { ... }, "receivedAt": "2026-06-23T12:00:00" }
```

**주요 eventType**

| eventType | payload 예시 |
|---|---|
| `AIR_QUALITY` | `{ "pm25": 23.0, "grade": "NORMAL" }` (약 15초 주기) |
| `DUST_HIGH` | `{ "pm25": 88.0, "grade": "BAD" }` (임계 초과 시) |
| `FIRE_ALARM` | `{ "status": "DETECTED" }` 또는 `"CLEARED"` |
| `OBSTACLE_DETECTED` | `{ "distance_cm": 45, "direction": "FRONT" }` |
| `CHILD_DETECTED` | `{ "source": "wondermv" }` |
| `QUIZ_ANSWER` | `{ "answer": "O" }` |

**미세먼지 등급 (환경부 PM2.5 기준)**

| PM2.5 (㎍/㎥) | 등급 |
|---|---|
| ≤ 15 | `GOOD` (좋음) |
| 16 ~ 35 | `NORMAL` (보통) |
| ≥ 36 | `BAD` (나쁨) |
| ≥ 75 | 추가로 `DUST_HIGH` 경보 |

</details>

---

## 🚀 빠른 시작 (전체 실행 순서)

<details>
<summary><b>처음부터 끝까지 실행하기 (클릭)</b></summary>

<br>

**1) 서버 켜기 — [KF-BE](https://github.com/Kids-Friends/KF-BE)**
```bash
# .env 에 Groq 키 넣기:  AI_API_KEY=gsk_발급받은키
./gradlew bootRun           # (Windows) .\gradlew.bat bootRun
# 확인:  http://localhost:8080/api/health  → OK
```

**2) 센서 보내기 — [KF-HW](https://github.com/Kids-Friends/KF-HW)(라파) 또는 [KF_AD](https://github.com/Kids-Friends/KF_AD)(아두이노)**
```bash
# 라즈베리파이
pip install -r requirements.txt
python src/main.py
# (PC 연습)  python tests/mock_stream.py
```

**3) 앱 실행 — [KF-FE](https://github.com/Kids-Friends/KF-FE)**
> Android Studio에서 열고 ▶ Run (테미 실기기/에뮬레이터). 서버 주소(ngrok)는 `ApiConfig`에서 확인.

**4) 관리자 웹 — [KF_WEB](https://github.com/Kids-Friends/KF_WEB)**
```bash
npm install && npm run dev   # → http://localhost:4173/
```

</details>

---

## 🧰 기술 스택 총정리

<details>
<summary><b>스택 한 번에 보기 (클릭)</b></summary>

<br>

| 영역 | 스택 |
|---|---|
| **앱** | Android (Java), Temi SDK 1.131.4, Retrofit2 + OkHttp(WebSocket), Media3 |
| **서버** | Java 17, Spring Boot 3.5.14, Spring AI(Groq · OpenAI 호환), WebSocket, springdoc |
| **AI** | Groq `llama-3.1-8b-instant` (텍스트, 반말 페르소나) |
| **하드웨어(라파)** | Python 3, CubeEye ToF SDK, OpenCV, pyserial, RPi.GPIO |
| **하드웨어(아두이노)** | Arduino Mega 2560, W5500 Ethernet, ArduinoJson |
| **웹** | React 18, Vite 5, Recharts, Framer Motion, Axios |
| **인프라** | ngrok(고정 도메인 터널), 포트 8080 |

</details>

---

## 📐 개발 규칙 & 컨벤션

<details>
<summary><b>반드시 지킬 규칙 (클릭)</b></summary>

<br>

- **레포는 각각 독립**입니다. 공통 부모 레포는 없습니다.
- **버전 동결(KF-FE)**: `Gradle 8.1 / AGP 8.1.0 / Java 11 / compileSdk 33` — 변경 금지.
- **보안 파일 커밋 금지**: `.env`, `ngrok.yml`, 키스토어 등은 절대 올리지 않습니다(각 `.gitignore`로 관리).
- **AI 프로바이더는 Groq**: `application.yml`에 `openai`로 적혀 있어도 정상(= OpenAI 호환 프로토콜).
- 커밋 메시지는 `feat:`, `fix:`, `docs:`, `chore:` 프리픽스 사용.

</details>

---

## 🔐 보안 & 공개(public) 전환 전 체크

<details>
<summary><b>private → public 전 점검 항목 (클릭)</b></summary>

<br>

현재 레포는 **private**이며, 실제 API 키·토큰은 코드/히스토리에 **박혀 있지 않습니다**(.env 규율 양호).
공개로 전환한다면 아래를 먼저 처리하세요.

- [ ] **KF_WEB**: 관리자 비번 `admin1234`가 **과거 히스토리**에 남아 있음 → 재사용 금지(필요 시 히스토리 재작성). `VITE_*` 환경변수는 공개 JS 번들에 박힘 → 실인증 아님.
- [ ] **KF-BE**: `/api/chat/ai` 무인증 + CORS `*` + 고정 ngrok 도메인 노출 → API 키/Origin 제한 또는 도메인 교체(Groq 쿼터 보호).
- [ ] **KF-BE**: DB 비번 `1111` 하드코딩 제거 → 환경변수/강한 비번.
- [ ] **KF_AD**: `.gitignore` 추가, 내부 IP/MAC을 예시값으로 치환.
- [ ] **전체**: 공개 직전 `gitleaks`로 히스토리 최종 스캔.

> ⚠️ **ngrok 터널은 레포가 private여도 인터넷에 열려 있습니다.** 시연할 때만 켜고 평소엔 끄세요.

</details>

---

## 🎓 후배들에게 — 학습 가이드

<details>
<summary><b>어디서부터 보면 좋을까? (클릭)</b></summary>

<br>

이 프로젝트는 **앱 · 서버 · 하드웨어 · 웹**을 한 번에 경험할 수 있는 좋은 교보재입니다.
관심 분야부터 시작하세요. 각 레포의 `README.md`에 따라하기식 실행법이 있습니다.

| 관심 분야 | 추천 시작 레포 |
|---|---|
| 앱/안드로이드가 궁금하면 | [KF-FE](https://github.com/Kids-Friends/KF-FE) |
| 서버/AI가 궁금하면 | [KF-BE](https://github.com/Kids-Friends/KF-BE) |
| 센서/하드웨어가 궁금하면 | [KF-HW](https://github.com/Kids-Friends/KF-HW) → [KF_AD](https://github.com/Kids-Friends/KF_AD) |
| 웹/프론트가 궁금하면 | [KF_WEB](https://github.com/Kids-Friends/KF_WEB) |

**전체 흐름을 먼저 이해하고 싶다면** 위 [시스템 한눈에 보기](#-시스템-한눈에-보기)를 보고,
**KF-BE → KF-HW → KF-FE** 순서로 데이터가 어떻게 흐르는지 따라가 보세요.

</details>

---

<div align="center">

**Kids-Friends** · 키즈카페 안내로봇 프로젝트
아이의 새로운 친구이자, 가장 든든한 안전 지킴이 🤖

</div>
