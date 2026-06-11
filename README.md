# 🧪 CHEMIBOT — 로봇팔을 활용한 영상처리와 신경망 알고리즘 개발

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/C%23-239120?style=flat&logo=csharp&logoColor=white"/>
  <img src="https://img.shields.io/badge/WPF-512BD4?style=flat&logo=dotnet&logoColor=white"/>
  <img src="https://img.shields.io/badge/React-61DAFB?style=flat&logo=react&logoColor=black"/>
  <img src="https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white"/>
  <img src="https://img.shields.io/badge/Raspberry%20Pi-A22846?style=flat&logo=raspberrypi&logoColor=white"/>
  <img src="https://img.shields.io/badge/MediaPipe-4285F4?style=flat&logo=google&logoColor=white"/>
  <img src="https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white"/>
  <img src="https://img.shields.io/badge/Arduino-00979D?style=flat&logo=arduino&logoColor=white"/>
</p>

<p align="center">
  MediaPipe 손동작 인식 · 시선 추적 커서 제어 · 얼굴 인증 · myCobot 로봇팔 연동 · WPF 모니터링 · React 관리자 화면을 통합한<br/>
  시선과 제스처 기반 화학 실험 자동화 시스템입니다.
</p>

---

## 📌 프로젝트 개요

시선 추적으로 실험실 구역을 선택하고, 손동작 제스처로 로봇팔(myCobot)을 제어하여 시약 집기·붓기·섞기 등 화학 실험 과정을 자동화하는 시스템입니다.

MediaPipe Hands 기반 9가지 제스처를 RandomForest로 분류하고, 시선 추적 커서로 시약대·거치대·비커 등 실험 구역을 Dwell 방식으로 선택합니다. Raspberry Pi Flask 서버를 통해 로봇팔을 HTTP로 제어하며, WPF 모니터링 화면에서 실험실 배치도·카메라 영상·환경 센서를 실시간 확인할 수 있습니다.

가스 누출·쓰러짐 감지·비상버튼 등 5종 비상 이벤트를 통합 처리하고, 서보모터 기반 자동 도어 잠금 및 환풍기 제어까지 포함한 안전 시스템을 구축하였습니다.

---

## ⚙️ 주요 기능

### 손동작 제스처 인식 (Python)

| 기능 | 설명 |
|------|------|
| **특징값 추출** | MediaPipe Hands 랜드마크 21개에서 펼침 비율·거리 비율·기울기 등 16개 특징값 추출 |
| **제스처 분류** | 9가지 제스처(GRAB·RELEASE·POUR·SHAKE·STOP·1~4) RandomForest 300트리 학습 |
| **오인식 방지** | 2초 투표 상태 머신(IDLE→MEASURING→EXECUTE), 신뢰도 60% 임계값 적용 |
| **정규화** | 손 크기(hand_size) 기반 좌표 정규화로 카메라 거리 영향 제거 |

### 시선 추적 및 구역 선택 (Python)

| 기능 | 설명 |
|------|------|
| **얼굴 트래킹 커서** | MediaPipe Face Mesh 기반 머리 방향 추정, 화면 좌표 매핑 |
| **캘리브레이션** | 사용자별 5포인트 캘리브레이션, .npy 파일로 저장·자동 로드 |
| **구역 Dwell 선택** | 커서가 구역 위 0.8초 체류 시 선택, 쿨다운 1.5초 적용 |
| **2단계 선택** | STAGE1: 큰 구역(시약대/A거치대/B거치대/비커) → STAGE2: 세부 슬롯(1~4) |

### 로봇팔 제어 (Raspberry Pi + Python)

| 기능 | 설명 |
|------|------|
| **수직/수평 집기** | 시약대는 수직 집기, A/B 거치대는 수평 집기 자동 판단 |
| **붓기 시퀀스** | 비커 이동 → POUR 제스처 대기 → 기울여 붓기 → 홈 자동 복귀 |
| **섞기 5단계** | 막대 이동→잡기→비커 이동→섞기→놓기 단계별 상태 전환 |
| **순차 실행** | robot.playing 상태 폴링으로 동작 완료 후 다음 명령 전송 보장 |
| **인접 슬롯 검증** | can_drop_at()으로 옆 집기 공간 확보, 차단 슬롯 자동 숨김 |

### WPF 모니터링 화면 (C#)

