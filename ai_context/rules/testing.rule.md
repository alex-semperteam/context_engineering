---
id: rule-testing-001
kind: rule
version: 1
applies_to: [ "src/**/*.ts" ]
---

# Testing Requirements

---

## Test Runner

- **Unit:** Jest — `npm test` / `npm run test:ci`
- **Coverage:** `npm run test:coverage`
- **E2E:** Behat + `@moodlehq/moodle-app-behat-setup`

Test files live **next to** the code they test:

```
src/core/services/utils/text.ts
src/core/services/tests/utils/text.test.ts

src/addons/mod/forum/services/forum.ts
src/addons/mod/forum/tests/forum.test.ts
```

---

## Coverage Requirements

| Complexity | Minimum    | Scope                     |
|------------|------------|---------------------------|
| trivial    | none       | —                         |
| normal     | 70% branch | changed service/component |
| critical   | 85% branch | all affected modules      |

---

## What Must Be Tested

**Services (normal/critical):** all public methods, error paths (network, WS errors), edge cases (empty response, null
site, offline).

**Components (critical):** template rendering with different inputs, user interactions, loading/error states.

**Delegates/Handlers (normal/critical):** registration, priority ordering, fallback when no handler matches.

---

## Mock Patterns

### Services — use `mockSingleton`

```typescript
import {mockSingleton, resetAllServices} from '@/testing/utils';
import {CoreSitesProvider} from '@services/sites';

beforeEach(() => resetAllServices());

it('should load course', async () => {
    mockSingleton(CoreSitesProvider, {getCurrentSite: () => mockSite});
    // ...
});
```

### Cordova Plugins

```typescript
import {mockProvider} from '@/testing/utils';
import {File} from '@awesome-cordova-plugins/file/ngx';

mockProvider(File, {readAsText: jest.fn().mockResolvedValue('content')});
```

### Async

```typescript
// CORRECT
it('should fetch data', async () => {
    const result = await MyService.instance?.getData();
    expect(result).toBeDefined();
});
```

### Observables

```typescript
import {firstValueFrom} from 'rxjs';

it('should emit update', async () => {
    const value = await firstValueFrom(MyService.instance!.updates$);
    expect(value).toBeTruthy();
});
```

---

## E2E (Behat)

Required for: new full-screen features, critical user flows (login, course access, offline sync), features using native
APIs.

Feature files: `tests/behat/features/`

---

## CI Integration

Tests run on **every push** via Jenkins. Failing tests block merging to environment branches.
See [rules/ci-cd.md](ci-cd.md) for pipeline details.

---

## Prohibited in Tests

- No `setTimeout`/`setInterval` for async — use `fakeAsync` + `tick` or `await`
- No real network calls — mock `CoreSite.read()` / `CoreSite.write()`
- No real file system — mock `CoreFile`
- No `jest.spyOn` on static `instance` — use `mockSingleton`
