# APKEDITOR UI

<p>
  <a href="./apkeditor-ui.md">Español</a> | <a href="./apkeditor-ui.en.md">English</a>
</p>

Documentation for the APKEditor graphical interface.

> Current status: the interface is implemented with **Swing + Java2D**.

## 1. Overview

APKEDITOR UI is a desktop interface for:

- Opening and analyzing APK/ZIP files.
- Browsing the package file tree.
- Reading Smali code, XML, and resources.
- Viewing binary images as hexadecimal dumps.
- Running the existing engine operations: decode, build, merge, refactor, and protect.
- Checking logs, progress, and APK statistics.

The interface does not reimplement the engine. Views only prepare the data, and `OperationService` runs the operations registered in `OperationCatalog`.

## 2. Requirements

- **JDK 21**.
- Gradle Wrapper included in the repository (`gradlew`).
- The files in `libs/`:
  - `ARSCLib.jar`
  - `smali.jar`
  - `JCommand.jar`
- An X11 graphical server:
  - On a Linux desktop: Xorg/Wayland with XWayland.
  - On Termux: VNC (`Xvnc`) or `Termux:X11`.
- For VNC, a VNC viewer connected to the corresponding port.

The Android SDK is not required to run the interface.

## 3. Build

From the project root:

```bash
sh gradlew compileJava --offline
```

Full build without tests:

```bash
sh gradlew build -x test --offline
```

Executable GUI jar with dependencies:

```bash
sh gradlew guiFatJar --offline
```

General fat jar (CLI + GUI):

```bash
sh gradlew fatJar --offline
```

Generated artifacts:

```text
build/classes/java/main/                  compiled classes
build/resources/main/                     resources
build/libs/APKEditor-1.4.9.jar            regular jar
build/libs/APKEditor-1.4.9-all.jar        general fat jar
build/libs/APKEditor-1.4.9-gui.jar        executable GUI jar
```

> Use `sh gradlew` if `./gradlew` fails with `permission denied`.

## 4. Running

### 4.1 Termux with VNC (tested environment)

If a VNC server already exists on `:0`:

```bash
cd /data/data/com.termux/files/home/proyecto/APKEditor

export DISPLAY=:0
export XAUTHORITY="$HOME/.Xauthority"

sh gradlew compileJava --offline

java -cp 'build/classes/java/main:build/resources/main:libs/*' \
  com.reandroid.apkeditor.gui.GUIApplication
```

Then open the VNC viewer at `localhost:5900` (or the configured port).

If the server is not running, an example using TigerVNC is:

```bash
vncserver :0 -geometry 2400x1080 -depth 24
```

### 4.2 Termux with Termux:X11

```bash
export DISPLAY=:0
termux-x11 :0 &
```

Open the Termux:X11 application and run the GUI with the same Java command.

### 4.3 Linux desktop

```bash
sh gradlew compileJava --offline

java -cp 'build/classes/java/main:build/resources/main:libs/*' \
  com.reandroid.apkeditor.gui.GUIApplication
```

### 4.4 From the GUI jar

```bash
sh gradlew guiFatJar --offline

java -jar build/libs/APKEditor-1.4.9-gui.jar
```

The general fat jar also contains the GUI, but its default entry point is the CLI:

```bash
sh gradlew fatJar --offline

java -cp build/libs/APKEditor-1.4.9-all.jar \
  com.reandroid.apkeditor.gui.GUIApplication
```

> Important: `sh gradlew run` runs the **CLI** (`com.reandroid.apkeditor.Main`), not the GUI. Use `guiFatJar` and `java -jar build/libs/APKEditor-1.4.9-gui.jar` to open the interface.

## 5. Window structure

The main window is `MainWindow`:

```text
+------------------------------------------------------------------+
| Top bar: brand | APK selector | theme | language | minimize/maximize/close |
+------------+-----------------------------------------------------+
| Sidebar    | Content area                                        |
| 236 px     | Dashboard / Workspace / Operations / Information    |
|            | Settings                                            |
+------------+-----------------------------------------------------+
| Status bar: system, memory, and operation status                  |
+------------------------------------------------------------------+
```

