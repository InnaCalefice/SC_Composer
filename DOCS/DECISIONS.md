# SC Composer & Animation — Architekturentscheidungen

> Legende: ✅ Bestätigt (aus Code oder explizitem Beschluss) · 🔶 Einschätzung · ❓ Noch offen

---

## Projektleitende Entscheidungen

### E1 — Schrittweise Verbesserung, keine Neuentwicklung

**Beschluss:** Die bestehende Composer-Architektur wird iterativ weiterentwickelt. Eine vollständige Neuentwicklung ist nicht vorgesehen.

**Begründung:** Der Composer hat eine umfangreiche, gewachsene Feature-Basis (Undo, Layout, Typography, Motion, Freistellen). Ein Neustart würde diesen Stand rücksetzen und Nutzerakzeptanz gefährden.

**Auswirkung:** Alle Phasen bauen auf dem bestehenden `S`-Objekt, `render()` und der Single-File-HTML-Architektur auf. Keine Einführung von Build-Tools, Frameworks oder Modulbündlern.

**Status:** ✅ Aktive Leitlinie

---

### E2 — Motion während der Template-Library-Arbeiten nicht verändern

**Beschluss:** `window.__motionPoc`, die Render-Integration und der GIF-Export bleiben während der Template-Library-Phase vollständig unverändert.

**Begründung:** Motion ist funktional, komplex und durch eigene Timing-Logik (rAF, Video-Sync, ZIP-Strip) fehleranfällig bei Nebenwirkungen. Separate Veränderungen erhöhen das Absturzrisiko.

**Auswirkung:** Template Library und Auto Draft schließen Motion explizit aus (`ensureCanSave()`). Motion-Assets sind weder in `.sctpl` noch im Auto Draft gespeichert.

**Status:** ✅ Aktive Schutzregel — technisch durchgesetzt

---

### E3 — Lokalen Projektstand vor GitHub-Abgleich schützen

**Beschluss:** Vor jedem Push oder Pull wird der lokale Stand gesichert. Kein Force-Push, kein Reset. Git-Operationen erst nach Verifikation des aktuellen Branch- und Diff-Status.

