# 🤖 키즈프렌즈 시연 실행 매뉴얼

> 각 단계는 토글(▸)을 눌러 펼칩니다. 단계 끝의 **✅ 성공 신호**를 꼭 확인하세요.
> 모든 주소는 고정값 **`ngrok주소`** 를 씁니다(이미 코드에 박혀 있어 보통 안 바꿔도 됨).

| 장비 | 역할 | 도구 | 켜두기 |
|---|---|---|---|
| 컴퓨터 A | 서버(두뇌) | **IntelliJ** | ✅ |
| 라즈베리파이 | 센서/카메라 | **터미널(리눅스)** | ✅ |
| 테미 로봇 | 시연 본체 | (앱 설치돼 단독 실행) | ✅ |
| 컴퓨터 B | 테미에 앱 설치 | **Android Studio** | 설치 후 꺼도 됨 |

---

<details>
<summary>📦 0. 시작 전 — 각 컴퓨터에 깔 프로그램</summary>

- **컴퓨터 A (서버)**: JDK 17, MySQL, ngrok, Git, IntelliJ IDEA
  - JDK 17: https://adoptium.net (Temurin 17)
  - MySQL: https://dev.mysql.com/downloads/installer/
  - ngrok: https://ngrok.com/download
  - IntelliJ: https://www.jetbrains.com/idea/ (Community 무료판으로 충분)
- **컴퓨터 B (앱 설치)**: Android Studio, USB 케이블
  - Android Studio: https://developer.android.com/studio
- **라즈베리파이**: 파이썬3(기본 내장), Git (없으면 `sudo apt install git`)

</details>

---

<details>
<summary>🧠 용어 한 줄 사전</summary>

- **터미널/명령창**: 명령어 입력하는 창 (Windows=PowerShell, 라즈베리파이=Terminal)
- **클론(clone)**: 깃허브 코드를 내 컴퓨터로 내려받기
- **ngrok**: 내 서버를 인터넷 주소로 열어주는 도구
- **adb**: 컴퓨터에서 안드로이드(테미)에 앱을 설치하는 통로

</details>

---

<details>
<summary>🖥️ 1. 컴퓨터 A — 서버(BE)를 IntelliJ로 켜기 【상세】</summary>

### 1-1. JDK 17 설치
1. https://adoptium.net 에서 **Temurin 17 (LTS)** 다운로드 → 설치(다음만 누르면 됨).

### 1-2. MySQL 설치 + 빈 데이터베이스 만들기
1. MySQL Installer로 MySQL Server 설치. 설치 중 **root 비밀번호**를 정합니다(예: `1111`).
2. 설치된 **MySQL Command Line Client** 실행 → root 비밀번호 입력 → 아래 한 줄 입력:
   ```sql
   CREATE DATABASE kf_db CHARACTER SET utf8mb4;
   ```
   > 표(테이블)는 서버가 켜질 때 자동으로 만들어집니다(Flyway). 빈 DB만 있으면 됩니다.

### 1-3. ngrok 설치 + 토큰 등록
1. https://ngrok.com 가입 → https://dashboard.ngrok.com/get-started/your-authtoken 에서 **토큰(authtoken)** 복사.
2. ngrok 다운로드 후 설치. (Windows는 `ngrok.exe`를 적당한 폴더에 두고 PATH 추가 또는 그 폴더에서 실행)

### 1-4. 코드 내려받기
1. 명령창(PowerShell)에서:
   ```powershell
   git clone https://github.com/Kids-Friends/KF-BE.git
   cd KF-BE
   ```

### 1-5. `.env` 파일 만들기 (비밀값)
1. `KF-BE` 폴더의 **`.env.example`** 을 복사해 같은 폴더에 **`.env`** 로 이름 변경.
2. 메모장으로 `.env`를 열어 값 채우기:
   ```env
   SERVER_PORT=8081
   NGROK_AUTHTOKEN=여기에_ngrok_토큰
   DB_URL=jdbc:mysql://localhost:3306/kf_db?serverTimezone=Asia/Seoul&characterEncoding=UTF-8
   DB_USERNAME=root
   DB_PASSWORD=내가_정한_MySQL_비밀번호
   AI_API_KEY=여기에_Groq_키
   ```
   > Groq 키 발급: https://console.groq.com/keys (무료, `gsk_...` 형태)

