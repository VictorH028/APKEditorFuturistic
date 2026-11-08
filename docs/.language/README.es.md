<p align="center">
  <a href="./README.es.md">Español</a> | <a href="https://github.com/VictorH028/APKEditorFuturistic/blob/master/README.md">English</a>
</p>

<p align="center">
  <b>APKEditor Futuristic - Interfaz</b>
</p>

## Descripción

APKEditor Futuristic es una interfaz gráfica moderna que envuelve el motor original de [APKEditor](https://github.com/REAndroid/APKEditor).

## Objetivo final

![ui_last](https://github.com/VictorH028/APKEditorFuturistic/blob/master/.img/ui_last.png)

> [!WARNING]
> En desarrollo.

## Compilación

Jar ejecutable de la interfaz gráfica:

```bash
sh gradlew guiFatJar --offline
```

El jar de la GUI se genera en:

```text
build/libs/APKEditor-1.4.9-gui.jar
```

También existe el fat jar general (CLI + GUI):

```bash
sh gradlew fatJar --offline
```

```text
build/libs/APKEditor-1.4.9-all.jar
```

> Usa `sh gradlew` si `./gradlew` falla con `permission denied`.

## Iniciar la interfaz gráfica

### Opción 1 - Classpath de desarrollo

```bash
sh gradlew compileJava --offline

java -cp 'build/classes/java/main:build/resources/main:libs/*' \
  com.reandroid.apkeditor.gui.GUIApplication
```

### Opción 2 - Jar de la GUI

```bash
sh gradlew guiFatJar --offline

java -jar build/libs/APKEditor-1.4.9-gui.jar
```

### Termux con VNC

```bash
cd /data/data/com.termux/files/home/proyecto/APKEditor

export DISPLAY=:0
export XAUTHORITY="$HOME/.Xauthority"

java -cp 'build/classes/java/main:build/resources/main:libs/*' \
  com.reandroid.apkeditor.gui.GUIApplication
```

Después abre el visor VNC en `localhost:5900` o en el puerto configurado.

## Uso rápido

1. Abre la GUI.
2. Pulsa el selector de APK o usa `Ctrl+O`.
3. Elige un archivo APK/ZIP.
4. Explora el árbol, abre archivos en el editor y revisa los logs.
5. Usa `F5` o el botón **Descompilar** para generar el proyecto.
6. Usa las vistas **Construir**, **Fusionar**, **Refactorizar** y **Proteger** para ejecutar las operaciones del motor.
7. Cambia el idioma español/inglés desde la topbar o desde **Configuración**.

## Estado

La interfaz actual está implementada con Swing + Java2D. Incluye temas en caliente y cambio de idioma español/inglés. El detalle de módulos, atajos, temas, idioma, operaciones y limitaciones está en [docs/apkeditor-ui.md](docs/apkeditor-ui.md).
