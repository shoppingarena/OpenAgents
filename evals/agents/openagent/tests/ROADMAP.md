# OpenAgent Test Suite Roadmap

**Last Updated**: Nov 26, 2024  
**Current Status**: Test restructure complete, 22 tests migrated  
**Branch**: `feature/openagent-test-restructure`

## Immediate Next Steps (This Session)

### 1. Push and Create PR ⬜
```bash
git push -u origin feature/openagent-test-restructure
gh pr create --title "Restructure OpenAgent test suite with priority-based organization" --body "See commit message for details"
```

**Why**: Get the restructure merged so we can build on it

### 2. Verify Old Tests Still Work ⬜
Run tests from old locations to ensure backward compatibility:
```bash
cd evals/framework
npm run eval:sdk -- --agent=openagent --pattern="business/*.yaml"
npm run eval:sdk -- --agent=openagent --pattern="developer/*.yaml"
npm run eval:sdk -- --agent=openagent --pattern="edge-case/*.yaml"
```

**Why**: Confirm we didn't break anything

### 3. Run Full Test Suite ⬜
Test all migrated tests in new locations:
```bash
npm run eval:sdk -- --agent=openagent --pattern="01-critical-rules/**/*.yaml"
npm run eval:sdk -- --agent=openagent --pattern="02-workflow-stages/**/*.yaml"
npm run eval:sdk -- --agent=openagent --pattern="04-execution-paths/**/*.yaml"
npm run eval:sdk -- --agent=openagent --pattern="05-edge-cases/**/*.yaml"
npm run eval:sdk -- --agent=openagent --pattern="06-integration/**/*.yaml"
```

**Expected**: 20-22 tests should pass (some negative tests may fail as expected)

---

## Short-term (Next 1-2 Days)

### 4. Add Missing Critical Tests (High Priority) ⬜

**Current Coverage**: 50% (2/4 critical rules tested)

#### A. Report-First Tests (2 tests needed)
Location: `01-critical-rules/report-first/`

**Test 1**: `01-error-report-workflow.yaml`
```yaml
# Test full workflow: Error → Report → Propose → Approve → Fix
# Scenario: Run tests that fail, verify agent reports and waits
```

**Test 2**: `02-auto-fix-negative.yaml`
```yaml
# Negative test: Verify agent NEVER auto-fixes
# Scenario: Test fails, agent should NOT fix without approval
```

#### B. Confirm-Cleanup Tests (2 tests needed)
Location: `01-critical-rules/confirm-cleanup/`

**Test 1**: `01-session-cleanup.yaml`
```yaml
# Test session cleanup confirmation
# Scenario: After task, agent asks "Cleanup session files?"
```

**Test 2**: `02-temp-files-cleanup.yaml`
```yaml
# Test temp file cleanup confirmation
# Scenario: Agent asks before deleting .tmp/ files
```

**Impact**: Brings critical rules coverage to 100% (4/4)

### 5. Fix SDK Mode Issue (Medium Priority) ⬜

**Problem**: SDK mode causes session creation failures  
**Location**: `evals/framework/src/sdk/server-manager.ts`  
**Documentation**: `SDK_MODE_ISSUE.md`

**Investigation Steps**:
1. Test SDK directly outside framework
2. Check `@opencode-ai/sdk` version compatibility
3. Review SDK source code for session.create()
4. Test with different SDK versions
5. Check if API changed

**Goal**: Enable SDK mode for CI/CD (avoid CLI dependency)

### 6. Clean Up Old Folders ⬜

**After** full test suite passes:
```bash
cd evals/agents/openagent/tests
rm -rf business/ context-loading/ developer/ edge-case/
git add -A
git commit -m "chore: remove old test folders after migration verification"
```

**Why**: Keep repo clean, avoid confusion

---

## Medium-term (Next Week)

### 7. Add Delegation Tests (8 tests needed) ⬜

**Current Coverage**: 0% (0/7 delegation conditions tested)

Location: `03-delegation/`

