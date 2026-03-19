---
marp: true
class: invert
paginate: false
style: |
  section {
    font-family: 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 40px 70px;
  }
  h1 {
    font-size: 1.6rem;
    margin-bottom: 0.15rem;
    line-height: 1.3;
  }
  .meta {
    color: #888;
    font-size: 0.78rem;
    margin-bottom: 1.4rem;
  }
  .divider {
    border: none;
    border-top: 1px solid #444;
    margin: 0.3rem 0 1.4rem 0;
  }
  .agenda-item {
    display: flex;
    align-items: flex-start;
    margin-bottom: 1rem;
    gap: 1rem;
  }
  .num {
    font-size: 1.3rem;
    font-weight: bold;
    color: #7ee787;
    min-width: 2rem;
    line-height: 1.2;
  }
  .content h3 {
    margin: 0 0 0.15rem 0;
    font-size: 0.95rem;
  }
  .content p {
    margin: 0;
    color: #aaa;
    font-size: 0.76rem;
    line-height: 1.5;
  }
  .footer {
    margin-top: 1.4rem;
    color: #555;
    font-size: 0.7rem;
    border-top: 1px solid #333;
    padding-top: 0.6rem;
  }
---

# 반복 업무를 AI로 자동화한<br>사례 3가지

<div class="meta">백엔드개발실 · 이규성 · 2026</div>

<hr class="divider" />

<div class="agenda-item">
  <div class="num">01</div>
  <div class="content">
    <h3>df-meetnote — 회의록 자동화</h3>
    <p>회의 녹음 파일을 올리면 회의록이 자동으로 나오는 사내 도구.<br>WebAssembly(ffmpeg.wasm) + OpenAI Whisper + GPT, 백엔드 서버 없이 구현.</p>
  </div>
</div>

<div class="agenda-item">
  <div class="num">02</div>
  <div class="content">
    <h3>daily-reports — 일간보고 자동화</h3>
    <p>Claude Code 슬래시 명령어(<code>/daily-report</code>)로 보고서 초안을 자동 생성.<br>Git 기반으로 팀 전체 보고서를 관리하는 구조.</p>
  </div>
</div>

<div class="agenda-item">
  <div class="num">03</div>
  <div class="content">
    <h3>telegram-mcp — AI와 Telegram 연결</h3>
    <p>MCP(Model Context Protocol)로 Claude가 Telegram을 직접 조작.<br>보고서 생성부터 팀 채널 공유까지 한 번에.</p>
  </div>
</div>

<div class="footer">세 가지 모두 팀에서 실제로 사용 중인 도구입니다.</div>
