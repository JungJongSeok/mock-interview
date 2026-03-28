# mock-interview

Resume-based mock interview plugin for Claude Code.

이력서 기반 모의 면접 플러그인.

## Installation / 설치

```bash
# 1. Add marketplace / 마켓플레이스 등록
/plugin marketplace add JungJongSeok/mock-interview

# 2. Install plugin / 플러그인 설치
/install mock-interview
```

## Usage / 사용법

```bash
/mock-interview <resume file path or text>
```

Or invoke without arguments and follow the prompts.

또는 인자 없이 실행 후 안내에 따라 진행하세요.

## Features / 기능

### Language / 언어
- Korean / English selectable at start

### Modes / 모드

| Mode | Description |
|------|-------------|
| Interviewee (면접자) | AI interviews you. You answer questions. |
| Question Generator (질문 생성) | AI generates a structured question set with evaluation criteria. |
| Mock Interviewer (모의 면접관) | AI plays the candidate. You practice as the interviewer. |

### Question Presets / 질문 프리셋

| Preset | Duration | Questions |
|--------|----------|-----------|
| Quick | 15 min | Tech 3 + Behavioral 1 = 4 |
| Standard | 30 min | Tech 5 + Behavioral 2 = 7 |
| Deep | 45 min | Tech 7 + Behavioral 3 = 10 |

### Supported Roles / 지원 직군

- **Developers**: Android, iOS, Flutter, Java Backend, Node.js, React+TS, React Native, AI Agent
- **Non-developers**: PO, Product Manager

### Key Features / 핵심 기능

- Resume analysis with auto-detection of role, level, and tech stack
- Why-focused follow-up questions (max 2 per question)
- Immediate feedback after each answer with interviewer tips
- Adaptive difficulty (1-5 scale)
- Comprehensive results summary with actionable next steps

## License / 라이선스

Apache-2.0