### 1-6. `ngrok.yml` 파일 만들기 (고정 주소용)
1. `KF-BE` 폴더의 **`ngrok.example.yml`** 을 복사해 **`ngrok.yml`** 로 이름 변경.
2. 메모장으로 열어 토큰만 채우기(도메인은 그대로 두기):
   ```yaml
   version: "2"
   authtoken: 여기에_ngrok_토큰
   tunnels:
     kf-be:
       proto: http
       addr: 8081
       domain: ngrok주소
   ```

### 1-7. IntelliJ로 프로젝트 열기
1. IntelliJ 실행 → **Open** → `KF-BE` 폴더 선택 → **Trust Project**.
2. 오른쪽 아래에 "Gradle 동기화" 진행 막대가 끝날 때까지 기다립니다(처음엔 몇 분 걸림).
3. 상단 메뉴 **File → Project Structure → Project** 에서 **SDK = 17** 인지 확인(아니면 17로 선택).

### 1-8. 서버 실행
1. 왼쪽 파일 목록에서 `src/main/java/com/kidsfriends/` → **`KfBeApplication`** 파일 더블클릭.
2. 코드 안 `public class KfBeApplication` 왼쪽의 **초록색 ▶ 버튼** 클릭 → **Run 'KfBeApplication'**.
   > `.env`는 자동으로 읽힙니다(따로 설정 불필요).

### 1-9. ✅ 성공 신호
- IntelliJ 아래 콘솔에 `Started KfBeApplication` 그리고 `[ngrok] 공개 URL: https://avengeful-...` 가 보임.
- 웹 브라우저에서 **`ngrok주소/api/health`** 열면 에러 없이 응답이 보임.

> 🛟 ngrok이 안 뜨면: 명령창에서 `ngrok config add-authtoken <토큰>` 한 번 실행 후 서버 재시작.

</details>

---

<details>
<summary>🍓 2. 라즈베리파이 — 센서(HW) 켜기 【상세】</summary>

> 컴퓨터 A의 서버가 **먼저 켜져 있어야** 합니다(1단계 완료 후 진행).

### 2-1. 터미널 열고 준비
```bash
sudo apt update
sudo apt install -y git python3-venv python3-pip
```

### 2-2. 코드 내려받기
```bash
git clone https://github.com/Kids-Friends/KF-HW.git
cd KF-HW
```

### 2-3. 파이썬 가상환경 + 라이브러리 설치
```bash
python3 -m venv .venv
source .venv/bin/activate          # 프롬프트 앞에 (.venv) 가 붙으면 성공
pip install -r requirements.txt
```

### 2-4. 카메라 SDK (실제 ToF 카메라 쓸 때만)
- CubeEye S111DU SDK를 `/home/pi/CubeEye2.0_SDK/release/python` 에 둡니다.
- 카메라/SDK가 아직 없으면 이 단계는 건너뛰고 2-6의 **연습용 실행**을 쓰세요.

### 2-5. 서버 주소 알려주기 (중요)
```bash
export KF_BE_URL="ngrok주소"
export KF_ROBOT_ID="TEMI_01"
```
> 이걸 안 넣으면 라즈베리파이가 자기 자신(localhost)으로 잘못 보냅니다.

### 2-6. 실행
- 실제 카메라/센서:
  ```bash
  python src/main.py
  ```
- 카메라 없이 연습(가짜 센서값):
  ```bash
  python tests/mock_stream.py
  ```

### 2-7. ✅ 성공 신호
- 터미널에 `백엔드 전송 대상: ngrok주소/api/sensor-events (robotId=TEMI_01)` 출력.

</details>

---

<details>
<summary>📱 3. 테미 로봇 — 앱(FE)을 Android Studio로 설치 【상세】</summary>

> 한 번만 설치하면 됩니다. 설치 후 컴퓨터 B는 꺼도 됩니다.

### 3-1. 코드 내려받기
```powershell
git clone https://github.com/Kids-Friends/KF-FE.git
```

### 3-2. Android Studio로 열기
1. Android Studio 실행 → **Open** → `KF-FE` 폴더 선택.
2. Gradle 동기화가 끝날 때까지 기다립니다.
3. ⚠️ **"Upgrade Gradle/AGP" 같은 권유 창이 뜨면 반드시 거절**(Don't upgrade / Don't remind me). 버전을 바꾸면 빌드가 깨집니다.

