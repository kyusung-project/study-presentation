---
marp: true
class: invert
paginate: true
style: |
  section {
    font-family: 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
  }
  section.title {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
  }
  section.title h1 {
    font-size: 2.2rem;
    margin-bottom: 0.5rem;
  }
  section.title p {
    color: #aaa;
    font-size: 1rem;
  }
  section.chapter {
    display: flex;
    flex-direction: column;
    justify-content: center;
    background: #1a1a2e;
  }
  section.chapter h1 {
    font-size: 2.8rem;
  }
  section.chapter p {
    color: #aaa;
    font-size: 1.1rem;
  }
  .highlight {
    color: #7ee787;
    font-weight: bold;
  }
  .sub {
    color: #aaa;
    font-size: 0.85rem;
  }
  code {
    font-size: 0.85rem;
  }
---

<!-- _class: title -->

# 개발팀 루틴을 AI로 바꾼<br>3가지 이야기

백엔드개발실 · 이규성
2026

---

## 오늘 이야기할 것

<br>

| | 도구 | 해결한 문제 |
|---|---|---|
| **1** | df-meetnote | 회의 후 회의록 작성이 귀찮다 |
| **2** | daily-reports | 매일 일간보고 쓰는 게 반복이다 |
| **3** | telegram-mcp | AI랑 Telegram이 단절되어 있다 |

<br>

> 세 가지 모두 **팀에서 실제로 쓰는 도구**입니다

---

## 공통된 출발점

<br>

개발자는 코드만 짜지 않는다

<br>

- 회의 → 회의록 작성
- 매일 → 일간보고 작성
- AI 쓰다가 → Telegram으로 전달

<br>

**반복되는 업무를 자동화하면 어떨까?**
직접 만들면 팀에 딱 맞는다

---

<!-- _class: chapter -->

# 01

## df-meetnote

**회의록, 이제 자동으로 나옵니다**

---

## 문제

<br>

회의가 끝나면 누군가 회의록을 써야 한다

<br>

- 회의 중엔 기록에 집중하기 어렵다
- 끝나고 나면 이미 기억이 흐릿하다
- 그렇다고 녹음만 해두면 아무도 안 듣는다

<br>

→ **녹음 파일을 올리면 알아서 회의록이 나오면?**

---

## 어떻게 동작하나요

<br>

```
[오디오 업로드]
    ↓
[10분 단위로 쪼개기]  ← ffmpeg.wasm (브라우저에서)
    ↓
[텍스트로 변환]       ← OpenAI Whisper API
    ↓
[회의록 생성]         ← GPT-4o
    ↓
[.txt / .md 다운로드]
```

---

## 핵심: 서버가 없다

<br>

보통 이런 서비스를 만들면

```
사용자 → 서버 → 오디오 처리 → AI API → 결과 반환
```

<br>

df-meetnote는

```
사용자 브라우저 → (ffmpeg.wasm으로 로컬 처리) → AI API → 결과
```

<br>

**서버 비용 0, 배포는 AWS Amplify 정적 호스팅**

---

## WebAssembly가 뭔가요

<br>

브라우저에서 C/C++ 수준의 성능으로 코드를 실행하는 기술

<br>

ffmpeg는 원래 서버에서 돌리는 영상/음성 처리 라이브러리

→ **ffmpeg.wasm**: 이걸 브라우저에서 그대로 실행

```js
const ffmpeg = new FFmpeg();
await ffmpeg.load();

// 브라우저 안에서 오디오를 10분 단위로 자르기
await ffmpeg.exec(['-i', 'input.m4a', '-t', '600', 'chunk_0.m4a']);
```

<br>

<span class="sub">서버 없이 무거운 미디어 처리가 가능해집니다</span>

---

## 결과

<br>

- 지원 형식: m4a, mp3, mp4, wav, ogg, flac
- 인증: Google 로그인 (@danalfintech.com 도메인 제한)
- 현재 사내에서 실제 사용 중

<br>

**Before**: 회의 끝 → 30분 회의록 작성
**After**: 회의 끝 → 파일 업로드 → 5분 후 회의록 완성

---

<!-- _class: chapter -->

# 02

## daily-reports

**일간보고, 명령어 하나로 끝냅니다**

---

## 문제

<br>

매일 일간보고를 써야 한다

<br>

- 오늘 뭐 했는지 생각하고 정리하는 게 귀찮다
- 보고서 형식 맞추는 데 시간이 든다
- 사실 Jira, Confluence, 회의록에 다 기록이 있다

<br>

→ **이미 있는 데이터로 초안을 자동 생성하면?**

---

## Claude Code 슬래시 명령어

<br>

Claude Code는 프로젝트 안에 커스텀 명령어를 만들 수 있다

<br>

```
.claude/
  commands/
    daily-report.md   ← /daily-report 명령어 정의
    meeting-prep.md   ← /meeting-prep 명령어 정의
```

<br>

```bash
# 이렇게만 치면
/daily-report

# Claude가 알아서 오늘 보고서 초안을 만들어줌
```

---

## 어떻게 동작하나요

<br>