#### A. Scale Tests (3 tests)
`scale/01-exactly-4-files.yaml` - Boundary test (4 files → delegate)  
`scale/02-3-files-negative.yaml` - Negative test (3 files → don't delegate)  
`scale/03-5-plus-files.yaml` - Scale test (5+ files → delegate)

#### B. Expertise Tests (2 tests)
`expertise/01-security-audit.yaml` - Security task → delegate  
`expertise/02-performance-optimization.yaml` - Performance task → delegate

#### C. Context Bundle Tests (3 tests)
`context-bundles/01-bundle-creation.yaml` - Verify bundle created  
`context-bundles/02-bundle-contents.yaml` - Verify bundle has all context  
`context-bundles/03-bundle-passed-to-subagent.yaml` - Verify subagent receives bundle

**Impact**: Brings delegation coverage to 86% (6/7 conditions)

### 8. Add Workflow Stage Tests (12 tests needed) ⬜

**Current Coverage**: 17% (1/6 stages tested)

Location: `02-workflow-stages/`

#### A. Analyze Stage (2 tests)
`analyze/01-conversational-detection.yaml` - Detect conversational path  
`analyze/02-task-detection.yaml` - Detect task path

#### B. Approve Stage (2 tests)
`approve/01-approval-request.yaml` - Verify approval requested  
`approve/02-approval-format.yaml` - Verify "Approval needed" format

#### C. Validate Stage (4 tests)
`validate/01-quality-check.yaml` - Quality verification  
`validate/02-additional-checks-prompt.yaml` - "Run additional checks?" prompt  
`validate/03-error-handling.yaml` - Stop on validation failure  
`validate/04-re-validation.yaml` - Re-validate after fixes

#### D. Summarize Stage (2 tests)
`summarize/01-brief-format.yaml` - Brief summary for simple tasks  
`summarize/02-formal-format.yaml` - Formal summary for complex tasks

#### E. Confirm Stage (2 tests)
`confirm/01-completion-check.yaml` - "Complete & satisfactory?" prompt  
`confirm/02-cleanup-prompt.yaml` - Cleanup confirmation

**Impact**: Brings workflow coverage to 100% (6/6 stages)

### 9. Add Edge Case Tests (8 tests needed) ⬜

Location: `05-edge-cases/`

#### A. Tier Conflicts (3 tests)
`tier-conflicts/01-context-override-negative.yaml` - Tier 1 overrides Tier 3  
`tier-conflicts/02-approval-override-negative.yaml` - Tier 1 overrides Tier 2  
`tier-conflicts/03-safety-first.yaml` - Safety always wins

#### B. Boundary Conditions (3 tests)
`boundary/01-bash-ls-approval.yaml` - "What files here?" needs approval  
`boundary/02-exactly-4-files.yaml` - Delegation boundary  
`boundary/03-empty-context.yaml` - Handle missing context files

#### C. Negative Tests (2 tests)
`negative/01-skip-context-negative.yaml` - Verify context can't be skipped  
`negative/02-skip-approval-negative.yaml` - Verify approval can't be skipped (except "just do it")

**Impact**: Better edge case coverage, more robust testing

### 10. Add Integration Tests (6 tests needed) ⬜

**Current Coverage**: 2 medium tests only

Location: `06-integration/`

#### A. Simple Integration (2 tests)
`simple/01-read-then-write.yaml` - Read context → Write code  
`simple/02-question-then-task.yaml` - Question → Task execution

#### B. Complex Integration (4 tests)
`complex/01-full-workflow.yaml` - All 6 stages  
`complex/02-delegation-with-validation.yaml` - Delegate → Validate → Summarize  
`complex/03-error-recovery.yaml` - Error → Report → Fix → Re-validate  
`complex/04-multi-context-multi-turn.yaml` - Multiple contexts, 6+ turns

**Impact**: Real-world scenario coverage

---

## Long-term (Next Month)

### 11. CI/CD Integration ⬜

**Update GitHub Actions**:
- Run tests by priority (critical first)
- Fail fast on critical test failures
- Run full suite on PR
- Run smoke tests on push

**Example workflow**:
```yaml
- name: Run critical tests
  run: npm run eval:sdk -- --agent=openagent --pattern="01-critical-rules/**/*.yaml"
  
- name: Run all tests (if critical pass)
  if: success()
  run: npm run eval:sdk -- --agent=openagent
```

### 12. Test Coverage Dashboard ⬜

Create visual dashboard showing:
- Coverage by category
- Pass/fail rates
- Test execution times
- Historical trends

### 13. Performance Optimization ⬜

- Parallelize test execution where possible
- Cache test results
- Optimize slow tests
- Add test timeouts based on category

### 14. Documentation Improvements ⬜

- Add test writing guide
- Create test templates
- Document common patterns
- Add troubleshooting guide

---

## Test Coverage Goals

### Current State
```
Total Tests: 22
Coverage:
  - Critical Rules: 50% (2/4 rules)
  - Context Loading: 100% (5/5 types)
  - Delegation: 0% (0/7 conditions)
  - Workflow Stages: 17% (1/6 stages)
  - Edge Cases: 20% (1/5 categories)
  - Integration: 33% (2/6 scenarios)

Overall: ~40%
```

### Target State (After All Tasks)
```
Total Tests: ~60
Coverage:
  - Critical Rules: 100% (4/4 rules)
  - Context Loading: 100% (5/5 types)
  - Delegation: 86% (6/7 conditions)
  - Workflow Stages: 100% (6/6 stages)
  - Edge Cases: 80% (4/5 categories)
  - Integration: 100% (6/6 scenarios)

Overall: ~85%
```

---

## Priority Matrix

| Task | Priority | Effort | Impact | When |
|------|----------|--------|--------|------|
| Push & Create PR | 🔴 Critical | Low | High | Now |
| Verify Old Tests | 🔴 Critical | Low | High | Now |
| Run Full Suite | 🔴 Critical | Low | High | Now |
| Add Report-First Tests | 🟠 High | Medium | High | 1-2 days |
| Add Confirm-Cleanup Tests | 🟠 High | Medium | High | 1-2 days |
| Fix SDK Mode | 🟠 High | High | Medium | 1-2 days |
| Clean Up Old Folders | 🟡 Medium | Low | Low | After tests pass |
| Add Delegation Tests | 🟡 Medium | High | Medium | 1 week |
| Add Workflow Tests | 🟡 Medium | High | Medium | 1 week |
| Add Edge Case Tests | 🟢 Low | Medium | Low | 2 weeks |
| Add Integration Tests | 🟢 Low | High | Medium | 2 weeks |
| CI/CD Integration | 🟢 Low | Medium | High | 1 month |

---

## Success Metrics

**Short-term** (This week):
- ✅ Test restructure merged
- ✅ All migrated tests passing
- ✅ Critical rules at 100% coverage
- ✅ Old folders cleaned up

**Medium-term** (This month):
- ✅ 60+ total tests
- ✅ 85%+ overall coverage
- ✅ SDK mode fixed
- ✅ CI/CD running tests automatically

**Long-term** (Next quarter):
- ✅ 100+ total tests
- ✅ 95%+ overall coverage
- ✅ Performance optimized
- ✅ Coverage dashboard live

---

## Questions to Answer

1. **Should we add tests for review.md context loading?**
   - Currently have 1 test, might need more scenarios

2. **Do we need tests for bash-only tasks?**
   - These don't require context loading (exception)
   - Should we verify the exception works?

3. **Should we test subagent responses?**
   - Currently only test delegation happens
   - Should we verify subagent actually completes task?

4. **How to test "just do it" override comprehensively?**
   - Currently have 1 test
   - Are there other override scenarios?

5. **Should we add performance benchmarks?**
   - Track test execution times
   - Alert on regressions

---

## Resources Needed

- **Time**: ~40 hours total for all tasks
- **Knowledge**: OpenAgent prompt internals, test framework
- **Tools**: Test framework, GitHub Actions, coverage tools
- **Review**: Someone to review new tests for quality

---

## Notes

- Keep test files small and focused (one scenario per test)
- Use descriptive names following convention: `{seq}-{description}-{type}.yaml`
- Document expected behavior in test description
- Add tags for easy filtering
- Update category READMEs when adding tests
