---
id: rule-coding-001
kind: rule
version: 1
applies_to: [ "src/**/*.ts", "src/**/*.html", "src/**/*.scss" ]
---

# Coding Standards

TypeScript, Angular, and SCSS standards for this project.
Enforced by ESLint (`.eslintrc`). Rules here extend the
upstream [Moodle App Coding Style](https://moodledev.io/general/development/policies/codingstyle-moodleapp).

---

## TypeScript

### Naming

| Entity        | Convention                       | Example                    |
|---------------|----------------------------------|----------------------------|
| Class (Core)  | `PascalCase` + `Core` prefix     | `CoreFileService`          |
| Class (Addon) | `PascalCase` + `Addon` + feature | `AddonModForumProvider`    |
| Interface     | `PascalCase` — no `I` prefix     | `CoreSiteInfo`             |
| Type          | `PascalCase`                     | `CoreWSExternalWarning`    |
| Enum          | `PascalCase`                     | `CoreLoginState`           |
| Constant      | `UPPER_SNAKE_CASE`               | `CORE_DATEPICKER_MAX_YEAR` |
| Observable    | `camelCase$`                     | `courseUpdates$`           |

### Type Safety

- **No `any`** in new code without a `// TODO: type this — <reason>` comment
- Prefer `unknown` over `any` when type is genuinely unknown
- All function parameters and return types must be explicitly typed

### File Size

- Max **400 lines** per `.ts` file — split into service + types + constants if exceeded
- Max **200 lines** per `.html` template — extract sub-components if exceeded

### Async

- Use `async/await` over raw Promise chains
- Use RxJS `Observable` for streams; convert to Promise with `firstValueFrom()` only at call boundaries
- Never swallow exceptions silently — always handle or rethrow

---

## Angular 20 Specifics

### New Control Flow (mandatory for new templates)

Use Angular 20 built-in control flow — **do not use structural directives** `*ngIf`, `*ngFor`, `*ngSwitch` in new code:

```html
<!-- CORRECT — Angular 17+ control flow -->
@if (isLoaded) {
<core-course-card [course]="course"/>
}

@for (item of items; track item.id) {
<ion-item>{{ item.title }}</ion-item>
}

@switch (status) {
@case ('loading') {
<ion-spinner/> }
@case ('error') {
<core-error/> }
@default {
<ion-content/> }
}

<!-- WRONG — legacy structural directives -->
<div *ngIf="isLoaded">...</div>
<ion-item *ngFor="let item of items">...</ion-item>
```

### Signals (available in Angular 20)

New components may use signals for local state:

```typescript
import {signal, computed} from '@angular/core';

readonly
count = signal(0);
readonly
doubled = computed(() => this.count() * 2);
```

Do not migrate existing components to signals without a dedicated task.

### Components

- Tag selector: `<namespace>-<component-name>` — e.g. `core-course-card`, `addon-forum-post`
- One component per file
- Use `OnPush` change detection for new components
- Never access DOM directly — use `Renderer2` or Angular CDK
- Never use `document.*` or `window.*` — use Cordova plugins or Angular abstractions

### Services

- All services: `providedIn: 'root'` unless scoped to a lazy module
- Expose static `instance` accessor:
  ```typescript
  static instance?: MyService;
  constructor() { MyService.instance = this; }
  ```

### Navigation

- Always use `CoreNavigator` — never Angular `Router` directly

---

## Cordova

- Access native APIs only via `@awesome-cordova-plugins/*` wrappers or direct Cordova plugin JS
- Always check `CorePlatform.isMobile()` before calling native APIs
- Wrap Cordova callbacks in promises/observables — never use raw Cordova callbacks in components
- New native plugins must be added to `config.xml` and documented in `moodle-constraints.md`

---

## SCSS

- Use CSS custom properties from `theme/` — never hardcode colors or font sizes
- Component-scoped styles only in `<component-name>.scss`
- Global overrides go in `theme/` — never in component files
- Follow Ionic variable naming: `--ion-color-*`, `--ion-font-*`

---

## Import Order (enforced by ESLint)

1. Angular core (`@angular/*`)
2. Ionic (`@ionic/*`)
3. RxJS (`rxjs/*`)
4. Core services (`@services/*`, `@singletons/*`)
5. Core components/features
6. Addon imports
7. Relative imports (`./`, `../`)

---

## Prohibited

```typescript
document.getElementById(...)     // use Angular ElementRef + Renderer2
localStorage.setItem(...)         // use CoreConfig (wraps Cordova Preferences)
sessionStorage.setItem(...)       // same — use CoreConfig
console.log(...)                  // use CoreLogger.getInstance('ClassName')
fetch('...')                      // use CoreSite.read() / CoreSite.write()
new HttpClient()                  // same
eval(...)                         // forbidden — code injection risk
element.innerHTML = userContent   // XSS risk — use CoreFormatTextDirective
```