- Minimum size: `1200 x 800`.
- Initial size: up to `1536 x 1024`, adjusted to the screen size.
- Top bar: `74 px`.
- Sidebar: `236 px`.
- Status bar: `48 px`.
- The window does not use system decoration; it is dragged from the top bar and maximized with a double click or the corresponding button.

## 6. Navigation

| Sidebar item | Route | Destination |
| --- | --- | --- |
| Dashboard | `DASHBOARD` | Dashboard, status, and quick actions |
| Decompile | `DECOMPILE` | Workspace on the Structure tab |
| Analyze | `ANALYZE` | Workspace on the Analysis tab |
| Smali Viewer | `SMALI_VIEWER` | Workspace on the Code tab |
| Build | `BUILD` | Build operation view |
| Merge | `MERGE` | Merge operation view |
| Refactor | `REFACTOR` | Refactor operation view |
| Protect | `PROTECT` | Protect operation view |
| Information | `INFORMATION` | Technical APK details |
| Settings | `SETTINGS` | Themes, language, paths, engine, and shortcuts |

The `DECODE` route exists as an alias and redirects to `DECOMPILE`. The `LOGS` route exists in the enum, but logs are shown integrated into the workspace and operation views.

## 7. Workspace

The workspace (`DecompileView`) is the main working area.

### 7.1 Header

- Title and subtitle with the active APK.
- File search button.
- Button to reset the workspace.
- **Decompile** button.
- Work tabs: **Structure**, **Code**, **Manifest**, **Resources**, and **Analysis**.

The tabs are views of the same workspace, not independent routes:

- **Structure**: shows the tree and focuses the explorer.
- **Code**: opens the editor and focuses the active document.
- **Manifest**: opens `AndroidManifest.xml`.
- **Resources**: tries to open `strings.xml`; if it does not exist, opens `resources.arsc`.
- **Analysis**: generates and opens a text report with the `ApkAnalysis` data.

### 7.2 Panels

The workspace uses nested `JSplitPane` components:

```text
+----------------+---------------------------+------------------+
| Explorer       | Code editor               | Log panel        |
| APK tree       | Document tabs             | Events           |
+----------------+---------------------------+------------------+
| Bottom dock: progress | information | statistics             |
+---------------------------------------------------------------+
```

- The divider width is `14 px`.
- Panels keep their proportions and minimum sizes when resized:
  - Explorer: minimum `240 px`.
  - Editor: minimum `360 px`.
  - Log: minimum `260 px`.
- The bottom dock is `175 px` high.

### 7.3 File explorer

- Tree with icons by type: Smali, XML, DEX, resources, images, libraries, manifest, assets, folders, and files.
- Incremental search by name.
- Single-click expansion.
- Selecting a file opens it in the editor.
- Expanded paths are preserved when filtering.
- The tree is limited to 6000 entries to keep the interface responsive.

### 7.4 Editor

The editor (`CodeEditor` + `EditorTabs`) includes:

- One tab per open file.
- Line numbers.
- Current line highlighting.
- Syntax highlighting for Smali, XML, JSON, Java, and text.
- Status with `Ln`, `Col`, spaces, encoding, and language.
- Search within the file.
- Go to line.
- Copy selection.
- Closing tabs from the tab or from the editor menu.
- Hexadecimal view for binary files.

Read limits:

- Reading files from ZIP: up to 2 MB per entry.
- Visible hexadecimal dump: first 4096 bytes.

> In this version, editor changes live in memory. They are not yet written back to the APK or the decompiled project.

## 8. APK loading and analysis

From the top bar:

1. Click the APK selector or press `Ctrl+O`.
2. Choose an `.apk`, `.zip`, or `.aab` file. The selector allows all three formats; actual AAB support depends on the engine capabilities.
3. `ApkService.analyze()` runs in the background.
4. The following are updated:
   - `AppState.currentInput`
   - `AppState.analysis`
   - `AppState.currentTree`
5. Analysis messages are shown in the log panel.
6. The interface navigates to the workspace.

`ApkService` can analyze APKs, ZIPs, and decompiled directories. Manifest reading and class counting use ARSCLib and the engine's DEX reader.

