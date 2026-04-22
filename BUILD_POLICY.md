# Qt 6.11 Build Policy (Windows/MSVC)

Diese Richtlinie stellt sicher, dass alle Entwickler denselben stabilen Build-Prozess verwenden.

## 1) Build-Ordner strikt trennen

- Release und Debug **immer** in getrennten Build-Ordnern bauen.
- Empfohlene Namenskonvention:
  - `D:/Qt/VS2026/6.11.0-build-release`
  - `D:/Qt/VS2026/6.11.0-build-debug`
- Re-Configure nur mit `-redo` oder in neuem Build-Ordner.

## 2) QtWebEngine und QtPdf in 6.11.0 hart deaktivieren

Nur `-skip qtwebengine` ist in 6.11.0 nicht in allen Szenarien ausreichend.

Zusätzlich verpflichtend setzen:

```txt
-- -DFEATURE_qtwebengine_build=OFF -DFEATURE_qtpdf_build=OFF
```

## 3) Konsole vor dem Build standardisieren

Vor jedem Configure/Build:

```bat
call "C:\Program Files\Microsoft Visual Studio\2026\Professional\VC\Auxiliary\Build\vcvars64.bat"
set QT_PLUGIN_PATH=
set QT_QPA_PLATFORM_PLUGIN_PATH=
set QML2_IMPORT_PATH=
set QT_QPA_PLATFORM=windows
```

## 4) Beispiele/Tests bewusst steuern

- Standard für reproduzierbare Team-Builds:
  - `-nomake examples -nomake tests`
- Wenn Beispiel-Quellen im Install-Verzeichnis benötigt werden:
  - `-install-examples-sources`
  - optional: `-examplesdir <Pfad>`

## 5) Häufige Fehlerbilder (Pflichtmaßnahmen)

- `QtGnGen.cmake:95 / GN FAILED`  
  -> WebEngine/Pdf hart deaktivieren, frischer Build-Ordner.
- `LNK1105 ... Qt6BundledPcre2.lib kann nicht geschlossen werden`  
  -> Datei-Lock lösen (Defender/Indexer/Build-Prozess), danach erneut bauen.
- `Could not find the Qt platform plugin "minimal"`  
  -> `QT_QPA_PLATFORM=windows` und QT_* Pfade bereinigen.
- `0x8002801c` bei `comapp.exe -regserver`  
  -> `-nomake examples` verwenden oder als Administrator bauen.

---

## Freigegebene Referenz-Konfiguration (Release)

```bat
configure.bat -prefix d:/Qt/VS2026/6.11.0-64-release -opensource -confirm-license ^
  -release -force-debug-info -platform win32-msvc -nomake examples -nomake tests ^
  -skip qt3d -skip qtconnectivity -skip qtdeclarative -skip qtdoc -skip qtgraphs ^
  -skip qtlocation -skip qtlottie -skip qtmqtt -skip qtopcua -skip qtquick3d ^
  -skip qtquick3dphysics -skip qtquickeffectmaker -skip qtquicktimeline -skip qtscxml ^
  -skip qtsensors -skip qtvirtualkeyboard -skip qtwayland -skip qtwebsockets ^
  -skip qtwebchannel -skip qtwebengine -skip qtwebview ^
  -- -DFEATURE_qtwebengine_build=OFF -DFEATURE_qtpdf_build=OFF
```