| 기능 | 설명 |
|------|------|
| **얼굴 인증** | 128차원 벡터 기반 인증(유클리드 거리 0.4 이하), 인증 정확도 93% |
| **화면 전환** | 얼굴 인증 → 메인 → 실험 진행 3단계 자동 전환 |
| **실시간 영상** | 실험실 조감캠·손동작 카메라 PiP(Picture-in-Picture) 전환 |
| **배치도 Canvas** | 시약대·A/B거치대·비커·막대 슬롯을 Pi 폴링으로 실시간 색상 표시 |
| **사이드바** | 외부문 잠금/해제·비상정지·일시정지·종료·리셋 버튼 |
| **실험 이력** | 이력 팝업 8개씩 페이징, 소요 시간 자동 계산 |

### React 웹 관리자 화면

| 기능 | 설명 |
|------|------|
| **대시보드** | 연구원 관리·실험 이력·시스템 설정 4개 탭 라우팅 |
| **얼굴 등록** | 웹캠 10초 촬영 → 128차원 벡터 MySQL BLOB 직렬화 저장 |
| **기준값 설정** | 가스·온도·습도 경고 임계값 설정 및 저장 |

### 안전 시스템

| 기능 | 설명 |
|------|------|
| **5종 비상 트리거** | 가스 누출(임계값 400)·비상버튼(NC)·쓰러짐 감지·화면 E-Stop·손동작 긴급 정지 |
| **비상 동시 실행** | 감지 즉시 로봇 정지·부저·LCD 경고·대피 안내 음성 동시 실행 |
| **다계층 알림** | Pi → WPF 모니터링 PC → 보안실 순차 전파 (TCP/UDP) |
| **자동 도어 잠금** | 서보모터 3개(내부문 2, 외부 잠금 1), 실험 시작 시 자동 잠금·시약 접근 시 해제·복귀 후 재잠금 |
| **환풍기 제어** | 가스 초과 시 자동 가동, 종료 후 3분 환기 카운트다운, 강제종료 시 즉시 정지 |
| **비상 이력** | MySQL 자동 적재(원인·시각·소요시간) 및 API 조회 |

---

## 🤚 제스처 정의

| 제스처 | 손모양 | 동작 | 용도 |
|--------|--------|------|------|
| **GRAB** | 주먹 | 그리퍼 닫기 | 시험관·막대 잡기 |
| **RELEASE** | 엄지 + 검지 | 그리퍼 열기 | 시험관·막대 놓기 |
| **POUR** | 엄지만 펴기 | 기울여 붓기 | 비커에 시약 붓기 |
| **SHAKE** | 엄지 + 새끼 | 섞기 동작 | 비커 내 용액 섞기 |
| **STOP** | 보자기 (5손가락) | 긴급 정지 | 로봇 즉시 정지 |
| **1** | 검지 1개 | 슬롯 1 선택 | 세부 위치 지정 |
| **2** | 검지 + 중지 | 슬롯 2 선택 | 세부 위치 지정 |
| **3** | 검지 + 중지 + 약지 | 슬롯 3 선택 | 세부 위치 지정 |
| **4** | 4손가락 | 슬롯 4 선택 | 세부 위치 지정 |

---

## 🔌 Pi 서버 엔드포인트

### 상태 조회

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/status` | GET | 로봇 동작 상태 (busy/idle) |
| `/state` | GET | 시험관 현재 위치 (action_log 역산) |

### 집기 (Pickup)

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/pickup_move/<num>` | GET | 시약통 위치로 수직 이동 (GRAB 대기) |
| `/pickup_grip` | GET | 그리퍼 닫기 + 수직 홈 복귀 |
| `/pickup_grip_lift` | GET | 그리퍼 닫기 + 수평 홈 복귀 |
| `/pickup_lift_move/<slot>` | GET | A/B 슬롯 수평 집기 이동 (GRAB 대기) |
| `/pickup_lift/<slot>` | GET | A/B 슬롯 수평 집기 |

### 꽂기 (Drop)

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/drop_move/<slot>` | GET | 슬롯 위치로 수직 이동 (RELEASE 대기) |
| `/drop_release` | GET | 그리퍼 열기 + 복귀 |
| `/side_drop_move/<slot>` | GET | 슬롯 위치로 수평 이동 (RELEASE 대기) |
| `/side_drop_release` | GET | 수평 놓기 + 복귀 |

### 붓기 (Pour)

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/beaker_move` | GET | 비커 위치로 이동 (POUR 대기) |
| `/beaker_pour` | GET | 기울여 붓기 + 수평 홈 복귀 |
| `/pour/<slot>` | GET | 전체 시퀀스 (집기→붓기→꽂기) |