## 9. Engine operations

Operations are registered in `OperationCatalog` and executed through `OperationService`.

| Operation | ID | Input | Output |
| --- | --- | --- | --- |
| Decompile | `decode` | APK | Project directory |
| Build | `build` | Decompiled directory | APK |
| Merge | `merge` | APK | Merged APK |
| Refactor | `refactor` | APK | Refactored APK |
| Protect | `protect` | APK | Protected APK |
| Info | `info` | APK | Package information |

`Info` is registered in the catalog for the engine; the sidebar **Information** view shows the data obtained during APK analysis.

In the interface, Build/Merge/Refactor/Protect share `OperationView`. Each view allows you to:

- Type or choose the input.
- Type or choose the output.
- Run the operation.
- Reset the form.
- View progress, log, and final status.

Decompilation is started from the workspace:

- If no APK is selected, the selector opens.
- The output is generated automatically as `<name>_decompile`, adding an index if it already exists.
- On success, the output path is stored in `AppState.currentOutput`.

`OperationService` runs the engine on a background thread and publishes changes to the EDT. The UI never calls `Decompiler`, `Builder`, `Protector`, etc. directly.

## 10. Keyboard shortcuts

Implemented shortcuts:

| Shortcut | Action |
| --- | --- |
| `F5` | Go to the workspace and start decompilation |
| `Ctrl+O` | Select APK |
| `Ctrl+1` | Return to the workspace |
| `Ctrl+,` | Open Settings |
| `Ctrl+F` | Search in the active file |
| `Ctrl+G` | Go to a line in the editor |
| `Esc` | Close the editor search |

The Settings card also shows `Ctrl+S` and `Ctrl+W`; in this version they are shown as a reference, but they are not yet bound to actions.

## 11. Themes and styles

Available themes:

| Theme | Characteristic |
| --- | --- |
| `Futuristic` | Default theme, neon blue on a dark background |
| `Cyberpunk` | Neon purple/pink |
| `Dark` | Sober blue-gray |
| `Midnight` | Deep blue |

They can be changed from:

- The top bar theme selector.
- The **Appearance** card in Settings.

When the theme changes, `MainWindow` rebuilds the interface and preserves open files through `AppState`. The theme is not saved between restarts.

### 11.1 Language

The interface supports Spanish and English:

- Default language: Spanish.
- Selector in the top bar, next to the theme.
- Selector in the **Appearance** card in Settings.
- When the language changes, `MainWindow` rebuilds the interface and preserves the current route and open files through `AppState`.
- Translations live in `src/main/resources/i18n/messages.properties` (English base) and `messages_es.properties` (Spanish).
- `I18n` centralizes translations and returns the key if a text is missing.
- The language is not saved between restarts.

Main `Futuristic` palette:

| Use | Color |
| --- | --- |
| Background | `#020B18` |
| Secondary background | `#041426` |
| Surface | `#06182A` |
| Border | `#123653` |
| Primary | `#00BFFF` |
| Bright primary | `#00D5FF` |
| Success | `#00E6A0` |
| Warning | `#FFB547` |
| Error | `#FF4668` |
| Text | `#E6F4FF` |
| Secondary text | `#9CB4CC` |
| Muted text | `#64829F` |

Fonts:

- Inter, Segoe UI, or SansSerif.
- JetBrains Mono, Cascadia Code, Fira Code, Source Code Pro, Consolas, or Monospaced.

Icons are drawn with Java2D through `IconFactory`; static images are not used for components.

## 12. Architecture

### 12.1 Packages

| Package | Responsibility |
| --- | --- |
| `gui` | `GUIApplication` and `MainWindow` |
| `gui.view` | Dashboard, workspace, operations, information, and settings |
| `gui.panel` | Reusable progress, information, statistics, and status panels |
| `gui.components` | Buttons, cards, fields, bars, and visual controls |
| `gui.editor` | Editor, tabs, line numbers, and Smali highlighting |
| `gui.tree` | File explorer |
| `gui.log` | Log panel |
| `gui.model` | Shared state and UI models |
| `gui.service` | APK analysis, logs, and operation execution |
| `gui.i18n` | Spanish/English translations and language switching |
| `gui.theme` | Colors, fonts, icons, and themes |
| `gui.navigation` | Routes and navigation |
| `core` | Engine contracts and adapters |
| `operations` | Central registry of existing operations |

