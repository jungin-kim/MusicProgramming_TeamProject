# Music Programming Team Project 🎵

이 프로젝트는 대학교 3학년 음악 프로그래밍(Music Programming) 수업의 팀 프로젝트 결과물입니다. 
ChucK 언어를 사용하여 MIDI 기반의 음악을 연주하는 프로그램을 개발했습니다.

### 👥 팀원 및 역할 (Team & Roles)
- 김정인 (Me): 메인 개발 및 로직 설계
  - 전체 프로젝트 아키텍처 설계
  - Player 클래스 (MIDI 신호 처리 로직) 구현
  - BPM 클래스 (박자 계산 로직) 구현
  - Score 시퀀싱 및 스레드(Shred) 관리

- 김가연: 음악 데이터 작성
  - 악보를 바탕으로 각 파트별(High/Low) 음표(Note) 및 길이(Duration) 데이터 배열 작성
  - 일부 파트의 하모니 구성

### 🛠 개발 환경 및 기술 (Tech Stack)
- Language: ChucK (Strongly-timed, Concurrent, and On-the-fly Music Programming Language)

- Protocol: MIDI (Musical Instrument Digital Interface)

- Output: MIDI Output (Requires a virtual instrument or external MIDI device)

### 📂 파일 구조 및 로직 설명 (Project Structure & Logic)
이 프로젝트는 객체 지향적 설계와 ChucK의 동시성(Concurrency) 기능을 적극 활용하여 모듈화되어 있습니다.

1. 핵심 로직 (Core Logic)
- Score.ck (Main Entry Point)

전체 곡의 흐름을 지휘하는 지휘자(Conductor) 역할을 합니다.
Machine.add()를 사용하여 필요한 클래스 파일(BPM.ck, Player.ck)을 먼저 로드합니다.
이후 시간 순서에 따라 High*.ck, Low*.ck 파일들을 스케줄링하여 순차적으로 실행합니다.

- Player.ck (MIDI Interface Class)
  - 역할: MIDI 출력을 추상화한 클래스로, 음표 배열을 받아 실제로 소리를 내는 엔진입니다.
  - 구현 원리:
   MidiOut, MidiMsg 객체를 사용하여 포트 1번으로 신호를 보냅니다.
   play(int notes[], dur duration[]): 음표 배열과 길이 배열을 인자로 받아 for 루프를 돌며 연주합니다.
   playNote(): Velocity(타건 강도)를 조절하여 NoteOn(144)과 NoteOff(128) 메시지를 전송합니다. 묵음(-1) 처리 로직이 포함되어 있습니다.

- BPM.ck (Timing Helper)
  - 역할: 곡의 빠르기(Tempo)를 전역적으로 관리합니다.
  - 구현 원리: tempo(float beat) 함수를 통해 BPM을 설정하면, 이를 기준으로 4분 음표, 8분 음표, 16분 음표 등의 길이를 dur 타입으로 계산하여 제공합니다.

2. 음악 데이터 및 연주 (Music Data & Performance)
- High[1-6].ck & Low[1-3].ck (Part Files)
  - 각 파일은 곡의 특정 구간(Section)을 담당합니다.
  - 데이터 구조:
   - int H1[]: MIDI 노트 번호가 담긴 정수 배열입니다.
   - dur H1D[]: 각 노트의 길이가 담긴 시간 배열입니다.
  - 동시성 구현 (Concurrency):
   - spork ~ p.play(...) 문법을 사용하여 여러 성부(Melody, Harmony)를 동시에 독립적인 스레드(Shred)로 실행합니다. 이를 통해 단일 파일 내에서도 화음을 연주할 수 있도록 구현했습니다.

### 🚀 실행 방법 (How to Run)
1. ChucK 실행 환경(miniAudicle 등)을 준비합니다.

2. 가상 MIDI 포트(예: LoopBe1, IAC Driver)나 외부 신디사이저가 연결되어 있는지 확인합니다.

3. Score.ck 파일을 실행합니다.
   ```bash
    // 터미널에서 실행 시
    chuck Score.ck
   
### 📝 개발 후기 (Conclusion)
이 프로젝트를 통해 ChucK 언어의 강력한 타이밍 제어 기능과 spork를 이용한 병렬 처리를 깊이 있게 이해할 수 있었습니다. 특히 반복되는 MIDI 전송 코드를 Player 클래스로 모듈화하여, 악보 데이터를 작성하는 팀원이 로직에 신경 쓰지 않고 음표 배열 작성에만 집중할 수 있도록 협업 효율을 높였습니다.