### 섞기 (Stir)

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/stir_move` | GET | 막대 위치로 이동 (GRAB 대기) |
| `/stir_grip` | GET | 막대 잡기 + 홈 복귀 |
| `/stir_beaker_move` | GET | 비커 위치로 이동 (SHAKE 대기) |
| `/stir_do` | GET | 섞기 동작 + 홈 복귀 |
| `/stir_drop_move` | GET | 막대 원위치 이동 (RELEASE 대기) |
| `/stir_drop_release` | GET | 막대 놓기 + 홈 복귀 |

### 제어

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/home` | GET | 수직 홈 복귀 |
| `/home_lift` | GET | 수평 홈 복귀 |
| `/grip/close` | GET | 그리퍼 닫기 |
| `/grip/open` | GET | 그리퍼 열기 |
| `/stop` | GET | 긴급 정지 |
| `/reset` | GET | action_log 역순 시험관 복구 |
| `/clear_log` | GET | 실험 로그 초기화 |

---

## 🔄 시스템 구조

```
[Arduino]                [Raspberry Pi (myCobot)]          [WPF 모니터링 PC]              [보안실 PC]
 가스/온습도 센서  ─Serial→  Flask 서버 (5001)  ←─HTTP─→  Flask 클라이언트              보안실 WPF
 서보모터 3개     ←────────  로봇팔 제어                   얼굴 인증 / 배치도
                            시험관 상태 관리               카메라 PiP 전환
                                 ↕ TCP                        ↕ TCP
                          [gesture_control]             [Zone_tracker]
                           제스처 인식 (9003)  ─────→   구역 선택 + 로봇 명령
                                                             ↑
                                                      [Learning_TWM]
                                                       시선 커서 (9002)
```

### 통신 포트 구조

| 포트 | 프로토콜 | 방향 | 용도 |
|------|----------|------|------|
| 5000 | HTTP | 양방향 | Flask A (로컬 서버) |
| 5001 | HTTP | 양방향 | Pi Flask B (로봇 제어·상태 조회) |
| 9002 | TCP | 단방향 | 시선 커서 좌표 (Learning_TWM → Zone_tracker) |
| 9003 | TCP | 단방향 | 제스처 전송 (gesture_control → Zone_tracker) + 서보 제어 |
| 9005 | TCP | 단방향 | 비상 신호 (Pi → WPF) |
| 9998 | UDP | 양방향 | 비상/해제 신호 |
| 9999 | TCP | 단방향 | 카메라 스트림 |
| 10000 | UDP | 양방향 | 인터콤 |

### 데이터 흐름

> **시약 집기** : 시선 커서(9002) → Zone_tracker 구역 선택 → 제스처(9003) 슬롯 선택 → HTTP → Pi 로봇팔 이동 → GRAB 제스처 → 집기 완료

> **붓기** : 비커 구역 Dwell 선택 → beaker_move → POUR 제스처 → beaker_pour → Pi가 기울여 붓기 + 홈 복귀

> **비상 정지** : 가스/버튼/쓰러짐/제스처 감지 → Pi(9005) → WPF 화면 전환 + 로봇 정지 + 부저 + 음성 → 보안실 전파

---

## 🗄️ DB 구조

### MySQL

```
researchers (연구원)
├── id              INT PRIMARY KEY AUTO_INCREMENT
├── name            VARCHAR(50)
├── face_vector     BLOB            -- 128차원 벡터 pickle 직렬화
└── created_at      DATETIME

experiments (실험 이력)
├── id              INT PRIMARY KEY AUTO_INCREMENT
├── researcher_id   INT FK
├── start_time      DATETIME
├── end_time        DATETIME
└── duration_sec    INT             -- 소요 시간 자동 계산

emergencies (비상 이력)
├── id              INT PRIMARY KEY AUTO_INCREMENT
├── cause           VARCHAR(100)    -- 가스누출 / 쓰러짐 / 비상버튼 / E-Stop
├── occurred_at     DATETIME
└── duration_sec    INT

sensor_settings (환경 기준값)
├── gas_threshold   INT DEFAULT 400
├── temp_warning    FLOAT
└── humi_warning    FLOAT
```

---

## 📁 프로젝트 구조