### 12.2 Data flow

```text
User
   |
   v
View / component
   |
   +--> AppState (shared state)
   |
   +--> OperationService
             |
             v
       OperationRegistry
             |
             v
       LegacyOptionsOperation
             |
             v
          APKEditor engine
             |
             +--> OperationListener
             +--> ExecutionContext
             +--> LogService
                        |
                        v
                     LogPanel
```

### 12.3 Design rules

- Views do not create or run engine operations directly.
- `AppState` does not contain decompilation logic.
- `OperationService` encapsulates background work.
- `LogService` is the interface message bus.
- UI updates are published to the EDT with `SwingUtilities.invokeLater`.
- Listeners are removed in `removeNotify()` to avoid leaks.

## 13. Logs and diagnostics

`LogService` is a singleton with:

- Levels: `INFO`, `SUCCESS`, `WARNING`, `ERROR`, `COMMAND`, `DEBUG`.
- Timestamps.
- Autoscroll.
- Panel clearing.
- Export to a text file.

The log panel appears:

- In the workspace.
- In each operation view.

To diagnose a failure:

1. Check the log panel.
2. Export the log with the **Export** button.
3. Verify the input and output paths.
4. Make sure the APK/project exists and is readable.
5. If the error comes from the engine, check the full message in the log.

## 14. Troubleshooting

### `No X11 DISPLAY variable was set`

There is no graphical server or the display is not exported:

```bash
export DISPLAY=:0
```

Verify that the server is running:

```bash
pgrep -af Xvnc
```

### The GUI does not appear on Termux

- Confirm that the VNC viewer is connected.
- Check `echo $DISPLAY`.
- Try `xrandr -display :0` to verify that the server responds.
- Check the Java output in the terminal.

### `permission denied: ./gradlew`

Use:

```bash
sh gradlew compileJava --offline
```

### `Could not find or load main class ...GUIApplication`

- Compile first with `sh gradlew compileJava --offline`.
- Run from the project root.
- Keep the quotes in the classpath:

```bash
java -cp 'build/classes/java/main:build/resources/main:libs/*' \
  com.reandroid.apkeditor.gui.GUIApplication
```

### `sh gradlew run` opens the console

This is expected behavior: the project `mainClass` is the CLI. Use the GUI command described in this document.

### The APK cannot be analyzed

- Verify that the file exists and is readable.
- Check the log to see whether the manifest, DEX, or ZIP failed.
- Some protected or corrupt APKs may fail in the engine.
- The tree is limited to 6000 entries and ZIP entry reading to 2 MB.

### The window is too small

The window has a minimum size of `1200 x 800`. If the screen is smaller, increase the X/VNC server resolution or use an external display.

## 15. Current status and limitations

Implemented:

- Main window, top bar, sidebar, routes, and status bar.
- Dashboard, workspace, information, and settings.
- Live theme switching.
- Live Spanish/English language switching.
- APK loading and analysis.
- File tree and search.
- Editor with highlighting, line numbers, search, and go-to-line.
- Decompilation and Build/Merge/Refactor/Protect operations.
- Logs with export.
- Simulated progress while the engine reports progress.

Pending or limited:

- Saving editor changes to the APK or project.
- `Ctrl+S` and `Ctrl+W` shortcuts.
- Persistence of theme, language, and session between restarts.
- Advanced forms with all engine-specific options.
- Dedicated logs route in the sidebar.
- Automated interface tests.

## 16. References

- [Project README](../README.en.md)
- [Visual reference](../.img/ui_last.png)
- [APKEditor repository](https://github.com/REAndroid/APKEditor)
- `build.gradle`: dependencies, version, and `fatJar` / `guiFatJar` tasks
- `src/main/java/com/reandroid/apkeditor/gui/`: interface implementation
- `src/main/java/com/reandroid/apkeditor/core/`: engine contracts
- `src/main/java/com/reandroid/apkeditor/operations/OperationCatalog.java`: operation registry
