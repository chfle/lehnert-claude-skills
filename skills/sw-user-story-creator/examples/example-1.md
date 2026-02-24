# Example 1 – SW User Story Creator

---

## German Example – FreelanceTracker

**User input:**
> `/sw-user-story-creator` ← vision.md exists with FreelanceTracker idea

**Skill output (shown in console + written to requirements/user-stories.md):**

---

# User Stories: FreelanceTracker

> Generated: 2026-02-24

---

## Epic: Zeiterfassung

### US-001: Zeit für ein Projekt starten und stoppen

**Als** Freelancer **möchte ich** die Zeit für ein Projekt per Klick starten und stoppen, **damit** ich meinen Arbeitsaufwand exakt erfassen kann ohne manuelle Notizen.

**Priorität:** MVP

**Akzeptanzkriterien:**
- [ ] Start/Stop-Button ist auf der Hauptseite sichtbar und mit einem Projekt verknüpft
- [ ] Laufende Zeit wird in Echtzeit angezeigt (HH:MM:SS)
- [ ] Zeiteintrag wird nach dem Stoppen automatisch gespeichert
- [ ] Zeiteintrag enthält Datum, Startzeit, Endzeit und Dauer
- [ ] Optionales Notizfeld pro Zeiteintrag

---

### US-002: Zeiteinträge manuell bearbeiten

**Als** Freelancer **möchte ich** vergessene oder falsche Zeiteinträge manuell hinzufügen und bearbeiten, **damit** meine Aufzeichnungen immer korrekt sind.

**Priorität:** MVP

**Akzeptanzkriterien:**
- [ ] Zeiteintrag kann über ein Formular manuell erstellt werden
- [ ] Start- und Endzeit sind frei editierbar
- [ ] Dauer wird automatisch berechnet
- [ ] Löschen eines Eintrags ist mit Bestätigungsdialog möglich

---

## Epic: Kundenverwaltung

### US-003: Kunden anlegen und verwalten

**Als** Freelancer **möchte ich** meine Kunden mit Name, E-Mail-Adresse und Stundensatz speichern, **damit** ich schnell Rechnungen mit den richtigen Daten erstellen kann.

**Priorität:** MVP

**Akzeptanzkriterien:**
- [ ] Formular zum Anlegen eines neuen Kunden mit Pflichtfeldern: Name, E-Mail
- [ ] Optionale Felder: Adresse, USt-IdNr., Standard-Stundensatz
- [ ] Kundenliste zeigt alle Kunden mit Such- und Filterfunktion
- [ ] Kunde kann bearbeitet und archiviert (nicht gelöscht) werden

---

### US-004: Projekte einem Kunden zuordnen

**Als** Freelancer **möchte ich** jedem Projekt einen Kunden zuweisen, **damit** alle Zeiteinträge und Rechnungen korrekt zugeordnet werden.

**Priorität:** MVP

**Akzeptanzkriterien:**
- [ ] Beim Anlegen eines Projekts muss ein Kunde ausgewählt werden
- [ ] Projekt erbt Standard-Stundensatz des Kunden (überschreibbar)
- [ ] Projektübersicht zeigt Kundenname und Gesamtstunden

---

## Epic: Rechnungsstellung

### US-005: Rechnung aus Zeiteinträgen generieren

**Als** Freelancer **möchte ich** mit einem Klick eine Rechnung aus allen Zeiteinträgen eines Projekts erstellen, **damit** ich keine Zeit mit manueller Rechnungsschreibung verschwende.

**Priorität:** MVP

**Akzeptanzkriterien:**
- [ ] Rechnung wird automatisch aus allen offenen Zeiteinträgen des Projekts befüllt
- [ ] Positionen zeigen Datum, Beschreibung, Stunden und Betrag
- [ ] Rechnungsnummer wird automatisch generiert (fortlaufend)
- [ ] Kundendaten werden automatisch aus der Kundenverwaltung übernommen
- [ ] PDF-Export ist per Klick verfügbar

---

### US-006: Rechnungsvorschau vor dem Versand

**Als** Freelancer **möchte ich** die Rechnung vor dem Finalisieren in einer Vorschau sehen, **damit** ich Fehler korrigieren kann bevor sie zum Kunden geht.

**Priorität:** MVP

**Akzeptanzkriterien:**
- [ ] Vorschau zeigt exakt das spätere PDF-Layout
- [ ] Alle Felder sind in der Vorschau noch editierbar
- [ ] „Finalisieren"-Button sperrt die Rechnung für weitere Bearbeitung