**Begründung:** Git-Status ist aktuell nicht bestimmbar (Shell nicht verfügbar). Der lokale Stand in `Downloads\` enthält potenziell unbegründete Änderungen gegenüber Remote.

**Auswirkung:** Aktuell keine Git-Operationen (Pull, Push, Reset, Checkout, Clean). Verifikation steht als erste Aufgabe in NEXT_STEPS.md.

**Status:** ✅ Aktive Schutzregel

---

### E4 — Kritische Speicher- und Wiederherstellungsprobleme vor neuen UI-Features

**Beschluss:** Auto Draft, Template Restore und Bildpersistenz müssen verifiziert und stabil sein, bevor weitere UI-Verbesserungen an der Template Library umgesetzt werden.

**Begründung:** Eine fehlerhafte Wiederherstellung (leeres Canvas nach Reload, verloren gegangene Produktbilder) untergräbt das Vertrauen in das Werkzeug grundlegend. Neue UI-Funktionen auf instabiler Datenbasis sind kontraproduktiv.

**Auswirkung:** Die Reihenfolge in NEXT_STEPS.md priorisiert Verifikation und Tests vor UX-Erweiterungen.

**Status:** ✅ Aktive Leitlinie

---

### E5 — Chatverläufe nicht als alleinige Projektdokumentation

**Beschluss:** Analysen, Architekturentscheidungen und Implementierungspläne aus Chat-Sessions werden dauerhaft in versionierbaren Markdown-Dateien im Projektverzeichnis festgehalten.

**Begründung:** Chat-Kontextfenster werden komprimiert. Wichtige Erkenntnisse (z.B. Autosave-Architektur-Verifikation, Typography-Analyse) gehen bei Kontextverlust verloren. Markdown-Dateien im Projektordner bleiben persistent und sind versionierbar.

**Auswirkung:** Diese Dokumentation (`PROJECT_STATE.md`, `DECISIONS.md`, `NEXT_STEPS.md`) ist der Ausgangspunkt. Zukünftige Sessions starten mit diesen Dateien als Kontext.

**Status:** ✅ Aktive Leitlinie (diese Datei ist Beweis dafür)

---

### E6 — Wichtige Entscheidungen dauerhaft in Markdown-Dateien festhalten

**Beschluss:** Jede wesentliche Architektur-, Technologie- oder Prozessentscheidung wird zeitnah in `DECISIONS.md` eingetragen, mit Begründung und Auswirkung.

**Begründung:** Ohne schriftliche Begründung werden frühere Entscheidungen vergessen oder unnötig revidiert.

**Auswirkung:** Dieses Dokument ist fortlaufend zu pflegen. Neue Entscheidungen werden an das Ende der jeweiligen Kategorie angehängt.

**Status:** ✅ Aktive Leitlinie

---

## Technische Implementierungsentscheidungen

> Diese Entscheidungen sind aus dem Quellcode nachweisbar, aber die ursprünglichen Entscheidungsgründe wurden nicht explizit dokumentiert.

### T1 — Single-File HTML als Deployment-Format

**Technische Umsetzung (nachgewiesen):** Gesamter Code (HTML, CSS, JS) in einer einzigen Datei ohne externe Abhängigkeiten (außer CDN-Fonts).

**Plausible Begründung:** Maximale Portabilität, keine Serverinfrastruktur, einfache Distribution per E-Mail oder Dateifreigabe.

**Status:** 🔶 Bestehende Umsetzung, Begründung rekonstruiert

---

### T2 — IndexedDB für Draft und Templates, getrennte Datenbanken

**Technische Umsetzung (nachgewiesen):**
- Auto Draft: IDB `swc_composer_drafts_v1`
- Template Library: IDB `sc_composer_template_library_v1`

**Plausible Begründung:** IDB ist atomar und hat keine nennenswerte Quota-Begrenzung für JSON-Daten. Trennung vermeidet Key-Kollisionen und erlaubt unabhängige Schema-Upgrades.

**Status:** 🔶 Bestehende Umsetzung, Begründung rekonstruiert

---

### T3 — `pack()`/`unpack()` statt JSON.stringify/parse

**Technische Umsetzung (nachgewiesen):** Beide IIFEs (Auto Draft L12715, Template Library L13206) definieren je eine eigene `pack()`-Funktion, die `HTMLImageElement` transparent als `{__swcImage: img.src}` serialisiert.

**Plausible Begründung:** Eliminiert separaten img-Strip-Schritt; Bildwiederherstellung ist direkt im Deserialisierungspfad integriert.

**Bekanntes Problem:** Doppelte Implementierung erhöht Wartungsaufwand. Bug-Fixes müssen an zwei Stellen gepflegt werden.

**Status:** 🔶 Bestehende Umsetzung, Wartungsrisiko bekannt

---

### T4 — Motion explizit aus Draft und Templates ausgeschlossen

**Technische Umsetzung (nachgewiesen):** `ensureCanSave()` in Template Library prüft `hasMotion()`. Auto-Draft-KEYS enthalten keine Motion-Felder.

**Begründung:** `window.__motionPoc` ist vollständig transient (Video-Elemente, Blob-URLs, Frame-Arrays). Technisch nicht seriell speicherbar ohne Backend.

**Status:** ✅ Bestätigt und by design

---

### T5 — `window.S = S` als globale Schnittstelle

**Technische Umsetzung (nachgewiesen):** L1935 — `window.S = S;` exponiert den Dokumentzustand global.

**Begründung:** Template Library und Auto Draft sind separate IIFEs ohne direkten Scope-Zugriff auf `S`. Global-Exposition ist der einzige Zugriffspfad ohne Refactoring.

**Status:** 🔶 Bestehende Umsetzung

---

### T6 — Dreifaches render() bei Restore

**Technische Umsetzung (nachgewiesen):** Restore ruft `render()` sofort, nach einem `requestAnimationFrame` und nach einem `setTimeout(120ms)` auf.

**Begründung:** Timing-Unsicherheit bei asynchroner Bildladung und Font-Init. Dreifaches Rendering stellt sicher, dass mindestens ein Render nach vollständiger Bildhydrierung feuert.

**Bekannte Einschränkung:** Nicht garantiert korrekt in allen Timing-Szenarien (Browser-abhängig). `reconcileInitialCanvasAspect()` als zusätzlicher Fallback.

**Status:** 🔶 Pragmatischer Workaround, noch nicht vollständig verifiziert

---

*Erstellt: 2026-10-17 | Fortlaufend zu pflegen*