```
CHEMIBOT/
├── gesture_learning/
│   ├── gesture_control_v6.py       # 제스처 인식 + 로봇 제어
│   ├── Zone_tracker.py             # 시선 구역 선택 + 로봇 명령
│   ├── robot_controller.py         # Pi HTTP 클라이언트
│   ├── zone_data.json              # 구역 좌표 정의
│   ├── camera_finder.py            # 카메라 동적 탐색
│   ├── camera_indices.json         # 카메라 매핑 (face/lab/gesture)
│   └── models/
│       ├── gesture_model_v6.pkl    # 학습된 제스처 모델
│       └── confusion_matrix.png    # 혼동행렬 (테스트 결과)
│
├── face_tracking/
│   ├── Learning_TWM.py             # 시선 추적 커서
│   ├── calib.py                    # 캘리브레이션
│   ├── utils.py                    # 공통 함수
│   └── calib_data/                 # 사용자별 .npy 파일
│
├── pi_server/
│   ├── chemibot_server.py        # Pi Flask 서버 (로봇 제어 + 상태)
│   └── chemibot.service          # systemd 자동실행 설정
│
├── flask_server/
│   ├── app.py                      # Flask 메인 서버
│   └── templates/                  # 관리자 웹 페이지
│
├── wpf/
│   ├── MainWindow.xaml/.cs         # WPF 메인 윈도우
│   ├── Views/
│   │   ├── FaceAuthView.xaml/.cs   # 얼굴 인증 화면
│   │   ├── MainView.xaml/.cs       # 메인 화면
│   │   └── RunningView.xaml/.cs    # 실험 진행 화면
│   ├── PythonProcessManager.cs     # Python 프로세스 관리 (좀비 정리)
│   └── FlaskClient.cs              # Flask API 클라이언트
│
├── react_admin/                    # React 웹 관리자
│
├── setup.bat                       # 다중 PC 배포 자동화 (6단계)
└── requirements.txt
```

---

## 🛠️ 기술 스택

| 분류 | 기술 |
|------|------|
| **제스처 인식** | Python, MediaPipe Hands, scikit-learn (RandomForest), OpenCV |
| **시선 추적** | Python, MediaPipe Face Mesh |
| **얼굴 인증** | Python, face_recognition, dlib 19.24.1 |
| **WPF 모니터링** | C#, WPF, OpenCvSharp, System.Management |
| **웹 관리자** | React, JavaScript |
| **서버** | Python, Flask |
| **로봇팔** | myCobot 280 Pi, pymycobot |
| **하드웨어** | Raspberry Pi 4, Arduino, 가스 센서, 온습도 센서, 서보모터 3개 |
| **통신** | TCP/UDP Socket, HTTP REST API |
| **데이터베이스** | MySQL |
| **배포** | setup.bat 자동화, venv, systemd |

---

## 🔧 환경 설정 및 실행

### 새 PC 셋업

```
1. Python 3.11 설치 (Add to PATH)
2. 외장 USB 카메라 3개 연결 (face / lab / gesture)
3. 프로젝트 + wheels/dlib-cp311.whl 복사
4. setup.bat 실행 (venv 생성 + 의존성 설치, 5~10분)
5. python calib.py --name 영문이름    → 캘리브레이션
6. name_map.json에 한글→영문 매핑 추가
7. 얼굴 등록 (Flask 관리자 페이지)
8. WPF 실행 → 얼굴 인증 → 실험 시작
```

### Pi 서버 (192.168.0.32)

```bash
# systemd 자동실행 등록
sudo systemctl enable chemibot.service
sudo systemctl start chemibot.service

# 수동 실행
cd ~/chemibot && python chemibot_server.py
```

### 네트워크

| 장비 | IP |
|------|-----|
| myCobot Pi | 192.168.0.32 |
| 관제실 PC | 192.168.0.25 |
| 보안실 PC | 192.168.0.6 |

---

## 👥 팀 구성

| 이름 | 역할 | 담당 |
|------|------|------|
| 김규대 | 팀장 | 프로젝트 총괄 |
| **김수영** | **팀원** | **제스처 인식 설계 및 학습, 로봇팔 제어 연동, WPF 모니터링 UI 설계 및 구현, 얼굴 인증, React 웹 관리자, 안전 시스템 연동** |
| 정서현 | 팀원 | 하드웨어 제어, FSM 설계, 서보모터 도어 제어, 보안실 WPF |
| 강은비 | 팀원 | Flask 서버, WebSocket 통신, 웹 UI |
| 김민준 | 팀원 | 시선 추적 커서, 얼굴 캘리브레이션, WPF 통합, 다중 PC 배포 |
