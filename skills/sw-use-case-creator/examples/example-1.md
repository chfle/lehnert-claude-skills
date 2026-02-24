# Example 1 – SW Use Case Creator

Both examples show what gets written to `requirements/use-cases.md`.
Nothing below is shown in the chat – only the confirmation line.

---

## German Example – FreelanceTracker

**User input:** `/sw-use-case-creator` ← user-stories.md exists

**Chat output (everything the user sees):**
```
✅ File created: requirements/use-cases.md
Open it in your editor or commit it.

Generated 6 use cases covering all MVP-Bereiche.
```

**Contents of requirements/use-cases.md (not shown in chat):**

---

# Use Cases: FreelanceTracker

> Generated: 2026-02-24
> Source: requirements/user-stories.md

---

## UC-001: Zeit für ein Projekt erfassen

**Akteure:** Freelancer
**Vorbedingungen:** Freelancer ist eingeloggt; mindestens ein Projekt und ein Kunde sind angelegt.
**Nachbedingungen:** Ein Zeiteintrag mit Start- und Endzeit ist gespeichert und dem Projekt zugeordnet.

### Hauptablauf
1. Freelancer öffnet die Zeiterfassungsansicht.
2. Freelancer wählt ein Projekt aus der Dropdown-Liste.
3. Freelancer klickt auf „Start".
4. System zeigt eine laufende Uhr (HH:MM:SS).
5. Freelancer klickt auf „Stop".
6. System speichert den Zeiteintrag mit Startzeit, Endzeit, Dauer und Projekt.
7. System zeigt eine Bestätigungsmeldung.

### Alternative Abläufe
- **A1 – Manueller Eintrag:** Freelancer klickt auf „Manuell hinzufügen" → gibt Start- und Endzeit direkt ein → System berechnet Dauer automatisch → Eintrag wird gespeichert.
- **A2 – Notiz hinzufügen:** Nach dem Stoppen gibt Freelancer eine optionale Notiz ein → System speichert Notiz zusammen mit dem Eintrag.

### Ausnahmen
- **E1 – Kein Projekt ausgewählt:** System deaktiviert den Start-Button solange kein Projekt gewählt ist.
- **E2 – Verbindung unterbrochen:** System speichert den Eintrag lokal und synchronisiert beim nächsten Online-Gang.

### Akzeptanzkriterien
- [ ] US-001: Start/Stop-Button ist mit einem Projekt verknüpft
- [ ] US-001: Laufende Zeit wird in Echtzeit angezeigt
- [ ] US-002: Manueller Eintrag ist über ein Formular möglich

---

## UC-002: Rechnung aus Zeiteinträgen erstellen

**Akteure:** Freelancer
**Vorbedingungen:** Mindestens ein abgeschlossener Zeiteintrag für das Projekt ist vorhanden; Kundendaten sind vollständig gepflegt.
**Nachbedingungen:** Eine Rechnung im PDF-Format ist erstellt und als „Offen" markiert.

### Hauptablauf
1. Freelancer öffnet die Projektansicht und wählt „Rechnung erstellen".
2. System lädt alle offenen Zeiteinträge des Projekts.
3. System berechnet Gesamtbetrag (Stunden × Stundensatz).
4. System befüllt die Rechnung automatisch mit Kundendaten und Zeitpositionen.
5. System generiert eine fortlaufende Rechnungsnummer.
6. Freelancer prüft die Vorschau und klickt „Finalisieren".
7. System sperrt die Rechnung für weitere Bearbeitung und setzt Status auf „Offen".
8. Freelancer lädt die Rechnung als PDF herunter.

### Alternative Abläufe
- **A1 – Einzelne Positionen ausschließen:** Freelancer deaktiviert einzelne Zeiteinträge in der Vorschau → System aktualisiert Gesamtbetrag in Echtzeit.
- **A2 – Stundensatz überschreiben:** Freelancer ändert den Stundensatz für diese Rechnung → System berechnet neu ohne den Kundenstamm zu ändern.

### Ausnahmen
- **E1 – Keine offenen Zeiteinträge:** System zeigt Hinweis „Keine abrechenbaren Zeiteinträge vorhanden".
- **E2 – Kundendaten unvollständig:** System markiert fehlende Pflichtfelder und blockiert Finalisierung.

### Akzeptanzkriterien
- [ ] US-005: Rechnung wird automatisch aus Zeiteinträgen befüllt
- [ ] US-005: PDF-Export ist per Klick verfügbar
- [ ] US-006: Vorschau zeigt exaktes PDF-Layout vor Finalisierung

---

---

## English Example – Browser Extension Summarizer

**User input:** `/sw-use-case-creator` ← user-stories.md exists

**Chat output (everything the user sees):**
```
✅ File created: requirements/use-cases.md
Open it in your editor or commit it.

Generated 4 use cases covering the core extension workflow.
```

**Contents of requirements/use-cases.md (not shown in chat):**

---

# Use Cases: Webpage Summarizer Extension

> Generated: 2026-02-24
> Source: requirements/user-stories.md

---

## UC-001: Summarize the Current Webpage

**Actors:** Knowledge Worker (primary)
**Preconditions:** Extension is installed and enabled; user has a valid API key saved in settings; a webpage is open in the active tab.
**Postconditions:** A 3-bullet summary of the current page is displayed in the extension popup.

### Main Flow
1. User clicks the extension toolbar icon.
2. System opens the popup and displays a loading indicator.
3. System injects the content script into the active tab.
4. Content script extracts visible page text and truncates to 8,000 characters.
5. System sends the text to the LLM API with the summarization prompt.
6. API returns a 3-bullet summary.
7. System displays the summary in the popup.

### Alternative Flows
- **A1 – Page already summarized:** System checks local session cache → displays cached summary immediately without a new API call.
- **A2 – Selected text only:** User has text selected on the page → System uses selected text instead of full page content.

### Exceptions
- **E1 – API key missing:** System shows "No API key set. Go to Settings to add your key."
- **E2 – API error:** System shows error type and a Retry button.
- **E3 – No readable content:** System shows "This page type is not supported (login wall, media, PDF)."
- **E4 – Timeout (>10s):** System cancels the request and shows "Request timed out. Try again."

### Acceptance Criteria
- [ ] US-001: Summary displays as exactly 3 bullet points
- [ ] US-001: Summarization completes within 5 seconds on a standard article
- [ ] US-001: Summary is in the same language as the page content

---

## UC-002: Manage API Key in Settings

**Actors:** User
**Preconditions:** Extension is installed; settings page is accessible via popup or browser extension options.
**Postconditions:** A valid API key is stored in `chrome.storage.local` and the extension is ready to use.

### Main Flow
1. User opens the extension settings page.
2. User enters their API key in the masked input field.
3. User clicks "Test Connection".
4. System sends a minimal test request to the API.
5. API returns a successful response.
6. System displays "Connection successful ✓".
7. User clicks "Save".
8. System stores the key in `chrome.storage.local`.

### Alternative Flows
- **A1 – Update existing key:** User clears the existing masked key, enters a new one, and repeats the test → System overwrites the stored key on save.

### Exceptions
- **E1 – Invalid key:** API returns 401 → System shows "Invalid API key. Please check and try again."
- **E2 – Network error during test:** System shows "Could not reach the API. Check your connection."

### Acceptance Criteria
- [ ] US-003: API key field masks the input
- [ ] US-003: Key is stored in chrome.storage.local, never transmitted to third parties
- [ ] US-003: Invalid keys show a clear error message
