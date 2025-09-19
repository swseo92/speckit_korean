
# 구현 계획: [FEATURE]

**브랜치**: `[###-feature-name]` | **날짜**: [DATE] | **명세**: [link]
**입력**: `/specs/[###-feature-name]/spec.md`에서 기능 명세서

## 실행 흐름 (/plan 명령어 범위)
```
1. Load feature spec from Input path
   → If not found: ERROR "No feature spec at {path}"
2. Fill Technical Context (scan for NEEDS CLARIFICATION)
   → Detect Project Type from context (web=frontend+backend, mobile=app+api)
   → Set Structure Decision based on project type
3. Fill the Constitution Check section based on the content of the constitution document.
4. Evaluate Constitution Check section below
   → If violations exist: Document in Complexity Tracking
   → If no justification possible: ERROR "Simplify approach first"
   → Update Progress Tracking: Initial Constitution Check
5. Execute Phase 0 → research.md
   → If NEEDS CLARIFICATION remain: ERROR "Resolve unknowns"
6. Execute Phase 1 → contracts, data-model.md, quickstart.md, agent-specific template file (e.g., `CLAUDE.md` for Claude Code, `.github/copilot-instructions.md` for GitHub Copilot, `GEMINI.md` for Gemini CLI, `QWEN.md` for Qwen Code or `AGENTS.md` for opencode).
7. Re-evaluate Constitution Check section
   → If new violations: Refactor design, return to Phase 1
   → Update Progress Tracking: Post-Design Constitution Check
8. Plan Phase 2 → Describe task generation approach (DO NOT create tasks.md)
9. STOP - Ready for /tasks command
```

**중요**: /plan 명령어는 7단계에서 중단됩니다. 2-4단계는 다른 명령어로 실행됩니다:
- 2단계: /tasks 명령어가 tasks.md를 생성
- 3-4단계: 구현 실행 (수동 또는 도구를 통해)

## 요약
[기능 명세에서 추출: 주요 요구사항 + 연구로부터의 기술적 접근법]

## 기술적 컨텍스트
**Language/Version**: [e.g., Python 3.11, Swift 5.9, Rust 1.75 or NEEDS CLARIFICATION]  
**Primary Dependencies**: [e.g., FastAPI, UIKit, LLVM or NEEDS CLARIFICATION]  
**Storage**: [if applicable, e.g., PostgreSQL, CoreData, files or N/A]  
**Testing**: [e.g., pytest, XCTest, cargo test or NEEDS CLARIFICATION]  
**Target Platform**: [e.g., Linux server, iOS 15+, WASM or NEEDS CLARIFICATION]
**Project Type**: [single/web/mobile - determines source structure]  
**Performance Goals**: [domain-specific, e.g., 1000 req/s, 10k lines/sec, 60 fps or NEEDS CLARIFICATION]  
**Constraints**: [domain-specific, e.g., <200ms p95, <100MB memory, offline-capable or NEEDS CLARIFICATION]  
**Scale/Scope**: [domain-specific, e.g., 10k users, 1M LOC, 50 screens or NEEDS CLARIFICATION]

## 헌법 체크
*게이트: 0단계 연구 전에 반드시 통과. 1단계 설계 후 재체크.*

[헌법 파일을 기반으로 결정된 게이트]

## 프로젝트 구조

### 문서화 (이 기능)
```
specs/[###-feature]/
├── plan.md              # This file (/plan command output)
├── research.md          # Phase 0 output (/plan command)
├── data-model.md        # Phase 1 output (/plan command)
├── quickstart.md        # Phase 1 output (/plan command)
├── contracts/           # Phase 1 output (/plan command)
└── tasks.md             # Phase 2 output (/tasks command - NOT created by /plan)
```

### 소스 코드 (저장소 루트)
```
# Option 1: Single project (DEFAULT)
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# Option 2: Web application (when "frontend" + "backend" detected)
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# Option 3: Mobile + API (when "iOS/Android" detected)
api/
└── [same as backend above]

ios/ or android/
└── [platform-specific structure]
```

