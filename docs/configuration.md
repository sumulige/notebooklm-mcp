## Configuration

**No config files needed!** The server works out of the box with sensible defaults.

### Configuration Priority (highest to lowest):

1. **Tool Parameters** - Claude passes settings like `browser_options` at runtime
2. **Environment Variables** - Optional overrides for advanced users
3. **Hardcoded Defaults** - Sensible defaults that work for most users

---

## Tool Parameters (Runtime Configuration)

Claude can control browser behavior via the `browser_options` parameter in tools like `ask_question`, `setup_auth`, and `re_auth`:

```typescript
browser_options: {
  show: boolean,              // Show browser window (overrides headless)
  headless: boolean,          // Run in headless mode (default: true)
  timeout_ms: number,         // Browser timeout in ms (default: 30000)

  stealth: {
    enabled: boolean,         // Master switch (default: true)
    random_delays: boolean,   // Random delays between actions (default: true)
    human_typing: boolean,    // Human-like typing (default: true)
    mouse_movements: boolean, // Realistic mouse movements (default: true)
    typing_wpm_min: number,   // Min typing speed (default: 160)
    typing_wpm_max: number,   // Max typing speed (default: 240)
    delay_min_ms: number,     // Min delay between actions (default: 100)
    delay_max_ms: number,     // Max delay between actions (default: 400)
  },

  viewport: {
    width: number,            // Viewport width (default: 1024)
    height: number,           // Viewport height (default: 768)
  }
}
```

**Example usage:**

- "Research this and show me the browser" → Sets `show: true`
- "Use slow typing for this query" → Adjusts typing WPM via stealth settings

---

## Environment Variables (Optional)

For advanced users who want to set global defaults:

- Auth
  - `AUTO_LOGIN_ENABLED` — `true|false` (default `false`)
  - `LOGIN_EMAIL`, `LOGIN_PASSWORD` — for auto‑login if enabled
  - `AUTO_LOGIN_TIMEOUT_MS` (default `120000`)
- Stealth / Human-like behavior
  - `STEALTH_ENABLED` — `true|false` (default `true`) — Master switch for all stealth features
  - `STEALTH_RANDOM_DELAYS` — `true|false` (default `true`)
  - `STEALTH_HUMAN_TYPING` — `true|false` (default `true`)
  - `STEALTH_MOUSE_MOVEMENTS` — `true|false` (default `true`)
- Typing speed (human‑like)
  - `TYPING_WPM_MIN` (default 160), `TYPING_WPM_MAX` (default 240)
- Delays (human‑like)
  - `MIN_DELAY_MS` (default 100), `MAX_DELAY_MS` (default 400)
- Browser
  - `HEADLESS` (default `true`), `BROWSER_TIMEOUT` (ms, default `30000`)
- Sessions
  - `MAX_SESSIONS` (default 10), `SESSION_TIMEOUT` (s, default 900)
- Multi‑instance profile strategy
  - `NOTEBOOK_PROFILE_STRATEGY` — `auto|single|isolated` (default `auto`)
  - `NOTEBOOK_CLONE_PROFILE` — clone base profile into isolated dir (default `false`)
- Cleanup (to prevent disk bloat)
  - `NOTEBOOK_CLEANUP_ON_STARTUP` (default `true`)
  - `NOTEBOOK_CLEANUP_ON_SHUTDOWN` (default `true`)
  - `NOTEBOOK_INSTANCE_TTL_HOURS` (default `72`)
  - `NOTEBOOK_INSTANCE_MAX_COUNT` (default `20`)
- Library metadata (optional hints)
  - `NOTEBOOK_DESCRIPTION`, `NOTEBOOK_TOPICS`, `NOTEBOOK_CONTENT_TYPES`, `NOTEBOOK_USE_CASES`
  - `NOTEBOOK_URL` — optional; leave empty and manage notebooks via the library

---

## Storage Paths

The server uses platform-specific paths via [env-paths](https://github.com/sindresorhus/env-paths)

- **Linux**: `~/.local/share/notebooklm-mcp/`
- **macOS**: `~/Library/Application Support/notebooklm-mcp/`
- **Windows**: `%LOCALAPPDATA%\notebooklm-mcp\`

**What's stored:**

- `chrome_profile/` - Persistent Chrome browser profile with login session
- `browser_state/` - Browser context state and cookies
- `library.json` - Your notebook library with metadata
- `chrome_profile_instances/` - Isolated Chrome profiles for concurrent sessions

**No config.json file** - Configuration is purely via environment variables or tool parameters!

---

## Configuration Validation

The server includes a built-in configuration validation system that ensures all settings are valid and provides warnings for non-optimal values.

### Validation Features

- **Type Safety** - Runtime validation with Zod schema
- **Range Checking** - Numeric values validated against min/max bounds
- **Logical Constraints** - Cross-field validation (e.g., `minDelayMs <= maxDelayMs`)
- **Health Warnings** - Alerts for non-optimal configurations

### Validation on Startup

When the server starts, it automatically validates all configuration values:

```bash
$ notebooklm-mcp
✅ Configuration validated successfully
ℹ️  Configuration warnings:
  - maxSessions (75) > 50 may cause performance issues
```

### Safe Fallback Behavior

Invalid values automatically fall back to safe defaults:

```bash
# These invalid values are safely rejected:
BROWSER_TIMEOUT=-100   # Negative → uses default 30000
MAX_SESSIONS=invalid    # NaN → uses default 10
TYPING_WPM_MIN=300     # Above max → uses default 160
```

### Configuration Value Bounds

| Setting                 | Min  | Max    | Default |
| ----------------------- | ---- | ------ | ------- |
| `BROWSER_TIMEOUT`       | 1000 | 300000 | 30000   |
| `MAX_SESSIONS`          | 1    | 100    | 10      |
| `SESSION_TIMEOUT`       | 60   | 86400  | 900     |
| `TYPING_WPM_MIN`        | 40   | 200    | 160     |
| `TYPING_WPM_MAX`        | 40   | 300    | 240     |
| `MIN_DELAY_MS`          | 0    | 10000  | 100     |
| `MAX_DELAY_MS`          | 0    | 30000  | 400     |
| `AUTO_LOGIN_TIMEOUT_MS` | 1000 | 600000 | 120000  |
| `INSTANCE_TTL_HOURS`    | 1    | 720    | 72      |
| `INSTANCE_MAX_COUNT`    | 1    | 100    | 20      |

### Logical Constraints

The following logical constraints are enforced:

- `TYPING_WPM_MIN` ≤ `TYPING_WPM_MAX`
- `MIN_DELAY_MS` ≤ `MAX_DELAY_MS`

### Health Check Warnings

The server will warn you about potentially problematic configurations:

| Condition                                    | Warning                         |
| -------------------------------------------- | ------------------------------- |
| `maxSessions > 50`                           | May cause performance issues    |
| `browserTimeout > 120s`                      | May cause slow responses        |
| `stealthEnabled = false`                     | Increased detection risk        |
| `sessionTimeout < 300s`                      | Sessions may expire too quickly |
| `instanceProfileMaxCount > 50`               | May use significant disk space  |
| `autoLoginEnabled = true` but no credentials | Auto-login won't work           |
