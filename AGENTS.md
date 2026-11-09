# AGENTS.md

APKEditor Futuristic: a Swing/Java2D GUI wrapping the upstream APKEditor engine. Single Gradle module (`rootProject.name = 'APKEditor'`), Java 21.

## Build & run

```bash
sh gradlew compileJava --offline          # compile
sh gradlew guiFatJar --offline            # build/libs/APKEditor-1.4.9-gui.jar (GUI)
sh gradlew fatJar --offline               # build/libs/APKEditor-1.4.9-all.jar (CLI+GUI, CLI main)
sh gradlew build -x test --offline        # no tests exist; -x test is in the docs
```

- On Termux `./gradlew` fails with permission denied; always use `sh gradlew`.
- Run GUI from classes: `java -cp 'build/classes/java/main:build/resources/main:libs/*' com.reandroid.apkeditor.gui.GUIApplication`
- `sh gradlew run` launches the **CLI** (`com.reandroid.apkeditor.Main`), not the GUI.
- GUI needs an X11 display (`export DISPLAY=:0`; Termux uses VNC/Termux:X11).
- Jar names are derived from `version` in `build.gradle` (currently `1.4.9`); CI globs `build/libs/*-gui.jar`.

## Dependencies & offline builds

- Engine deps are checked-in jars in `libs/` (`ARSCLib.jar`, `smali.jar`, `JCommand.jar`), loaded via `files(...)`, not Maven coordinates. Do not replace with remote deps.
- `--offline` is expected/required in this environment; Gradle wrapper is 8.5.

## Entrypoints & architecture

- CLI: `src/main/java/com/reandroid/apkeditor/Main.java`.
- GUI: `com.reandroid.apkeditor.gui.GUIApplication` -> `MainWindow`.
- Operations are registered once in `operations/OperationCatalog.java` and run via `core/OperationRegistry` -> `LegacyOptionsOperation`.
- Views must not call `Decompiler`/`Builder`/`Protector` directly. Route work through `gui/service/OperationService`, which runs on a background thread and marshals UI updates with `SwingUtilities.invokeLater`.
- Shared UI state lives in `gui/model/AppState` (PropertyChangeSupport); it must not contain engine logic.
- `docs/dev-ui.md` is the authoritative deep reference for UI structure, routes, themes, shortcuts, and limitations. Update it when UI behavior changes.

## Conventions

- GUI source comments (and `build.gradle` comments) are in Spanish; upstream engine code is English. Match the file you edit.
- GUI strings live in `src/main/resources/i18n/messages.properties` (English base) and `messages_es.properties`. Add every new key to both; `I18n` returns the raw key on a miss. Default UI language is Spanish.
- Engine CLI strings use `src/main/resources/strings/strings*.properties` (separate from GUI i18n).
- `jar` task uses `duplicatesStrategy = FAIL`: duplicate resources break the regular jar (fat jars use EXCLUDE).
- No test sources or test framework are configured; do not invent test commands. Verify with `compileJava`/`guiFatJar`.

## Known limitations (do not "fix" silently)

- Editor edits are in-memory only; saving back to APK/project is not implemented.
- Theme, language, and session are not persisted across restarts.
- ZIP entries read are capped at 2 MB; tree is capped at 6000 entries; hex dump shows 4096 bytes.
- AAB loading is offered by the file selector but actual support depends on the engine.
