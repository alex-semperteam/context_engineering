---
id: rule-arch-001
kind: rule
version: 1
applies_to: ["src/**"]
---

# Architecture Principles

Core architectural decisions for this Moodle App fork.
Deviation requires explicit justification in `implementation.md`.

---

## Layer Structure

```
app/           → root bootstrap only
core/          → shared infrastructure (services, components, directives, pipes)
core/features/ → core features (login, course, mainmenu, settings, ...)
addons/        → feature modules — lazy-loaded, self-contained
```

**Rule:** `core/` must never import from `addons/`.
**Rule:** addons communicate through delegates/handlers, not direct imports.

---

## Delegate Pattern (Plugin System)

Moodle App uses delegates for extensibility. New extensible features must follow this pattern:

```
CoreXxxDelegate          ← registers handlers
CoreXxxHandler           ← interface that addons implement
AddonYyyXxxHandler       ← concrete handler in an addon
```

Example: `CoreCourseModuleDelegate` + `AddonModForumModuleHandler`.

**Never** hard-code addon-specific logic inside `core/`.

---

## State Management

- **No NgRx, Akita, or external state libraries** — service-based state only
- Reactive state: RxJS `BehaviorSubject` / `ReplaySubject` in services
- Angular 20 signals for local component state (new components only)
- Shared state: `CoreEvents` pub/sub bus
- All state scoped to `siteId` to support multi-site

```typescript
// Correct: scoped to site
CoreEvents.on(CoreEvents.LOGOUT, () => this.clearData(), siteId);

// Wrong: global listener without site scope
CoreEvents.on(CoreEvents.LOGOUT, () => this.clearData());
```

---

## Database & Storage

| Data type | Storage |
|---|---|
| User preferences | `CoreConfig` (Cordova Preferences plugin) |
| Site-scoped cache | `CoreSite` SQLite (`cordova-sqlite-storage`) |
| Files | `CoreFile` (Cordova File plugin) |
| Sensitive data | Never stored locally |

- Never use `localStorage` or `sessionStorage`
- Each site has an isolated SQLite DB — never mix data across sites

---

## Web Services

All Moodle API calls go through `CoreSite`:

```typescript
// Read (cacheable)
const result = await CoreSite.instance?.read<ResponseType>('wsfunction_name', params);

// Write (never cached)
await CoreSite.instance?.write('wsfunction_name', params);
```

- Never call Moodle WS with raw `fetch` or Angular `HttpClient`
- Always handle `CoreWSError` separately from network errors
- Cache strategy defined per WS call via `preSets`

---

## Offline Support

- Addons that support offline must use `CoreSyncBaseProvider` pattern
- Offline data in addon-specific SQLite tables (defined in `addons/<name>/database/`)
- Sync via `CoreCronDelegate` — never triggered on-demand in UI
- Conflict resolution: server wins unless explicitly documented

---

## Lazy Loading

- Every addon module **must** be lazy-loaded via `loadChildren`
- No addon imported in `app.module.ts` or `core.module.ts`
- Route-based lazy loading via `CoreRoutedModules`

---

## i18n

- All UI strings via `CoreLangProvider.getString('key', 'component')`
- New strings added to `assets/lang/en.json` first
- Template: `{{ 'addon.mod_forum.subject' | translate }}`
- Never hardcode UI strings in templates or components

---

## Error Handling

- User-facing errors via `CoreDomUtils.showErrorModal()` or `CoreAlerts.showError()`
- Never show raw exception messages to users
- Log via `CoreLogger.getInstance('ClassName').error()`
- Check connectivity before offline-capable WS calls: `CoreNetwork.isOnline()`

---

## Cordova Plugin Usage

- All native access via `@awesome-cordova-plugins/*` wrappers
- Always check platform before calling native: `if (CorePlatform.isMobile()) { ... }`
- New plugins must be declared in `config.xml` and listed in `rules/moodle-constraints.md`

---

## Fork-Specific Additions

<!-- TODO: Document custom modules added in this fork -->
<!-- Example:
- src/addons/custom/   — custom features not in upstream
- src/theme/custom/    — branding overrides
-->
_Placeholder — fill when custom modules are added._
