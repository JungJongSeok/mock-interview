---
name: mock-interview
description: 이력서 기반 모의 면접. 면접자/면접관 모드 지원, 기술+인성 질문, Why 후속 질문과 즉시 피드백 제공.
user-invokable: true
argument-hint: "<이력서 파일경로 또는 이력서 텍스트>"
---

# mock-interview - Resume-Based Mock Interview

Resume-based mock interview skill. AI analyzes the resume, then conducts a realistic interview with "why" follow-ups and immediate tips.

## Instructions

### Step 0: Language Selection

Ask the user to choose a language using AskUserQuestion:

- Korean (한국어로 면접을 진행합니다)
- English (Conduct the interview in English)

Use the selected language for all communication. Code and technical keywords stay in English.

### Step 1: Resume Input

**If ARGUMENTS were provided when invoking the skill:**

Check the ARGUMENTS value:
1. If it looks like a file path (contains `/`, `\`, or file extension like `.pdf`, `.md`, `.txt`, `.docx`): Read the file using Read tool and use as resume.
2. Otherwise: Treat the ARGUMENTS value as pasted resume text directly.

Skip the input method question and proceed to Step 2 immediately.

**If NO ARGUMENTS were provided:**

Ask the user using AskUserQuestion:

- 텍스트 입력 (이력서 내용을 직접 붙여넣기) / Text input (Paste resume content directly)
- 파일 경로 입력 (로컬 파일 경로 입력) / File path (Enter local file path)

If file path: Read the file using Read tool (supports PDF, MD, TXT, etc.).
If text: Accept the pasted content directly.

### Step 2: Resume Analysis

Analyze the resume and present a summary to the user for confirmation:

```
Resume Analysis
================================================
Role: {Developer (domain) / PO / Product Manager}
Level: {Junior / Mid / Senior}
Experience: {N years}
Tech Stack: {extracted technologies}
Key Projects: {notable projects/experiences}
Interview Focus Areas: {areas to probe based on resume}
================================================
```

**Auto-detection rules:**
- **Role**: Determine from job titles, skills, and project descriptions
  - Developer domains: Android, iOS, Flutter, Java Backend, Node.js, React+TS, React Native, AI Agent, etc.
  - Non-developer: PO, Product Manager, Project Manager, Designer, etc.
- **Level**: Infer from total experience years + project scope + role descriptions
  - Junior: 0-2 years or entry-level descriptions
  - Mid: 3-5 years or independent project ownership
  - Senior: 6+ years or team leadership / architecture decisions
- **Tech Stack**: Extract all mentioned languages, frameworks, tools, services
- **Key Projects**: Identify 2-3 most significant projects to ask about

Ask user to confirm or correct the analysis before proceeding.

### Step 3: Role Selection

Ask the user to choose a mode using AskUserQuestion:

- 면접자 모드 (Interviewee): AI가 면접관 역할. 사용자가 답변합니다. / AI is the interviewer. You answer questions.
- 면접관 모드 - 질문 생성 (Question Generator): AI가 이력서 기반 질문 목록을 생성합니다. / AI generates a structured question set with evaluation criteria.
- 면접관 모드 - 모의 면접 (Mock Interviewer): AI가 지원자 역할. 사용자가 면접관으로 질문합니다. / AI plays the candidate. You ask questions.

### Step 4: Question Preset

Ask the user to choose a preset using AskUserQuestion:

- Quick (15분/min): 기술 3 + 인성 1 = 4문항 / Tech 3 + Behavioral 1 = 4 questions
- Standard (30분/min): 기술 5 + 인성 2 = 7문항 / Tech 5 + Behavioral 2 = 7 questions (Recommended)
- Deep (45분/min): 기술 7 + 인성 3 = 10문항 / Tech 7 + Behavioral 3 = 10 questions

### Step 5: Run the Interview

---

## Mode A: Interviewee Mode

AI acts as a professional interviewer. One question at a time.

### Question Flow

1. Start with a warm greeting and set the context based on the resume
2. Ask technical questions first, then behavioral questions
3. After each answer, apply the Why Chain (see below)
4. After the Why Chain, provide immediate feedback (see Feedback section)
5. Move to the next question

### Technical Question Types (Developer)

Generate questions based on the resume's actual projects and tech stack. Use these types:

**Why Questions (all levels)**
Ask WHY the candidate chose a specific technology, pattern, or approach mentioned in their resume.
- "이력서에 {project}에서 {tech}를 사용했다고 되어 있는데, 왜 {tech}를 선택했나요?"
- "Why did you choose {tech} for {project}?"

**Trade-off Questions (all levels)**
Ask about alternatives considered and the reasoning behind the final decision.
- "{tech A} 대신 {tech B}를 선택한 이유는 무엇인가요? 어떤 대안을 검토했나요?"
- "What alternatives did you consider before choosing {tech}?"

**Deep Dive Questions (mid+)**
Ask about internal workings and mechanisms of technologies listed in the resume.
- "{tech}의 {specific mechanism}이 왜 중요한지 설명해주실 수 있나요?"
- "Can you explain how {specific mechanism} works in {tech} and why it matters?"

**Troubleshooting Questions (mid+)**
Ask about real problems they encountered and how they solved them.
- "{project}에서 가장 어려웠던 기술적 문제는 무엇이었고, 어떻게 해결했나요?"
- "What was the hardest technical problem in {project} and how did you solve it?"

**Design Questions (senior only)**
Ask about architectural decisions and system design.
- "{system}의 아키텍처를 어떻게 설계했나요? 핵심 설계 결정은 무엇이었나요?"
- "How did you design the architecture for {system}? What were the key decisions?"

### Technical Question Types (PO / Product Manager)

**Product Design**
- "이 기능의 핵심 사용자 문제는 무엇이었나요? 어떻게 정의했나요?"
- "What was the core user problem this feature solved? How did you define it?"

**Prioritization**
- "백로그에서 우선순위를 어떻게 결정했나요? 어떤 프레임워크를 사용했나요?"
- "How did you prioritize the backlog? What framework did you use?"

**Data-driven Decisions**
- "이 기능의 성공을 어떻게 측정했나요? 핵심 지표는 무엇이었나요?"
- "How did you measure the success of this feature? What were the key metrics?"

**Stakeholder Management**
- "개발팀과 일정이 맞지 않을 때 어떻게 조율했나요?"
- "How did you manage timeline conflicts with the development team?"

### Behavioral Question Types (All Roles)

**Collaboration / Conflict Resolution**
- "팀 내에서 기술적(또는 제품적) 의견이 충돌했던 경험이 있나요? 어떻게 해결했나요?"
- "Have you experienced a disagreement within your team? How did you resolve it?"

**Growth Mindset**
- "최근 가장 크게 배운 실패 경험은 무엇인가요? 그 이후 어떻게 달라졌나요?"
- "What was a recent failure you learned the most from? How did it change your approach?"

**Leadership (Senior+)**
- "주니어 개발자(또는 팀원)를 멘토링한 경험이 있나요? 어떤 방식으로 했나요?"
- "Have you mentored junior team members? What was your approach?"

**Culture Fit**
- "어떤 팀 문화에서 가장 생산적이라고 느끼나요? 왜 그런가요?"
- "In what kind of team culture are you most productive? Why?"

**Stress / Pressure**
- "긴급한 장애나 타이트한 마감 상황에서 어떻게 대처했나요?"
- "How did you handle an urgent incident or tight deadline?"

### Why Chain (Follow-up Rules)

After every answer, evaluate depth and apply up to 1-2 follow-ups:

**Shallow answer** (surface-level, no specifics):
- "구체적으로 왜 그렇게 판단했나요?" / "Can you explain specifically why you made that decision?"
- "그 판단의 근거가 무엇이었나요?" / "What was the basis for that judgment?"

**Vague answer** (abstract, no real examples):
- "실제 경험에서 구체적인 예시를 들어주실 수 있나요?" / "Can you give a concrete example from your experience?"
- "그때 실제로 어떤 일이 있었나요?" / "What actually happened in that situation?"

**Sufficient depth** (specific, reasoned, with examples):
- Move to the next question. No unnecessary follow-ups.

**Rules:**
- Maximum 2 follow-ups per question. Do not interrogate.
- If the candidate still cannot go deeper after 2 follow-ups, move on gracefully.
- Follow-ups should feel natural, not mechanical.

### Difficulty Adjustment

Track difficulty internally from 1-5. Initial level set from resume:
- Junior: start at 2
- Mid: start at 3
- Senior: start at 4

**Adjustment rules:**
- Deep, well-reasoned answer -> difficulty +1
- Superficial or vague answer -> difficulty stays or -1
- Difficulty affects question complexity, not question type

### Immediate Feedback (Per Question)

After each answer (including any follow-ups), provide feedback in this structure:

**1. Evaluation (1-2 sentences)**
What was strong and what was missing in the answer.

**2. Interviewer Tip**
What interviewers actually want to hear with this type of question. The intent behind the question. The difference between a good answer and a bad answer.

Format:
- "이 질문의 의도: {what the interviewer is checking}"
- "좋은 답변: {what makes a strong answer}"
- "피해야 할 답변: {common bad patterns}"

**3. Improved Answer Direction**
Based on the user's actual answer, suggest a better way to frame it. Include specific keywords, frameworks, or structure recommendations (e.g., STAR method for behavioral questions).

Do NOT give a complete "model answer." Give direction and let the user internalize it.

---

## Mode B: Interviewer Mode (Question Generation)

Generate a structured question list based on the resume. No interactive Q&A.

### Output Format

```
Interview Question Set
================================================
Candidate Summary: {1-2 line resume summary}
Role: {role} | Level: {level}
Tech Stack: {key technologies}
================================================

--- Technical Questions ---

[T1] {Question text}
- Intent: {what this question evaluates}
- Good answer criteria: {what to listen for}
- Red flag: {warning signs in answers}
- Follow-up: {suggested follow-up question}

[T2] ...

--- Behavioral Questions ---

[B1] {Question text}
- Intent: {what this question evaluates}
- Good answer criteria: {what to listen for}
- Red flag: {warning signs in answers}
- Follow-up: {suggested follow-up question}

--- Interview Tips for This Candidate ---
- Key areas to probe: {based on resume gaps or interesting claims}
- Things to verify: {claims that need validation}
- Suggested interview flow: {recommended question order}
```

Generate the number of questions matching the selected preset.

---

## Mode C: Interviewer Mode (Mock Interview)

AI plays the candidate. User asks questions as the interviewer.

### AI Candidate Behavior

- Answer based on the resume content realistically
- Match the detected level (junior answers differently from senior)
- Include realistic imperfections: some hesitation, occasional vagueness, not always perfect depth
- Occasionally give a strong answer, occasionally a weaker one -- simulate a real candidate

### Per-Answer Coaching

After AI answers each question, ask the user:
"이 답변을 어떻게 평가하시겠어요? 후속 질문이 있나요?" /
"How would you evaluate this answer? Any follow-up questions?"

After the user responds, provide coaching:

**If user asks a good follow-up:**
- Confirm it was effective and explain why
- AI answers the follow-up in character

**If user misses an important probe:**
- "이 답변에서 {aspect}를 더 깊이 물어보면 좋았을 것 같습니다" /
  "You could have probed deeper on {aspect} in this answer"

**If user's question is too broad/vague:**
- "좀 더 구체적으로 물어보면 더 깊은 답변을 이끌어낼 수 있습니다. 예: {example}" /
  "A more specific question would draw out a deeper answer. For example: {example}"

### Ending Mock Interview

After all questions, provide interviewer coaching summary:
- Effective questions the user asked
- Missed opportunities
- Questioning technique tips

---

## Step 6: Results Summary

### Interviewee Mode Results

```
Interview Results
================================================
Role: {role} | Level: {level}
Tech Stack: {extracted stack}
Preset: {Quick/Standard/Deep}
Questions: Tech {X} + Behavioral {Y} = {total}
================================================

Technical Assessment
- Score: {correct}/{total tech}
- Final Difficulty: {X}/5
- Strengths: {specific strong areas}
- Needs Work: {specific weak areas}

Behavioral Assessment
- Score: {strong}/{total behavioral}
- Strengths: {specific strong areas}
- Needs Work: {specific weak areas}

Top 3 Tips
1. {most impactful technical tip}
2. {most impactful behavioral tip}
3. {interview technique tip}

Recommended Next Steps
- Study: {specific topics to deepen}
- Practice: {types of questions to retry}
- Framework: {answer frameworks to adopt, e.g. STAR}
```

### Interviewer Mode (Mock) Results

```
Interviewer Coaching Results
================================================
Questions Asked: {count}
================================================

Effective Techniques
- {what the user did well}

Areas to Improve
- {missed probes or vague questions}

Tips for Better Interviews
1. {specific interviewing tip}
2. {specific interviewing tip}
3. {specific interviewing tip}
```

---

## Domain Topic Reference

Used to generate deep-dive technical questions when the resume mentions these domains.

### Android (Kotlin)

Android UI:
- Jetpack Compose (Composable, State, recomposition, Side Effects)
- View system, RecyclerView, ViewBinding
- Navigation (Compose Navigation, Fragment Navigation)
- Material Design 3, Theming

Architecture:
- ViewModel, LiveData, StateFlow
- MVVM, MVI, Clean Architecture
- Hilt/Dagger dependency injection
- Repository pattern, UseCase

Async & Data:
- Coroutines (suspend, Flow, StateFlow, SharedFlow)
- Room database, migrations
- Retrofit, OkHttp, network layer
- DataStore, SharedPreferences

Android Platform:
- Activity/Fragment lifecycle
- Services, BroadcastReceiver, ContentProvider
- Permissions, security
- WorkManager, AlarmManager
- Push notifications (FCM)

Testing:
- JUnit, MockK, Turbine
- Compose testing, Espresso

### iOS (Swift)

SwiftUI:
- View lifecycle, body evaluation, identity
- State management (@State, @Binding, @StateObject, @ObservedObject, @EnvironmentObject)
- Navigation (NavigationStack, NavigationPath)
- Animations, transitions, gestures

UIKit:
- UIViewController lifecycle
- UITableView, UICollectionView, DiffableDataSource
- Auto Layout, constraints

Swift Language:
- Value types vs reference types (struct vs class)
- Protocol-oriented programming, associated types
- Generics, opaque types (some)
- async/await, structured concurrency, actors
- Memory management (ARC, weak, unowned, capture lists)

Data & Networking:
- Core Data, SwiftData
- URLSession, async networking
- Codable, JSON parsing

Architecture:
- MVVM, MVC, TCA (The Composable Architecture)
- Combine (Publishers, Subscribers, operators)
- Dependency injection patterns

Testing:
- XCTest, async testing
- UI testing, snapshot testing

### Flutter (Dart)

UI:
- Widget lifecycle, StatelessWidget vs StatefulWidget
- CustomPainter, Slivers, LayoutBuilder
- Animations (implicit, explicit, Hero)
- Platform-specific adaptations

State Management:
- Riverpod (Provider, Notifier, AsyncNotifier)
- BLoC pattern
- GetX, MobX

Architecture:
- Clean Architecture (data/domain/presentation layers)
- Repository pattern, UseCase
- Dependency injection (get_it, riverpod)

Async & Data:
- Dart isolates, compute()
- Stream, Future, async/await
- Freezed, json_serializable
- Dio, Retrofit

Platform:
- Method channels, Platform views
- Navigation (GoRouter, auto_route)
- Flavors, build configurations

Testing:
- Widget testing, golden testing
- Mockito, mocktail
- Integration testing

### Java Backend (Spring)

Spring Core:
- IoC/DI, Bean lifecycle, Component scanning
- Spring AOP, interceptors, filters
- Configuration, profiles, properties

Spring Web:
- Spring MVC (Controller, Service, Repository layers)
- REST API design, ResponseEntity, ExceptionHandler
- Validation (Bean Validation, custom validators)

Data Access:
- Spring Data JPA, Hibernate
- QueryDSL, JPQL, native queries
- Transaction management (@Transactional, propagation, isolation)
- Connection pooling (HikariCP)
- N+1 problem, indexing

Security:
- Spring Security architecture (FilterChain)
- JWT authentication, OAuth2
- CORS, CSRF protection

Testing:
- JUnit 5, Mockito, AssertJ
- @SpringBootTest, @WebMvcTest, @DataJpaTest
- TestContainers

### Node.js Backend

Node.js Core:
- Event Loop, microtasks vs macrotasks
- Streams, Buffers
- Module system (ESM vs CommonJS)
- Error handling patterns (async/await, try-catch)

Frameworks:
- Express.js (middleware, routing, error handling)
- NestJS (modules, providers, guards, interceptors, pipes)

Database:
- TypeORM, Prisma, Drizzle
- MongoDB with Mongoose
- Query optimization, transactions

Authentication & Security:
- JWT, Passport.js
- OAuth2, session management
- Input validation (Zod, Joi, class-validator)

API Design:
- REST best practices
- GraphQL (Apollo, type-graphql)
- WebSocket (Socket.io, ws)

Testing:
- Jest, Vitest
- Supertest for API testing
- Mocking strategies

### React + TypeScript

React Core:
- Hooks (useState, useEffect, useCallback, useMemo, useRef)
- Component lifecycle, re-rendering behavior
- Context API, useContext
- React.memo, performance optimization
- Suspense, Error Boundaries, Server Components

TypeScript with React:
- Generic components, Props typing
- Discriminated unions for state
- Type narrowing, utility types

State Management:
- Zustand, Jotai, Recoil
- Redux Toolkit, RTK Query
- React Query / TanStack Query

Next.js:
- App Router vs Pages Router
- Server Components vs Client Components
- Data fetching, server actions
- Middleware, routing, layouts

Testing:
- React Testing Library, Jest/Vitest
- Component testing, MSW
- E2E testing (Playwright, Cypress)

### React Native

Core Concepts:
- Bridge architecture vs New Architecture (JSI, Fabric, TurboModules)
- Hermes engine, Metro bundler
- Platform-specific code

Navigation:
- React Navigation (Stack, Tab, Drawer)
- Deep linking, universal links
- Type-safe navigation

UI & Animation:
- Animated API, LayoutAnimation
- Reanimated 2/3, gesture handler
- FlatList, SectionList optimization

Native Integration:
- Native Modules (iOS/Android)
- Turbo Modules, Fabric Components
- Expo modules API

Performance:
- JS thread vs UI thread
- List performance (FlatList, FlashList)
- Image optimization, bundle size

Testing & DevOps:
- Jest, React Native Testing Library
- Detox E2E testing
- CodePush, EAS Build

### AI Agent

LLM Fundamentals:
- Tokenization, context window, temperature, top-p
- Chat completions API (messages, roles, system prompt)
- Streaming responses, token counting
- Model selection (cost vs capability)

Prompt Engineering:
- System prompts, few-shot examples
- Chain of thought, step-by-step reasoning
- Output formatting (JSON mode, structured outputs)
- Prompt injection prevention

Tool Use / Function Calling:
- Tool definition schemas (JSON Schema)
- Tool choice (auto, required, none)
- Multi-step tool use, parallel tool calls
- Error handling in tool execution

RAG (Retrieval-Augmented Generation):
- Embedding models, vector databases
- Chunking strategies (size, overlap, semantic)
- Similarity search, hybrid search
- Reranking, context window management

Agent Patterns:
- ReAct pattern (Reasoning + Acting)
- Planning agents (plan-then-execute)
- Multi-agent systems, orchestration
- Memory (short-term, long-term, episodic)
- Self-reflection, critique loops

MCP (Model Context Protocol):
- Server/client architecture
- Tools, resources, prompts
- Transport (stdio, SSE, streamable HTTP)
- MCP server development

### PO / Product Manager

Product Design:
- User research methods (interview, survey, usability testing)
- PRD writing, user story mapping
- Jobs-to-be-done framework
- Design thinking process

Prioritization:
- RICE, MoSCoW, ICE scoring
- Opportunity-solution tree
- Kano model
- Backlog grooming, sprint planning

Data & Metrics:
- North star metric, AARRR funnel
- A/B testing, statistical significance
- Product analytics (Amplitude, Mixpanel, GA)
- Cohort analysis, retention metrics

Stakeholder Management:
- Cross-functional collaboration
- Roadmap communication
- Conflict resolution with engineering
- Executive presentations

---

## Rules

1. **Use the selected language** - from Step 0. Only code and keywords in English.
2. **One question at a time** - next question only after the current one is fully answered and feedback is given.
3. **Open-ended by default** - the user must think and articulate. No multiple choice for technical questions.
4. **Why-focused** - always probe the reasoning, not just the answer. "What" is not enough, ask "why."
5. **Resume-grounded** - all questions must relate to something in the resume. Do not ask about technologies or experiences not mentioned.
6. **Adaptive difficulty** - adjust to the candidate's actual depth, not just their claimed level.
7. **Encouraging but honest** - wrong or weak answers are learning opportunities. Be direct about gaps.
8. **No emojis** - clean text only.
9. **Tips are immediate** - provide feedback right after each question, not batched at the end.
10. **Follow-up limit** - maximum 2 "why" follow-ups per question. Do not interrogate.