### 3-3. 서버 주소 확인 (보통 그대로)
- 기본값이 고정 주소 `ngrok주소` 로 이미 박혀 있어 **수정 불필요**.
- 혹시 다른 주소를 써야 하면 두 곳을 같은 주소로 바꿉니다:
  - `app/src/main/java/com/kidsFriend/global/config/AppConfig.java` 의 `DEFAULT_BASE_URL`
  - `app/build.gradle` 의 `buildConfigField ... "API_BASE_URL" ...`

### 3-4. 테미를 컴퓨터에 연결
1. 테미 화면에서 **설정 → 정보(About)** 의 빌드번호를 여러 번 눌러 **개발자 옵션** 활성화.
2. **개발자 옵션 → USB 디버깅 ON**.
3. 테미와 컴퓨터 B를 **USB 케이블**로 연결 → 테미에 뜨는 "USB 디버깅 허용?" → **허용**.

### 3-5. 설치 실행
1. Android Studio 상단 기기 선택칸에 **테미(temi)** 가 보이는지 확인.
2. **초록색 ▶(Run)** 버튼 클릭 → 빌드 후 테미에 자동 설치·실행.

### 3-6. ✅ 성공 신호
- 테미 화면에 캐릭터 얼굴 + **`"친구야" 하고 부르거나 화면을 터치해줘!`** 안내가 보임.

</details>

---

<details>
<summary>▶️ 4. 시연 진행</summary>

테미 앞에서:
1. **"친구야"** 라고 부른다 → 대화 시작.
2. 말해본다: "퀴즈 풀고 싶어" / "미끄럼틀 어디 있어?" / "회원 등록하고 싶어" / "사진 찍어줘"
3. 아이가 테미 앞에 다가가면(또는 라즈베리파이 센서 작동 시) 테미가 인사·반응.

✅ 말하는 동안 화면 **아래 실시간 자막**이 뜨고, 테미가 음성으로 답하면 정상.

</details>

---

<details>
<summary>✅ 5. 최종 점검표</summary>

- [ ] `ngrok주소/api/health` 정상 응답
- [ ] IntelliJ 콘솔에 `Started KfBeApplication` + ngrok URL
- [ ] 라즈베리파이 터미널에 "백엔드 전송 대상: …ngrok…/api/sensor-events"
- [ ] 테미 화면에 대기 안내문
- [ ] "친구야" 부르면 반응 + 하단 자막

</details>

---

<details>
<summary>🆘 6. 문제 해결</summary>

| 증상 | 조치 |
|---|---|
| `/api/health` 안 열림 | 컴퓨터 A 서버 실행 중인지, ngrok 콘솔 로그 확인 |
| 모든 API가 404 | ngrok 터널이 안 떴음 → `ngrok config add-authtoken <토큰>` 후 서버 재시작 |
| DB 오류로 서버 안 켜짐 | MySQL 켜졌는지, `.env`의 비밀번호가 MySQL과 같은지, `kf_db` 만들었는지 |
| AI 답변이 에러 | `.env`의 `AI_API_KEY`(Groq `gsk_...`) 확인 |
| 라즈베리파이가 localhost로 보냄 | `export KF_BE_URL=...` 안 했음 → 다시 설정 후 재실행 |
| 테미가 서버 못 붙음 | 테미 와이파이 연결 확인, 앱 주소가 고정 ngrok인지 확인 |
| "친구야" 인식 안 됨 | 소음 줄이기, 테미 시스템 언어 **한국어** 확인 |
| 음성 멈춤 | 테미 화면 한 번 터치 또는 앱 재시작 |

</details>

---

<details>
<summary>🔌 7. 시연 종료(끄기)</summary>

1. 컴퓨터 A: IntelliJ의 빨간 ■(Stop) 버튼 → 서버 종료.
2. 라즈베리파이: 터미널에서 `Ctrl + C`.
3. 테미: 앱 그대로 둬도 됨.

</details>

---

<details>
<summary>⚙️ (선택) 8. AI로 시연 자동 제어 — KF-MCP</summary>

개발자가 Claude/Gemini로 시연을 직접 조종·점검하고 싶을 때만 사용. 일반 시연엔 불필요.
설치/등록법은 **`KF-MCP` 저장소 README** 참고.

</details>
---