```
/daily-report 실행
    ↓
오늘 날짜 기준으로 Jira 이슈, 회의록, Git 커밋 참고
    ↓
팀별 템플릿에 맞춰 초안 생성
    ↓
파일 저장 → git commit & push
```

<br>

추가로 `/meeting-prep` 명령어로 본부회의 요약본도 자동 생성

---

## Git 기반 보고서 관리

<br>

보고서를 Git으로 관리하면

- 누가 언제 뭘 했는지 히스토리가 남는다
- PR/머지 없이 `git push` 하나로 공유
- 충돌을 최소화하는 날짜별 폴더 구조

```
daily-reports/
  devs/2026/03/19/한규현.md
  blockchain/2026/03/19/최성용.md
  lead/2026/03/19/이규성.md
```

<br>

<span class="sub">Rebase 방식으로 항상 깔끔한 히스토리 유지</span>

---

## 결과

<br>

- 보고서 작성 시간: 약 15분 → 3분 (초안 검토만)
- 팀 전체 보고서 히스토리를 Git에서 한눈에 확인
- 본부회의 요약본도 자동 생성으로 준비 시간 단축

<br>

**핵심**: AI가 초안을 쓰고, 사람이 검토하고 수정한다

---

<!-- _class: chapter -->

# 03

## telegram-mcp

**Claude가 Telegram을 직접 다룹니다**

---

## 문제

<br>

AI 도구와 사내 메신저(Telegram)가 단절되어 있다

<br>

- AI한테 "이 내용 팀에 공유해줘" → 직접 복붙해서 보내야 함
- 보고서 생성 → 텔레그램 공유까지 자동화하고 싶다
- AI가 Telegram 메시지도 읽고 쓸 수 있으면?

<br>

→ **MCP로 연결하면 된다**

---

## MCP가 뭔가요

<br>

**Model Context Protocol** — Anthropic이 만든 AI 연동 표준

<br>

```
AI (Claude/Cursor)
    ↕  MCP 프로토콜
외부 도구 (Telegram, Jira, DB, 파일시스템...)
```

<br>

AI가 외부 서비스를 **직접 호출**할 수 있게 해주는 연결 규격

<br>

<span class="sub">USB-C 같은 것 — 표준이 있으면 어디든 꽂힌다</span>

---

## telegram-mcp 구조

<br>

```python
# MCP 서버로 등록하면
# Claude가 이런 도구들을 직접 사용할 수 있게 됨

tools = [
    "send_message",      # 메시지 전송
    "get_history",       # 대화 기록 조회
    "search_messages",   # 메시지 검색
    "get_participants",  # 그룹 멤버 조회
    # ... 100+ 도구
]
```

<br>

Telegram API 전체를 MCP 도구로 구현 → Claude에 연결

---

## 실제로 어떻게 쓰나요

<br>

Claude Code에서

```
"오늘 daily report 만들고 S팀 텔레그램 방에 공유해줘"
```

<br>

→ Claude가 알아서

1. 일간보고 초안 생성
2. 파일 저장
3. `send_message`로 텔레그램 방에 전송

<br>

**사람이 할 건 명령 한 줄**

---

## MCP 서버 설정 방법

<br>

Claude Code `settings.json`에 등록만 하면 끝

```json
{
  "mcpServers": {
    "telegram": {
      "command": "python",
      "args": ["/path/to/telegram-mcp/server.py"],
      "env": {
        "TELEGRAM_API_ID": "...",
        "TELEGRAM_API_HASH": "..."
      }
    }
  }
}
```

<br>

<span class="sub">Docker로도 실행 가능 — 팀 단위 배포에 용이</span>

---

## 결과

<br>

- Telegram ↔ Claude 완전 연동
- 보고서 작성 → 공유까지 한 번에
- MCP 표준이라 Jira, Confluence, DB 등 다른 서비스도 동일한 방식으로 연결 가능

<br>

**AI가 단순 텍스트 생성기에서 → 업무 실행자로**

---

<!-- _class: chapter -->

# 정리

---

## 세 가지의 공통점

<br>

| | 문제 | 해결 방식 |
|---|---|---|
| df-meetnote | 회의록 작성 | AI + WebAssembly |
| daily-reports | 일간보고 작성 | AI + slash command |
| telegram-mcp | AI-메신저 단절 | MCP 프로토콜 |

<br>

공통 원칙: **반복되는 것을 자동화, 판단은 사람이**

---

## 직접 만드는 이유

<br>

시중 도구들도 많지만

<br>

- 우리 팀 워크플로우에 딱 맞지 않는다
- 사내 보안/인증 정책 적용이 어렵다
- 도구를 만드는 과정 자체가 공부다

<br>

> **팀 상황을 가장 잘 아는 건 팀 안에 있는 개발자**

---

## 참고

<br>

- **df-meetnote** — ffmpeg.wasm + OpenAI Whisper + GPT
- **daily-reports** — Claude Code slash commands & hooks
- **telegram-mcp** — Python + Telethon + MCP SDK
  - [modelcontextprotocol.io](https://modelcontextprotocol.io)

<br>

도구 사용 문의나 도입 관련 이야기는 편하게 연락주세요

---

<!-- _class: title -->

# 감사합니다

백엔드개발실 · 이규성