**구조 결정**: [기술적 컨텍스트가 웹/모바일 앱을 나타내지 않는 한 옵션 1이 기본값]

## 0단계: 개요 및 연구
1. **Extract unknowns from Technical Context** above:
   - For each NEEDS CLARIFICATION → research task
   - For each dependency → best practices task
   - For each integration → patterns task

2. **Generate and dispatch research agents**:
   ```
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   ```

3. **Consolidate findings** in `research.md` using format:
   - Decision: [what was chosen]
   - Rationale: [why chosen]
   - Alternatives considered: [what else evaluated]

**출력**: 모든 명확화 필요가 해결된 research.md

## 1단계: 설계 및 계약
*선행 요구사항: research.md 완료*

1. **Extract entities from feature spec** → `data-model.md`:
   - Entity name, fields, relationships
   - Validation rules from requirements
   - State transitions if applicable

2. **Generate API contracts** from functional requirements:
   - For each user action → endpoint
   - Use standard REST/GraphQL patterns
   - Output OpenAPI/GraphQL schema to `/contracts/`

3. **Generate contract tests** from contracts:
   - One test file per endpoint
   - Assert request/response schemas
   - Tests must fail (no implementation yet)

4. **Extract test scenarios** from user stories:
   - Each story → integration test scenario
   - Quickstart test = story validation steps

5. **Update agent file incrementally** (O(1) operation):
   - Run `.specify/scripts/bash/update-agent-context.sh claude` for your AI assistant
   - If exists: Add only NEW tech from current plan
   - Preserve manual additions between markers
   - Update recent changes (keep last 3)
   - Keep under 150 lines for token efficiency
   - Output to repository root

**출력**: data-model.md, /contracts/*, 실패하는 테스트, quickstart.md, 에이전트별 파일

## 2단계: 작업 계획 접근법
*이 섹션은 /tasks 명령어가 수행할 내용을 설명 - /plan 중에는 실행하지 말 것*

**Task Generation Strategy**:
- Load `.specify/templates/tasks-template.md` as base
- Generate tasks from Phase 1 design docs (contracts, data model, quickstart)
- Each contract → contract test task [P]
- Each entity → model creation task [P] 
- Each user story → integration test task
- Implementation tasks to make tests pass

**Ordering Strategy**:
- TDD order: Tests before implementation 
- Dependency order: Models before services before UI
- Mark [P] for parallel execution (independent files)

**예상 출력**: tasks.md에 25-30개의 번호가 매겨진 순서대로 정렬된 작업

**중요**: 이 단계는 /tasks 명령어에 의해 실행되며, /plan에 의해 실행되지 않음

## 3단계+: 향후 구현
*이 단계들은 /plan 명령어의 범위를 벗어남*

**Phase 3**: Task execution (/tasks command creates tasks.md)  
**Phase 4**: Implementation (execute tasks.md following constitutional principles)  
**Phase 5**: Validation (run tests, execute quickstart.md, performance validation)

## 복잡성 추적
*헌법 체크에서 정당화되어야 할 위반사항이 있는 경우에만 작성*

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |


## 진행 상황 추적
*이 체크리스트는 실행 흐름 중에 업데이트됨*

**단계 상태**:
- [ ] 0단계: 연구 완료 (/plan 명령어)
- [ ] 1단계: 설계 완료 (/plan 명령어)
- [ ] 2단계: 작업 계획 완료 (/plan 명령어 - 접근법 설명만)
- [ ] 3단계: 작업 생성됨 (/tasks 명령어)
- [ ] 4단계: 구현 완료
- [ ] 5단계: 검증 통과

**게이트 상태**:
- [ ] 초기 헌법 체크: 통과
- [ ] 설계 후 헌법 체크: 통과
- [ ] 모든 명확화 필요 해결됨
- [ ] 복잡성 편차 문서화됨

---
*헌법 v2.1.1 기반 - `/memory/constitution.md` 참조*
