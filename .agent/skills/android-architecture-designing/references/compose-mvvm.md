# Compose + MVVM + UDF Checklist

- Expose screen state as StateFlow<UiState> or immutable UiState holder.
- UiState is immutable data class or sealed interface.
- UiEvent or UiAction represents user intent; ViewModel handles it.
- Use collectAsStateWithLifecycle in composables.
- Hoist state; keep transient UI-only state in composables with remember or rememberSaveable.
- Use @Stable or @Immutable when appropriate for performance.
- Avoid direct repository calls in composables; use ViewModel and use cases.
- Use one-off effects via Channel or SharedFlow, consumed in UI with LaunchedEffect.
- Map domain models to UI models at the ViewModel boundary.