---

## Epic: Zahlungsstatus

### US-007: Zahlungsstatus einer Rechnung verfolgen

**Als** Freelancer **möchte ich** den Status jeder Rechnung (Offen / Bezahlt / Überfällig) auf einen Blick sehen, **damit** ich sofort weiß welche Zahlungen noch ausstehen.

**Priorität:** MVP

**Akzeptanzkriterien:**
- [ ] Rechnungsliste zeigt Status farblich markiert (Offen=gelb, Bezahlt=grün, Überfällig=rot)
- [ ] Status kann manuell auf „Bezahlt" gesetzt werden mit Eingabe des Zahlungsdatums
- [ ] Rechnungen werden automatisch „Überfällig" wenn das Fälligkeitsdatum überschritten ist
- [ ] Dashboard zeigt Summe aller offenen Beträge

---

## Epic: Export & Einstellungen

### US-008: Daten als CSV exportieren

**Als** Freelancer **möchte ich** alle Zeiteinträge und Rechnungen als CSV exportieren, **damit** ich sie meinem Steuerberater übergeben kann.

**Priorität:** Nice-to-have

**Akzeptanzkriterien:**
- [ ] Export-Button in der Zeiterfassungs- und Rechnungsansicht vorhanden
- [ ] CSV enthält alle relevanten Felder (Datum, Kunde, Projekt, Stunden, Betrag)
- [ ] Datumsbereich für den Export ist filterbar

---

✅ File created: `requirements/user-stories.md`

▶ Next: Run `/sw-diagram-creator` to visualize the architecture, or start building.

---
---

## English Example – Browser Extension Summarizer

**User input:**
> `/sw-user-story-creator` ← vision.md exists with browser extension idea

**Skill output (shown in console + written to requirements/user-stories.md):**

---

# User Stories: Webpage Summarizer Extension

> Generated: 2026-02-24

---

## Epic: Core Summarization

### US-001: Summarize the current webpage

**As a** knowledge worker, **I want** to click the toolbar button and instantly get a 3-bullet summary of the current page **so that** I can decide in seconds whether the content is worth reading in full.

**Priority:** MVP

**Acceptance Criteria:**
- [ ] Clicking the toolbar icon opens the popup and triggers summarization immediately
- [ ] Summary is displayed as exactly 3 bullet points
- [ ] Summarization completes within 5 seconds on a standard article
- [ ] If the page has no readable content, a clear error message is shown
- [ ] Summary is displayed in the same language as the page content

---

### US-002: Copy summary to clipboard

**As a** knowledge worker, **I want** to copy the generated summary with one click **so that** I can paste it into my notes or share it with colleagues.

**Priority:** MVP

**Acceptance Criteria:**
- [ ] A "Copy" button is visible next to the summary
- [ ] Clicking it copies all 3 bullets as plain text to the clipboard
- [ ] A brief confirmation ("Copied!") appears for 2 seconds after clicking

---

## Epic: API Key Management

### US-003: Enter and save my own API key

**As a** user, **I want** to enter my own LLM API key in the extension settings **so that** I can use the extension for free without a subscription.

**Priority:** MVP

**Acceptance Criteria:**
- [ ] Settings page is accessible via the popup or browser extension options
- [ ] API key field masks the input (password type)
- [ ] Key is stored in `chrome.storage.local`, never transmitted to third parties
- [ ] A "Test connection" button validates the key before saving
- [ ] Invalid keys show a clear error message

---

### US-004: See remaining API usage

**As a** user, **I want** to see how many API calls I've made this session **so that** I can manage my usage and avoid unexpected costs.

**Priority:** Nice-to-have

**Acceptance Criteria:**
- [ ] Popup shows a call counter for the current browser session
- [ ] Counter resets on browser restart
- [ ] Estimated cost is shown next to the counter (based on token estimates)

---

## Epic: Error Handling

### US-005: Handle pages that cannot be summarized

**As a** user, **I want** to see a helpful message when the extension can't summarize a page **so that** I understand what went wrong and what to do next.

**Priority:** MVP

**Acceptance Criteria:**
- [ ] Login-gated pages show: "This page requires a login – content cannot be extracted"
- [ ] API errors show the error type and a retry button
- [ ] PDF and media pages show: "This content type is not supported yet"
- [ ] All error messages are shown in the popup, not as browser alerts

---

✅ File created: `requirements/user-stories.md`

▶ Next: Run `/sw-diagram-creator` to visualize the architecture, or start building.
