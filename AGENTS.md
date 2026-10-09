# log_box_navigation_logger Context

## Purpose:
This repository contains a specialized extension for the LogBox ecosystem that provides automated logging for Flutter's Navigator. It tracks route transitions (push, pop, replace, remove) and captures route names and arguments to build a historical timeline of user navigation.

## Key Components:
- **lib/src/observer/log_box_navigator_observer.dart**: The core implementation of a Flutter `NavigatorObserver`. It intercepts navigation events and converts them into `NavigationEntryModel` instances.
- **lib/src/model/navigation_entry_model.dart**: The data structure representing a single navigation event, including the action type, current route, and previous route metadata.
- **lib/src/enum/enum.dart**: Defines the `NavigationAction` enum (push, pop, replace, remove) used to categorize transitions.
- **lib/src/extension/extension.dart**: Provides helper extensions, likely for extracting readable strings from `RouteSettings` or handling arguments safely.

## Dependencies:
- **flutter**: Relies on the core Flutter `NavigatorObserver` and `Route` classes.
- **log_box**: The core internal module (referenced via Git in pubspec.yaml) which provides the base logging infrastructure that this logger feeds into.
- **json_annotation**: Used for generating serialization logic for the navigation models.

## Local Conventions:
- **Observer-Based Integration**: The package is designed to be integrated by adding `LogBoxNavigatorObserver` to the `navigatorObservers` list in a `MaterialApp` or `CupertinoApp`.
- **Callback Pattern**: The `LogBoxNavigatorObserver` uses an `onEvent` callback (ValueSetter) to pass captured navigation data back to the central LogBox storage, allowing for flexible integration.
- **Metadata Capture**: The observer prioritizes capturing both the route name and its arguments, ensuring that dynamic routes are correctly identified in the logs.
- **Automated Serialization**: Standard practice across the LogBox repositories, using `json_serializable` for all data models in the `model/` directory.

## Development:
- **Commands**: Use the `Makefile` (`make` lists targets). `make analyze` and `make format-check` must pass — CI enforces both (infos are fatal).
- **Code Generation**: Run `make generate` after modifying `@JsonSerializable` models; commit the `.g.dart` files.
- **Releasing**: Bump `version:` in `pubspec.yaml` and add a matching `## <version>` section to `CHANGELOG.md` in the same PR; merging creates tag `v<version>` via `release.yaml`.

## Known Pitfalls:
- **Cross-Repo Dependency Bumps**: `log_box` is pinned by git tag (`ref: v<version>`), so CI never sees an unreleased core change. For a shared-constraint bump (e.g. rxdart), land and release it in [log_box](https://github.com/robzimpulse/log_box) first, then update `ref:` and the constraint here in its own PR. See core's `AGENTS.md` → "Cross-Repo Dependency Bumps".
- **Pushing Workflow Changes**: Pushing files under `.github/workflows/` requires a GitHub token with the `workflow` scope. If a push is rejected with "refusing to allow an OAuth App to create or update workflow", run `gh auth refresh -h github.com -s workflow` and make git use that token (`gh auth setup-git`) — a stale macOS Keychain token will otherwise keep failing.
