## RFC: Prime Directive / Bootstrap Command for Tools

### 1. Overview

This document specifies a **Prime Directive** mechanism for tools (CLI and MCP) via a standardized `prime` / `bootstrap` command.

**Prime Directive definition**

> A mandatory, idempotent initialization command that an agent MUST invoke before any substantive use of a tool in a session, in order to obtain configuration, constraints, and guidance governing all subsequent calls.

### 2. Goals & Non‑Goals

**Goals**

- Provide a single, consistent initialization entrypoint (`prime`) across tools.
- Allow tools to:
  - communicate **usage directives** (“do/don’t”),
  - expose **capabilities** and **rate limits**,
  - describe **preferred / deprecated** methods.
- Enable agents to automatically adapt to tool‑specific constraints per session.

**Non‑Goals**

- This RFC does not define authentication or authorization semantics.
- This RFC does not prescribe transport (HTTP, gRPC, MCP, local process).
- This RFC does not define business‑specific payloads for other commands.

### 3. Terminology

- **Agent**: An LLM‑driven or programmatic orchestrator that calls tools.
- **Tool**: A CLI or MCP service exposed as commands / methods.
- **Session**: A logical scope (e.g., conversation, workflow run) within which initialization and subsequent tool calls are correlated.
- **Prime Directive (`prime` command)**: A mandatory initialization command used to establish context and constraints for a session.

### 4. Requirements

#### 4.1 Must‑Call Rule

- Agents **MUST** invoke `prime` for a given `(tool, session_id)` before calling any non‑`prime` method for that tool in that session.
- Agents **MAY** invoke `prime` again in the same session, but tools **MUST** treat it idempotently (see below).

#### 4.2 Idempotence

- For a given `(agent_id, session_id)` pair, repeated `prime` calls:
  - **MUST** return logically consistent `usageDirectives`, `rateLimits`, and `schema` fields.
  - **MUST NOT** cause side effects beyond logging/telemetry or benign cache updates.
- Tools **MAY** refresh time‑sensitive fields (e.g., `expiresAt`) as long as semantics remain unchanged.

#### 4.3 Directive Precedence

- Agents **SHOULD** treat `usageDirectives` returned by `prime` as higher priority than generic heuristics when deciding:
  - whether to call the tool,
  - which methods to prefer,
  - how often or in what sequence to call methods.
- Global safety and policy layers **MAY** override any directive.

#### 4.4 Versioning

- Tools **MUST** include a `version` field in `prime` responses.
- Tools **SHOULD** include optional fields for compatibility (e.g., `breakingChangeSince`, `minAgentVersion`) to allow agents to gracefully degrade or adjust behavior.

### 5. Prime Command Interface

The `prime` command is defined by:

- **Input**: A request object describing the agent and session context.
- **Output**: A response object containing directives and configuration.

Both are formally described in the JSON Schema in Section 8.

#### 5.1 Prime Request (Conceptual)

Fields:

- `agentId` (string, required): Identifier of the calling agent or persona.
- `sessionId` (string, required): Identifier of the logical session.
- `capabilities` (object, optional): Declares what the agent can handle (e.g., streaming, pagination).
- `locale` (string, optional): Locale hint, e.g., `en-US`.
- `userRole` (string, optional): Role of the ultimate user (`end_user`, `admin`, `system`, etc.).
- `metadata` (object, optional): Arbitrary additional hints.

#### 5.2 Prime Response (Conceptual)

Fields:

- `version` (string, required): Version of the prime schema/contract implemented by the tool.
- `toolName` (string, optional but recommended): Human‑readable tool name.
- `session` (object, required):
  - `sessionId` (string, required): Echo or derived session identifier.
  - `expiresAt` (string, date-time, optional): When this session context should be treated as expired.
- `usageDirectives` (object, required):
  - `primaryIntents` (string[], optional): High‑level intended uses.
  - `do` (string[], optional): Positive instructions for agents.
  - `dont` (string[], optional): Negative instructions / prohibitions.
- `capabilities` (object, optional): Tool‑provided capability flags and limits.
- `rateLimits` (object, optional):
  - e.g., `requestsPerMinute`, `burst`, other rate limit info.
- `schema` (object, optional):
  - `preferredCommands` (string[]): Methods/commands agents should favor.
  - `deprecatedCommands` (string[]): Methods/commands agents should avoid.
  - Additional fields for tool method metadata as needed.
- `examples` (array, optional): Example call sequences / flows.

### 6. CLI Mapping

For CLI‑style tools, the `prime` request is typically mapped to a command such as:

```bash
tool prime \
  --agent-id "<agent-id>" \
  --session-id "<session-id>" \
  --capabilities '{"supportsStreaming": true}' \
  --locale "en-US" \
  --user-role "end_user"
```

The tool **MUST**:

- Parse these inputs into the conceptual `PrimeRequest`.
- Emit a JSON `PrimeResponse` matching the schema in Section 8 to stdout (or another agreed channel).

Example response (abbreviated):

```json
{
  "version": "1.0.0",
  "toolName": "example-tool",
  "session": {
    "sessionId": "abc-123",
    "expiresAt": "2026-01-25T12:00:00Z"
  },
  "usageDirectives": {
    "primaryIntents": ["summarization", "report_generation"],
    "do": [
      "Use this tool only for batch operations.",
      "Prefer 'analyze' before 'execute' for safety."
    ],
    "dont": [
      "Do not send PII payloads.",
      "Do not call 'execute' more than once per session."
    ]
  },
  "schema": {
    "preferredCommands": ["analyze", "execute"],
    "deprecatedCommands": ["legacy-run"]
  }
}
```

