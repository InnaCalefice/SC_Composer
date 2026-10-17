# SC Composer & Animation — Nächste Schritte

> Status-Legende: 🔴 Offen · 🟡 In Bearbeitung · 🟢 Abgeschlossen · ⏸ Zurückgestellt
> Priorität: P1 (kritisch) → P5 (langfristig)

---

## P1 — Aktives lokales Projekt und Git-Status verifizieren

**Status:** 🔴 Offen

**Kontext:**
- Das Projektverzeichnis ist `C:\Users\taacain1\Downloads\` (flache Dateistruktur, nachgewiesen)
- Kein `.git`-Verzeichnis in `Downloads\` gefunden
- Shell/VM ist aktuell nicht verfügbar (Windows-Update-Problem)
- Ob ein Git-Repository in einem übergeordneten Verzeichnis oder an anderem Ort existiert: unbekannt

**Aufgaben:**
1. Windows-Update installieren und System neu starten (VM-Verbindung wiederherstellen)
2. `git -C "C:\Users\taacain1\Downloads" status` ausführen
3. `git remote -v` — GitHub-URL prüfen
4. `git log --oneline -10` — letzten Commits prüfen
5. Klären: Ist `SC_Composer_CHECKPOINT_2026-10-11.html` oder `SC_Composer_v21_1_Template_Library_UX_Beta.html` die aktive Entwicklungsdatei?
6. Klären: Gibt es weitere Projektordner außerhalb von `Downloads\`?

**Akzeptanzkriterien:**
- [ ] Branch-Name bekannt
- [ ] Letzter Commit-Stand bekannt
- [ ] GitHub-Remote-URL bestätigt oder widerlegt
- [ ] Aktive Entwicklungsdatei eindeutig identifiziert

**Einschränkung:** Kein Pull, Push, Reset, Checkout oder Clean bis dieser Schritt abgeschlossen ist.

---

## P1 — API-Key-Sicherheitsrisiko untersuchen

**Status:** 🔴 Offen

**Kontext:**
- `S.removeBgKey: 'M2rSB4eRY5JMd22zXv2PzneB'` ist als Hardcode-Default in der S-Initialisierung (L1923 der analysierten Datei)
- Dieser Key ist im Klartext in jeder distribuierten HTML-Datei sichtbar
- Betrifft: alle Composer-Dateien ab dem Zeitpunkt, an dem dieser Key eingeführt wurde

**Aufgaben:**
1. Prüfen: Ist der Key noch aktiv (remove.bg Dashboard)?
2. Prüfen: In welchen anderen Dateien ist derselbe Key hartcodiert?
3. Entscheiden: Key sofort rotieren oder zunächst nur aus dem Code entfernen?
4. Implementieren: Key-Default aus Hardcode entfernen; nur noch über UI-Eingabe oder localStorage setzen

**Akzeptanzkriterien:**
- [ ] `grep -r 'M2rSB4eRY5JMd22zXv2PzneB' *.html` findet keinen Treffer in distributionsfähigen Dateien
- [ ] remove.bg-Funktionalität weiterhin nutzbar (Key via UI eingebbar)
- [ ] Kein Key mehr im Git-History sichtbar (oder bewusste Entscheidung, History nicht zu bereinigen)

---

## P2 — Template-Speicherung und Wiederherstellung testen

**Status:** 🔴 Offen

**Kontext:**
- Template Library v21.1 Beta ist im Code vollständig implementiert (nachgewiesen)
- Funktionsfähigkeit im Browser wurde noch nicht verifiziert
- Beide Speicherpfade (Browser-IDB + `.sctpl`-Datei) sind zu testen

**Testszenarien:**
1. Komposition erstellen → "In Browser-Bibliothek speichern" → Browser schließen → neu öffnen → "Öffnen" → Komposition korrekt wiederhergestellt?
2. `.sctpl` exportieren → neue Browser-Session → `.sctpl` importieren → "Öffnen" → korrekte Wiederherstellung?
3. Backup-Funktion: Template öffnen → "Aktuelle sichern" → vorherige Komposition erscheint als gesicherter Eintrag?
4. Motion-Schutz: Motion-Asset laden → Template speichern versuchen → Fehlermeldung erscheint?

**Akzeptanzkriterien:**
- [ ] Alle 4 Testszenarien bestanden
- [ ] Kein leeres Canvas nach Template-Restore
- [ ] Produktbild nach Restore sichtbar (ohne Nutzerinteraktion)
- [ ] Hintergrund nach Restore korrekt dargestellt
- [ ] Free-Text-Blöcke nach Restore korrekt positioniert

---

## P2 — Bild- und Schriftarten-Persistenz prüfen

**Status:** 🔴 Offen

**Kontext:**
- Auto Draft serialisiert `HTMLImageElement` als `{__swcImage: img.src}` (nachgewiesen)
- Wenn `img.src` eine `blob:`-URL ist (aus FileReader oder Canvas), ist diese nach Seiten-Reload ungültig
- Wenn `img.src` eine `data:`-URL oder HTTP(S)-URL ist, bleibt die Persistenz erhalten
- Das tatsächliche Verhalten bei product.img-Quellen wurde noch nicht geprüft

**Testszenarien:**
1. Produktbild (lokal, via Datei-Upload) einsetzen → Auto Draft speichert → Seite neu laden → Produktbild sichtbar?
2. Hintergrundbild (Custom, via bgLib) einsetzen → Reload → Hintergrund sichtbar?
3. Custom Font laden → Reload → Font korrekt aktiv?
4. Canvas-Format (z.B. 9:16) → Reload → Format korrekt wiederhergestellt?

**Akzeptanzkriterien:**
- [ ] Produktbild nach Reload sichtbar (kein leeres Canvas)
- [ ] Custom Hintergrundbilder persistieren
- [ ] Aktiver Font nach Reload korrekt
- [ ] Canvas-Format korrekt
- [ ] Wenn `blob:`-URLs das Problem sind: `dataUrl`-Feld als Serialisierungsquelle nutzen (Korrektur dokumentieren)

---

## P3 — Editor-/Template-Library-Schnittstelle verifizieren

**Status:** 🔴 Offen

**Kontext:**
- Template Library greift auf `window.S`, `window.setFormat`, `window.setCustomFormat`, `window.render`, `window.BG_PRESETS`, `window.FORMATS` und diverse UI-Rebuild-Funktionen zu
- Diese globalen Schnittstellen sind im Code vorhanden, aber ihre Vollständigkeit ist nicht verifiziert

**Prüfpunkte:**
1. Alle in `applyTemplate()` aufgerufenen Funktionen (L13244): `rebuildLayerPanel`, `rebuildBgGrid`, `syncBgFlipBtn`, `syncBgSplitUI`, `syncStripeUI`, `syncPropPanel`, `updateP2UI`, `updateRemoveBgUI`, `updateUI`, `updateInspector`, `render` — alle im Composer definiert?
2. `BG_PRESETS` global verfügbar für bg-Preset-Lookup?
3. `FORMATS` global verfügbar für setFormat-Check?
4. `window.__saveComposerDraft` und `window.__restoreComposerDraft` aus Auto Draft — werden sie irgendwo außerhalb genutzt?

**Akzeptanzkriterien:**
- [ ] Kein `ReferenceError` in der Browser-Konsole beim Template-Öffnen
- [ ] Alle UI-Bereiche (Layer-Panel, Bg-Grid, Props) nach Template-Restore korrekt synchron

---

## P4 — Template Library UX verbessern

**Status:** ⏸ Zurückgestellt — erst nach P1–P3

**Kontext:**
- Template Library v21.1 ist als Beta markiert
- UX-Verbesserungen (z.B. Thumbnail-Vorschau, Kategorien, Suchfunktion) sind denkbar
- Keine Implementierung bevor Grundfunktionalität verifiziert ist (E4 in DECISIONS.md)

**Vorgemerkte Ideen (noch keine Aufgaben):**
- Template-Vorschau-Thumbnail beim Speichern generieren (canvas.toDataURL, klein)
- Kategorien / Tags für Templates
- Template-Duplikation im Browser
- Sortierung nach Name / Datum

---

## P5 — SDX Connection (separater Entwicklungsbereich)

**Status:** ⏸ Zurückgestellt — langfristig

**Kontext:**
- Aktuell: Fonts von SDX CDN, Design-Token-Werte manuell übernommen
- Mögliche Erweiterung: Direkte SDX-API für Assets, Icons oder Farbsysteme
- Scope und Anforderungen noch nicht definiert

**Voraussetzungen vor Beginn:**
- [ ] P1–P3 abgeschlossen
- [ ] Klärung: Welche SDX-APIs sind für intern-Nutzung zugänglich?
- [ ] Klärung: Sicherheitsanforderungen (CORS, Auth)

---

## Backlog / Technische Schulden

| ID | Beschreibung | Priorität |
|---|---|---|
| B1 | `pack()`/`unpack()` doppelt implementiert (Auto Draft + Template Library) — gemeinsame Utility-Funktion extrahieren | Mittel |
| B2 | Typography-Divergenz klären: v21.1-Werte (headline 80/88) vs. ältere Analyseversion (70/74) | Mittel |
| B3 | `expressiveHeadline`-Rolle in Abwärtskompatibilitätsstrategie berücksichtigen | Niedrig |
| B4 | Panel-Breiten-Keys migriert (alte Keys `panel_left_w`/`panel_right_w` bei Start gelöscht, L12462) — dokumentieren ob das für alle Browser-Profile funktioniert | Niedrig |

---

*Erstellt: 2026-10-17 | Fortlaufend zu pflegen*
*Reihenfolge: P1 zuerst, kein Vorgriff auf P4/P5 vor P1–P3-Abschluss*
