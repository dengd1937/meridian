# Testing Requirements

## Common
### Minimum Test Coverage: 80%

Test Types (ALL required):
1. **Unit Tests** - Individual functions, utilities, components
2. **Integration Tests** - API endpoints, database operations
3. **E2E Tests** - Critical user flows (framework chosen per language)

### Test-Driven Development

MANDATORY workflow:
1. Write test first (RED)
2. Run test - it should FAIL
3. Write minimal implementation (GREEN)
4. Run test - it should PASS
5. Refactor (IMPROVE)
6. Verify coverage (80%+)

### Troubleshooting Test Failures

1. Use **tdd-guide** agent
2. Check test isolation
3. Verify mocks are correct
4. Fix implementation, not tests (unless tests are wrong)

### Agent Support

- **tdd-guide** - Use PROACTIVELY for new features, enforces write-tests-first


## Python

### Framework

Use **pytest** as the testing framework.

### Coverage

```bash
pytest --cov=src --cov-report=term-missing
```

### Test Organization

Use `pytest.mark` for test categorization:

```python
import pytest

@pytest.mark.unit
def test_calculate_total():
    ...

@pytest.mark.integration
def test_database_connection():
    ...
```

### Reference

See skill: `python-testing` for detailed pytest patterns and fixtures.

## TypeScript/JavaScript Testing

### Framework

- **Unit / Component tests**: Vitest + React Testing Library
- **E2E tests**: Playwright
- **API Mocking**: MSW (Mock Service Worker)

### Coverage

80%+ minimum, enforced via `vitest run --coverage`.

### Unit & Component Testing

- Test user-visible behavior, NOT implementation details
- Selector priority: `getByRole` > `getByLabelText` > `getByText` > `getByTestId`
- **NEVER** use CSS class names or DOM hierarchy as selectors

```typescript
render(<UserCard user={{ id: '1', name: 'Alice' }} onSelect={onSelect} />)
await user.click(screen.getByRole('button'))
expect(onSelect).toHaveBeenCalledWith('1')
```

### Hooks Testing

Use `renderHook` and `act` from `@testing-library/react`.

### API Mocking

Intercept network requests with MSW — do not mock fetch/axios implementations.

### E2E Testing

Use Playwright for critical user flows. Baseline screenshots committed to repo.

### Visual Regression

Use Playwright screenshot comparison (`toHaveScreenshot`) with `maxDiffPixelRatio: 0.01`.

### Accessibility Testing

Use `@axe-core/playwright` for WCAG 2.1 AA automated checks. Manual review still needed for keyboard navigation flow, screen reader announcements, and focus trap in modals.

### Test Organization

Group tests with `describe` by feature, co-locate test files with source.

### Anti-Patterns

**NEVER:**

- Test mock behavior instead of real functionality
- Add test-only methods to production code
- Use async assertions inside `.forEach`
- Replace behavioral assertions with snapshots
- Depend on test execution order
- Test framework internals

### Reference

See skill: `typescript-testing` for detailed Vitest, Playwright, MSW, and axe-core testing patterns and code examples.