### 7. MCP Mapping

For MCP tools, `prime` is exposed as a method in the tool’s manifest/schema:

- **Method name**: `prime`
- **Description**: MUST clearly state that the method is mandatory before other calls and is idempotent.
- **Parameters**: MUST align with the `PrimeRequest` schema.
- **Result**: MUST align with the `PrimeResponse` schema.

Agents using MCP:

1. On first use of a tool in a session, call `prime`.
2. Cache the response keyed by `(tool, sessionId)`.
3. Enforce `usageDirectives`, `rateLimits`, and `schema` for subsequent calls.

### 8. JSON Schema

This section defines canonical JSON Schema documents for the `prime` request and response.

#### 8.1 `PrimeRequest` Schema

```json
{
  "$id": "https://example.com/schemas/prime-request.json",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "PrimeRequest",
  "description": "Input schema for the prime/bootstrapping command for tools.",
  "type": "object",
  "properties": {
    "agentId": {
      "type": "string",
      "description": "Identifier for the calling agent or persona."
    },
    "sessionId": {
      "type": "string",
      "description": "Identifier for the logical session or conversation."
    },
    "capabilities": {
      "type": "object",
      "description": "Description of agent capabilities and preferences (e.g., streaming support, max tokens).",
      "additionalProperties": true
    },
    "locale": {
      "type": "string",
      "description": "Locale hint for responses, e.g. 'en-US'."
    },
    "userRole": {
      "type": "string",
      "description": "Role of the ultimate user for this session.",
      "enum": ["end_user", "admin", "system"],
      "default": "end_user"
    },
    "metadata": {
      "type": "object",
      "description": "Arbitrary additional hints or context for the tool.",
      "additionalProperties": true
    }
  },
  "required": ["agentId", "sessionId"],
  "additionalProperties": false
}
```

#### 8.2 `PrimeResponse` Schema

```json
{
  "$id": "https://example.com/schemas/prime-response.json",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "PrimeResponse",
  "description": "Output schema for the prime/bootstrapping command for tools.",
  "type": "object",
  "properties": {
    "version": {
      "type": "string",
      "description": "Version of the prime contract implemented by the tool (e.g., '1.0.0')."
    },
    "toolName": {
      "type": "string",
      "description": "Human-readable name of the tool."
    },
    "session": {
      "type": "object",
      "description": "Session-level information and context.",
      "properties": {
        "sessionId": {
          "type": "string",
          "description": "Identifier of the initialized session (may echo the request sessionId)."
        },
        "expiresAt": {
          "type": "string",
          "format": "date-time",
          "description": "Timestamp after which the agent SHOULD treat this session context as expired."
        }
      },
      "required": ["sessionId"],
      "additionalProperties": false
    },
    "usageDirectives": {
      "type": "object",
      "description": "Guidance and constraints that MUST govern subsequent tool usage.",
      "properties": {
        "primaryIntents": {
          "type": "array",
          "description": "High-level intended uses of this tool.",
          "items": {
            "type": "string"
          }
        },
        "do": {
          "type": "array",
          "description": "Positive instructions for agents when using this tool.",
          "items": {
            "type": "string"
          }
        },
        "dont": {
          "type": "array",
          "description": "Negative instructions / prohibitions for agents when using this tool.",
          "items": {
            "type": "string"
          }
        }
      },
      "required": [],
      "additionalProperties": false
    },
    "capabilities": {
      "type": "object",
      "description": "Tool-provided capabilities and configuration flags.",
      "additionalProperties": true
    },
    "rateLimits": {
      "type": "object",
      "description": "Rate limiting information the agent SHOULD respect.",
      "properties": {
        "requestsPerMinute": {
          "type": "number",
          "description": "Recommended maximum requests per minute for this tool."
        },
        "burst": {
          "type": "number",
          "description": "Recommended burst size of requests in a short window."
        }
      },
      "additionalProperties": true
    },
    "schema": {
      "type": "object",
      "description": "Meta-schema describing preferred and deprecated commands/methods.",
      "properties": {
        "preferredCommands": {
          "type": "array",
          "description": "Commands or methods the agent SHOULD prefer using.",
          "items": {
            "type": "string"
          }
        },
        "deprecatedCommands": {
          "type": "array",
          "description": "Commands or methods the agent SHOULD avoid using.",
          "items": {
            "type": "string"
          }
        }
      },
      "additionalProperties": true
    },
    "examples": {
      "type": "array",
      "description": "Example usage flows or sequences.",
      "items": {
        "type": "object",
        "properties": {
          "description": {
            "type": "string",
            "description": "Human-readable description of the example flow."
          },
          "sequence": {
            "type": "array",
            "description": "Ordered list of command/method names representing a typical flow.",
            "items": {
              "type": "string"
            }
          }
        },
        "required": ["description", "sequence"],
        "additionalProperties": false
      }
    },
    "breakingChangeSince": {
      "type": "string",
      "description": "Optional version string indicating from which version a breaking change occurred."
    },
    "minAgentVersion": {
      "type": "string",
      "description": "Optional minimum agent version required to fully support this tool contract."
    }
  },
  "required": ["version", "session", "usageDirectives"],
  "additionalProperties": false
}
```
