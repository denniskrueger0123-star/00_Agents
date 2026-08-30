# MCP-Konnektoren

Die vier Konnektoren sind in `.mcp.json` (Projekt-Scope) hinterlegt. Beim
nächsten Start von Claude Code in diesem Repo fragt Claude einmalig nach der
Freigabe der Server.

Die API-Keys stehen **nicht** in der Datei — sie werden aus Umgebungsvariablen
expandiert (`${PERPLEXITY_API_KEY}`, `${FIRECRAWL_API_KEY}`). Vor dem Start
setzen, z. B. in `~/.zshrc` / `~/.bashrc`:

```bash
export PERPLEXITY_API_KEY="pplx-..."   # https://www.perplexity.ai/account/api
export FIRECRAWL_API_KEY="fc-..."      # https://www.firecrawl.dev/app/api-keys
```

Prüfen mit `claude mcp list`.

## Die vier Server

| Server | Transport | Paket / URL | Auth |
|---|---|---|---|
| perplexity | stdio | `@perplexity-ai/mcp-server` (1.2.1) | `PERPLEXITY_API_KEY`, Pflicht — Server startet ohne Key nicht |
| firecrawl | stdio | `firecrawl-mcp` (3.24.0) | `FIRECRAWL_API_KEY`; ohne Key läuft ein rate-limitierter Keyless-Modus (nur `firecrawl_scrape`, `firecrawl_search`) |
| playwright | stdio | `@playwright/mcp@latest` (0.0.79) | keine |
| rube | http | `https://rube.app/mcp` | OAuth im Browser, per `/mcp` in Claude Code |

## Hinweis zu Rube

`claude mcp add rube -- npx -y @composio/rube-mcp` funktioniert **nicht**:
`@composio/rube-mcp` ist kein stdio-MCP-Server, sondern ein Setup-Utility
(`rube setup` / `rube info`). Als MCP-Server registriert bricht es sofort mit
`CONNECTION_CLOSED` ab. Korrekt ist der HTTP-Endpunkt:

```bash
claude mcp add --transport http rube https://rube.app/mcp
```

## Benötigte ausgehende Hosts

Für Umgebungen mit Egress-Allowlist (z. B. Claude Code on the web):
`api.perplexity.ai`, `api.firecrawl.dev`, `rube.app`, `backend.composio.dev`
sowie die Zielseiten, die Playwright/Firecrawl aufrufen sollen.
