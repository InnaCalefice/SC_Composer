# SC Composer & Animation — Projektstand

> **Analysebasis:** Nur `SC_Composer_v21_1_Template_Library_UX_Beta.html` wurde bisher analysiert.
> Git-Status und aktive Entwicklungsdatei sind noch **nicht abschließend verifiziert**.
> Legende: ✅ Bestätigt · 🔶 Einschätzung · ❓ Offen / Noch zu prüfen

---

## 1. Projektstruktur

| Aspekt | Stand | Status |
|---|---|---|
| Typ | Single-File HTML, Browser-only, kein Backend, kein Build-Step | ✅ |
| Speicherort (nachgewiesen) | `C:\Users\taacain1\Downloads\` — flache Dateistruktur | ✅ |
| Git-Repository | Kein `.git`-Verzeichnis in `Downloads\` gefunden | ✅ |
| GitHub-Verbindung | Existenz nicht bestimmbar (Shell nicht verfügbar) | ❓ |
| Aktive Entwicklungsdatei | `SC_Composer_v21_1_Template_Library_UX_Beta.html` — aktuellste analysierte Datei | 🔶 |
| Neuester Checkpoint | `SC_Composer_CHECKPOINT_2026-10-11.html` (Datum: 2026-10-11) | ✅ |

### Bekannte Dateien im Projektverzeichnis (Auswahl)

```
Downloads/
├── SC_Composer_v21_1_Template_Library_UX_Beta.html   ← analysiert, v0.9.1
├── SC_Composer_v21_Template_Library_Beta.html
├── SC_Composer_CHECKPOINT_2026-10-11.html
├── Composer_v20_Smooth_Loop_Beta.html
├── Composer_Light_Dark_v1–v19_*.html                 ← Entwicklungshistorie
├── gif_export_engine_poc/                             ← separates PoC-Projekt
├── swisscom_gif_local_poc/                            ← separates PoC-Projekt
└── README.md                                          ← SDX Library Dokumentation (unrelated)
```

---

## 2. Technischer Stand der Hauptdatei

**Datei:** `SC_Composer_v21_1_Template_Library_UX_Beta.html`
**Header-Version:** `v0.9.1 | 2026-08-31`
**Template Library Sub-Version:** `v21.1 Beta`

### 2a. SC Composer & Canvas-Editor

| Funktion | Status |
|---|---|
| Objekte: Product 1/2, Badge/Badge2/ExtraBadges, AI Label, Label, Free Text | ✅ Implementiert |
| Drag, Resize, Snap-Guides | ✅ Implementiert |
| Multi-Select + Alignment/Distribution Toolbar | ✅ Implementiert (Phase 5C/5D) |
| Contextual Selection Toolbar | ✅ Implementiert (Phase 5D) |
| Layer-Panel + Reordering | ✅ Implementiert |
| Inline Free-Text-Editor (Textarea-Overlay) | ✅ Implementiert |
| Undo (pushUndo/undoStack) | ✅ Implementiert |
| Format-Wechsel (1:1, 9:16, Custom) | ✅ Implementiert |
| Deklaratives Layout (Phase 8L.2) | ✅ Implementiert |
| Hintergrund-Grid (Verläufe, Bilder, Stripe, Split) | ✅ Implementiert |
| Onboarding-Overlay | ✅ Implementiert (Key: `cnc_onboarding_done_v2`) |
| Light/Dark Theme Toggle | ✅ Implementiert (Key: `sc-composer-ui-theme-v1`) |

### 2b. Typography

| Aspekt | Details | Status |
|---|---|---|
| Version | v2 — SDX Design-Token-Werte + `trackingPx` | ✅ |
| `FT_ROLE_WEIGHTS` | Entfernt (Phase 7T.3B, Tombstone L3666) | ✅ |
| Rollen | headline, expressiveHeadline, subheadline, lead, body, cta | ✅ |
| headline | exportPx: 80 · lineHeightPx: 88 · trackingPx: −1.6 | ✅ |
| expressiveHeadline | exportPx: 80 · lineHeightPx: 68 · trackingPx: −0.8 (neue Rolle) | ✅ |
| subheadline | exportPx: 40 · lineHeightPx: 48 · trackingPx: −0.8 | ✅ |
| lead | exportPx: 20 · lineHeightPx: 28 · trackingPx: −0.1 | ✅ |
| body | exportPx: 16 · lineHeightPx: 24 · trackingPx: 0.0 | ✅ |
| cta | exportPx: 18 · lineHeightPx: 24 · trackingPx: 0.0 | ✅ |
| `trackingPx` Rendering | `applyFtCanvasTracking()` via Canvas `letterSpacing` | ✅ |

**Hinweis:** Diese Werte weichen von `outputs/index.html` (interne Analyseversion) ab. Divergenz muss geklärt werden.

### 2c. Auto Draft

| Aspekt | Details | Status |
|---|---|---|
| IDB-Datenbank | `swc_composer_drafts_v1`, Store `drafts`, Key `'current'` | ✅ |
| Serialisierte Felder (KEYS) | 26 Felder inkl. bgStripe, bgSplit, freistellen, layoutStyle | ✅ |
| Bildserialisiierung | `pack()`: HTMLImageElement → `{__swcImage: img.src}` | ✅ |
| Bildwiederherstellung | `unpack()`: `{__swcImage}` → `new Image()` via onload | ✅ |
| Save-Trigger | `input`, `change`, `pointerup`, `keyup` global + `setInterval(2500ms)` | ✅ |
| Debounce | 700ms, Change-Detection per JSON-Signatur | ✅ |
| `restoring`-Flag | Verhindert Saves während Restore | ✅ |
| Restore-Sequenz | setFormat → KEYS → bg-Preset-Lookup → UI-Rebuild → 3× render() | ✅ |
| Canvas-Aspect-Fix | `reconcileInitialCanvasAspect()` nach Restore | ✅ |
| boot() | DOMContentLoaded → restore → 2× rAF → reconcile → Listener | ✅ |
| Blob-URL-Risiko bei product.img | Verhalten bei HTTP(S)-Quellen ungeklärt | ❓ |

### 2d. Template Library

| Aspekt | Details | Status |
|---|---|---|
| IDB-Datenbank | `sc_composer_template_library_v1`, Store `templates` | ✅ |
| Dateiformat | `.sctpl` (JSON), max. 80 MB | ✅ |
| Format-String | `'sc-composer-template'`, VERSION 1 | ✅ |
| Serialisierte Felder | Identische KEYS-Liste wie Auto Draft (26 Felder) | ✅ |
| Speichern in Browser | IDB mit UUID als Key | ✅ |
| Speichern als Datei | `showSaveFilePicker` + Fallback-Download | ✅ |
| Import `.sctpl` | JSON parse + IDB put | ✅ |
| Öffnen mit Backup | Vor Wechsel aktuelle Komposition sichern (optional) | ✅ |
| Motion-Schutz | `ensureCanSave()` verhindert Template-Save bei aktivem Motion | ✅ |
| `pack()`/`unpack()` | Eigene Kopie innerhalb Template-Library-IIFE | ✅ (Wartungsrisiko) |

### 2e. Motion & Animation

> **Schutzregel aktiv: Motion wird in dieser Dokumentation nur beschreibend erfasst. Keine Änderungen.**

| Aspekt | Details | Status |
|---|---|---|
| `window.__motionPoc` | Zentrales State-Objekt, vollständig transient | ✅ |
| Quellen | WebM (`<video>` + drawImage) + Binkies ZIP (Frame-Array) | ✅ |
| GIF-Export | Erfordert Strip ZIP als Produktionsquelle | ✅ |
| Alpha-Erkennung | Inline im `loadeddata`-Handler | ✅ |
| Persistenz | Nicht persistiert (weder Draft noch Template) | ✅ by design |

### 2f. SDX Connection

| Aspekt | Details | Status |
|---|---|---|
| Fonts | `@font-face` von `sdx.scsstatic.ch/v2.153.0/fonts/TheSans/` | ✅ |
| SDX API | Keine weiteren SDX-API-Aufrufe nachweisbar | ✅ |
| SDX als Referenz | Design-Token-Werte (Typography) übernommen | ✅ |
| Live SDX-Integration | Nicht implementiert | ✅ |

### 2g. Freistellen (remove.bg)

| Aspekt | Details | Status |
|---|---|---|
| API-Integration | remove.bg direkt + Key-Rotation | ✅ |
| Cloudflare Worker Proxy | `round-field-b289.inna-calefice.workers.dev` | ✅ |
| Hardcoded Fallback-Key | `M2rSB4eRY5JMd22zXv2PzneB` in S-Init (L1923) | ✅ ⚠ Sicherheitsrisiko |

---

## 3. localStorage-Keys (nachgewiesen)

| Key | Zweck |
|---|---|
| `swc_format` | Letztes Canvasformat |
| `swc_bgLib` | Benutzerdefinierte Hintergrundbilder |
| `swc_fonts`, `swc_activeFont` | Geladene Fonts |
| `swc_iconLib` | Benutzerdefinierte Icons |
| `swc_shapeLib` | Benutzerdefinierte Formen |
| `swc_myRibbons` | Gespeicherte Ribbons |
| `swc_rbKey`, `swc_rbKeys`, `swc_rbProxy` | remove.bg Keys + Proxy |
| `swc_exportRes` | Export-Auflösung |
| `sc-composer-ui-theme-v1` | Light/Dark Theme |
| `cnc_onboarding_done_v2` | Onboarding-Status |

---

## 4. Bekannte Risiken

| ID | Risiko | Schwere | Status |
|---|---|---|---|
| R1 | Hardcoded remove.bg API-Key im Klartext | Hoch | ❓ Zu klären |
| R2 | `pack()`/`unpack()` doppelt implementiert (Auto Draft + Template Library) | Mittel | Bekannt |
| R3 | Blob-URL-Verhalten bei product.img-Restore (HTTP-Quellen) | Mittel | ❓ Ungeklärt |
| R4 | shapeLib/iconLib nicht in Template-KEYS (custom assets gehen verloren) | Niedrig | By design |
| R5 | expressiveHeadline-Rolle nicht abwärtskompatibel zu älteren Composer-Versionen | Niedrig | Bekannt |
| R6 | Motion-Assets nicht in Templates speicherbar | Niedrig | By design (Beta) |

---

## 5. Analysegrenzen und offene Fragen

- **Nur eine Datei analysiert:** `SC_Composer_v21_1_Template_Library_UX_Beta.html`
- **Git-Status unbekannt:** Kein `.git`-Verzeichnis in `Downloads\` gefunden. GitHub-Verbindung nicht geprüft.
- **Aktive Entwicklungsdatei unklar:** Ob `CHECKPOINT_2026-10-11.html` oder `v21_1_...Beta.html` die aktuelle Arbeitsdatei ist, wurde nicht verifiziert.
- **Browser-Tests ausstehend:** Auto Draft, Template Restore und Blob-URL-Verhalten wurden nicht live getestet.
- **Typography-Divergenz:** v21.1-Werte (headline 80/88) vs. interne Analyseversion (headline 70/74) — Ursache noch ungeklärt.

---

*Erstellt: 2026-10-17 | Basis: statische Code-Analyse von SC_Composer_v21_1_Template_Library_UX_Beta.html*
