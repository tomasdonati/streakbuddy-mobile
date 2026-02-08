# Coroutines and Dispatchers (Android)

Use this guide to make coroutine decisions that are lifecycle-safe and memory-efficient.

## When to Use Coroutines
- Use for async work that benefits from structured concurrency and cancellation.
- Prefer suspend functions over callbacks for data access and domain logic.
- Use Flow for streams or continuous updates.

## Scopes and Lifecycles
- Use `viewModelScope` for UI-driven work tied to a screen.
- Use `lifecycleScope` for UI-level operations in activities/fragments.
- Use `applicationScope` (custom) for app-long work like prefetching.
- Avoid `GlobalScope` to prevent leaks and uncontrolled work.

## Dispatcher Guidance
- `Dispatchers.Main` for UI updates only.
- `Dispatchers.IO` for disk and network I/O.
- `Dispatchers.Default` for CPU-intensive work (parsing, sorting, crypto).
- Use `withContext` to switch dispatchers for specific blocks.
- Use a custom dispatcher for high-priority or limited concurrency tasks.

## Avoiding Memory Issues
- Cancel work on scope cancellation; rely on structured concurrency.
- Avoid launching long-lived coroutines from Composables without a proper scope.
- Use `repeatOnLifecycle` for Flow collection to avoid leaking collectors.
- Use `stateIn` or `shareIn` with correct scope to avoid duplicate work.
- Keep references out of long-lived coroutines (avoid capturing Activities).

## Error Handling
- Use `SupervisorJob` in app-wide scopes to isolate failures.
- Prefer `runCatching` or explicit try/catch around boundary calls.
- Surface errors as part of UiState; avoid throwing in UI collectors.