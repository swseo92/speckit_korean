# 작업: [FEATURE NAME]

**입력**: `/specs/[###-feature-name]/`에서 설계 문서
**선행 요구사항**: plan.md (필수), research.md, data-model.md, contracts/

## 실행 흐름 (main)
```
1. Load plan.md from feature directory
   → If not found: ERROR "No implementation plan found"
   → Extract: tech stack, libraries, structure
2. Load optional design documents:
   → data-model.md: Extract entities → model tasks
   → contracts/: Each file → contract test task
   → research.md: Extract decisions → setup tasks
3. Generate tasks by category:
   → Setup: project init, dependencies, linting
   → Tests: contract tests, integration tests
   → Core: models, services, CLI commands
   → Integration: DB, middleware, logging
   → Polish: unit tests, performance, docs
4. Apply task rules:
   → Different files = mark [P] for parallel
   → Same file = sequential (no [P])
   → Tests before implementation (TDD)
5. Number tasks sequentially (T001, T002...)
6. Generate dependency graph
7. Create parallel execution examples
8. Validate task completeness:
   → All contracts have tests?
   → All entities have models?
   → All endpoints implemented?
9. Return: SUCCESS (tasks ready for execution)
```

## 형식: `[ID] [P?] 설명`
- **[P]**: 병렬 실행 가능 (다른 파일, 의존성 없음)
- 설명에 정확한 파일 경로 포함

## 경로 규칙
- **단일 프로젝트**: 저장소 루트에 `src/`, `tests/`
- **웹 앱**: `backend/src/`, `frontend/src/`
- **모바일**: `api/src/`, `ios/src/` 또는 `android/src/`
- 아래 경로들은 단일 프로젝트를 가정 - plan.md 구조에 따라 조정

## 3.1단계: 설정
- [ ] T001 Create project structure per implementation plan
- [ ] T002 Initialize [language] project with [framework] dependencies
- [ ] T003 [P] Configure linting and formatting tools

## 3.2단계: 테스트 우선 (TDD) ⚠️ 3.3 전에 반드시 완료
*테스트 작성 시 `docs/testing_guidelines.md`의 최신 표준을 동적으로 참조하여 준수*
**중요: 이 테스트들은 반드시 작성되어야 하고 구현 전에 반드시 실패해야 함**
- [ ] T004 [P] Contract test POST /api/users in tests/contract/test_users_post.py
- [ ] T005 [P] Contract test GET /api/users/{id} in tests/contract/test_users_get.py
- [ ] T006 [P] Integration test user registration in tests/integration/test_registration.py
- [ ] T007 [P] Integration test auth flow in tests/integration/test_auth.py

## 3.3단계: 핵심 구현 (테스트가 실패한 후에만)
- [ ] T008 [P] User model in src/models/user.py
- [ ] T009 [P] UserService CRUD in src/services/user_service.py
- [ ] T010 [P] CLI --create-user in src/cli/user_commands.py
- [ ] T011 POST /api/users endpoint
- [ ] T012 GET /api/users/{id} endpoint
- [ ] T013 Input validation
- [ ] T014 Error handling and logging

## 3.4단계: 통합
- [ ] T015 Connect UserService to DB
- [ ] T016 Auth middleware
- [ ] T017 Request/response logging
- [ ] T018 CORS and security headers

## 3.5단계: 마무리
- [ ] T019 [P] Unit tests for validation in tests/unit/test_validation.py
- [ ] T020 Performance tests (<200ms)
- [ ] T021 [P] Update docs/api.md
- [ ] T022 Remove duplication
- [ ] T023 Run manual-testing.md

## 의존성
- Tests (T004-T007) before implementation (T008-T014)
- T008 blocks T009, T015
- T016 blocks T018
- Implementation before polish (T019-T023)

## 병렬 실행 예시
```
# Launch T004-T007 together:
Task: "Contract test POST /api/users in tests/contract/test_users_post.py"
Task: "Contract test GET /api/users/{id} in tests/contract/test_users_get.py"
Task: "Integration test registration in tests/integration/test_registration.py"
Task: "Integration test auth in tests/integration/test_auth.py"
```

## 주의사항
- [P] 작업 = 다른 파일, 의존성 없음
- 구현 전에 테스트 실패 확인
- 각 작업 후 커밋
- 피하기: 모호한 작업, 동일 파일 충돌

## 작업 생성 규칙
*main() 실행 중 적용*

1. **From Contracts**:
   - Each contract file → contract test task [P]
   - Each endpoint → implementation task
   
2. **From Data Model**:
   - Each entity → model creation task [P]
   - Relationships → service layer tasks
   
3. **From User Stories**:
   - Each story → integration test [P]
   - Quickstart scenarios → validation tasks

4. **Ordering**:
   - Setup → Tests → Models → Services → Endpoints → Polish
   - Dependencies block parallel execution

## 검증 체크리스트
*게이트: 반환 전에 main()에 의해 체크*

- [ ] All contracts have corresponding tests
- [ ] All entities have model tasks
- [ ] All tests come before implementation
- [ ] Parallel tasks truly independent
- [ ] Each task specifies exact file path
- [ ] No task modifies same file as another [P] task