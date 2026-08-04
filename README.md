# 00_Agents — zentrale Subagenten für alle Projekte

Marketplace-Repo für eigene Claude Code Subagenten. Statt einen Agenten in
jedes Projekt-Repo zu kopieren, liegt er hier **einmal** und wird per
Claude-Code-Plugin in den anderen Repos eingebunden.

## Struktur

```
.claude-plugin/marketplace.json      Katalog (verweist auf das Plugin unten)
plugins/dk-agents/
  .claude-plugin/plugin.json         Plugin-Manifest
  agents/
    control-consti.md                Security-/DSGVO-Auditor
    execution-axel.md                Fuehrt fertige Plaene 1:1 aus
    (weitere Agenten kommen hierher)
```

## Agenten im Überblick

- **control-consti** — Security-/DSGVO-Auditor. Read-only, prüft vor einem
  Deployment oder bei sicherheitsrelevantem Code, ändert selbst nichts.
- **execution-axel** — reiner Ausführungs-Agent. Bekommt einen bereits
  fertigen, detaillierten Plan und setzt ihn Schritt für Schritt exakt um,
  ohne eigene Produktentscheidungen zu treffen. Unklares im Plan wird als
  offene Frage in der Abschluss-Zusammenfassung notiert, nicht erraten.
  Versioniert, committet und pusht dabei nach fester Routine (siehe Datei):
  bestehende Projekte behalten ihr bisheriges Versionsschema, neue Projekte
  starten mit dem 3-teiligen Schema `v0.1.0` (MAJOR=Release, MINOR=Feature,
  PATCH=Bugfix). Legt nach jedem Push zusätzlich einen Zweig an, der exakt
  die neue Versionsnummer trägt, damit GitHub das ZIP entsprechend benennt.

## Neuen Agenten hinzufügen

1. Neue Datei unter `plugins/dk-agents/agents/<name>.md` anlegen (gleiches
   Format: YAML-Frontmatter mit `name`, `description`, `tools`, `model`,
   dann der System-Prompt).
2. Optional die Version in `plugins/dk-agents/.claude-plugin/plugin.json`
   und `.claude-plugin/marketplace.json` hochzählen.
3. Committen und pushen.
4. In jedem Projekt, das die Marketplace bereits eingebunden hat: einmalig
   `/plugin marketplace update dk-agenten` (lokal) — Cloud-Sessions ziehen
   die aktuelle Version beim nächsten Start automatisch.

Kein erneutes Kopieren in einzelne Projekt-Repos mehr nötig.

## Einbindung in einem Projekt

**Lokal (einmalig, gilt danach für alle Projekte auf diesem Rechner):**

```
/plugin marketplace add denniskrueger0123-star/00_Agents
/plugin install dk-agents@dk-agenten
```

**Cloud-Sessions:** im jeweiligen Projekt-Repo in `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "dk-agenten": {
      "source": { "source": "github", "repo": "denniskrueger0123-star/00_Agents" }
    }
  },
  "enabledPlugins": ["dk-agents@dk-agenten"]
}
```

## Hinweis

Projekt- oder User-eigene `.claude/agents/<name>.md`-Dateien überschreiben
gleichnamige Plugin-Agenten. Wird ein Agent auf dieses Plugin umgestellt,
muss die lokale Kopie in dem Projekt entfernt werden — sonst gewinnt
stillschweigend die alte Kopie und das Plugin greift nicht sichtbar.
