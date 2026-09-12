# Gemini CLI Architecture Overview

This document provides a visual overview of the Gemini CLI project architecture, including key components, packages, and their relationships.

## System Architecture

```mermaid
graph TB
    User["👤 User"]
    Terminal["🖥️ Terminal"]
    
    subgraph CLI["🎯 CLI Layer (packages/cli)"]
        Input["Input Parser<br/>Command Handling"]
        UI["UI Renderer<br/>React + Ink"]
        Output["Output Formatter<br/>Display Logic"]
    end
    
    subgraph Core["⚙️ Core Engine (packages/core)"]
        Router["Model Router<br/>Route to Gemini"]
        Prompt["Prompt Constructor<br/>Context Building"]
        Executor["Tool Executor<br/>Execute Commands"]
        Cache["Cache Manager<br/>Response Caching"]
    end
    
    subgraph API["🌐 External Services"]
        Gemini["Gemini API<br/>AI Model"]
        MCP["MCP Server<br/>Model Context Protocol"]
    end
    
    subgraph Packages["📦 Supporting Packages"]
        SDK["SDK<br/>Programmatic API"]
        Devtools["Devtools<br/>Network/Console Inspector"]
        TestUtils["Test Utils<br/>Test Utilities"]
    end
    
    subgraph VSCode["VS Code Integration"]
        Companion["IDE Companion<br/>Extension"]
    end
    
    subgraph A2A["🔄 Experimental"]
        Server["A2A Server<br/>Agent-to-Agent"]
    end
    
    User -->|Enters Commands| Terminal
    Terminal -->|CLI Input| Input
    Input -->|Process| UI
    UI -->|Render| Terminal
    Input -->|Route Request| Router
    
    Router -->|Query| Gemini
    Router -->|Connect| MCP
    Router -->|Build Context| Prompt
    
    Prompt -->|Execute Tools| Executor
    Executor -->|Cache Results| Cache
    
    UI -->|Display Results| Output
    Output -->|Render Output| Terminal
    
    SDK -->|Embed| Packages
    Devtools -->|Debug| Core
    TestUtils -->|Test| Packages
    
    Companion -->|Sync with| CLI
    Server -->|Communicate| Core
    
    style CLI fill:#e1f5ff
    style Core fill:#f3e5f5
    style API fill:#fff3e0
    style Packages fill:#e8f5e9
    style VSCode fill:#fce4ec
    style A2A fill:#f1f8e9
```

## Component Details

### CLI Layer (`packages/cli`)
- **Input Parser**: Handles command-line argument parsing and user input
- **UI Renderer**: React-based terminal UI powered by [Ink](https://github.com/vadimdemedes/ink)
- **Output Formatter**: Formats and displays results back to the user

### Core Engine (`packages/core`)
- **Model Router**: Routes queries to appropriate Gemini models based on request type
- **Prompt Constructor**: Builds optimized prompts with context and history
- **Tool Executor**: Executes external tools and commands as directed by the model
- **Cache Manager**: Manages response caching to improve performance

### External Services
- **Gemini API**: Google's AI model providing intelligence and reasoning
- **MCP Server**: Model Context Protocol integration for extensibility

### Supporting Packages
- **SDK** (`packages/sdk`): Allows programmatic embedding of Gemini CLI capabilities
- **Devtools** (`packages/devtools`): Integrated network and console inspection tools
- **Test Utils** (`packages/test-utils`): Shared testing utilities and test rigs

### VS Code Integration
- **IDE Companion** (`packages/vscode-ide-companion`): VS Code extension for paired IDE experience

### Experimental
- **A2A Server** (`packages/a2a-server`): Agent-to-Agent communication server

## Data Flow

```mermaid
sequenceDiagram
    participant User
    participant CLI as CLI Layer
    participant Core as Core Engine
    participant Gemini as Gemini API
    participant Tools as External Tools
    
    User->>CLI: Enter command
    CLI->>Core: Route request
    Core->>Core: Build context & prompt
    Core->>Gemini: Send query
    Gemini->>Core: Return response
    
    alt Tools needed
        Core->>Tools: Execute tool calls
        Tools->>Core: Return results
        Core->>Gemini: Send results + context
        Gemini->>Core: Generate response
    end
    
    Core->>CLI: Return result
    CLI->>User: Display output
```

## Technology Stack

| Layer | Technology |
|-------|-----------|
| **Runtime** | Node.js >= 20.0.0 |
| **Language** | TypeScript |
| **UI Framework** | React + Ink (CLI) |
| **Testing** | Vitest |
| **Bundling** | esbuild |
| **Package Management** | npm workspaces |
| **Code Quality** | ESLint, Prettier |

## Monorepo Structure

```
gemini-cli/
├── packages/
│   ├── cli/                 # User-facing terminal UI
│   ├── core/                # Backend logic & Gemini orchestration
│   ├── sdk/                 # Programmatic SDK
│   ├── devtools/            # Developer tools
│   ├── test-utils/          # Shared test utilities
│   └── vscode-ide-companion/# VS Code extension
├── integration-tests/       # E2E integration tests
├── evals/                   # Evaluation scripts
├── docs/                    # Documentation
└── scripts/                 # Build & utility scripts
```

## Build Pipeline

```mermaid
graph LR
    Source["Source Code<br/>TypeScript"]
    Lint["Lint & Format<br/>ESLint, Prettier"]
    Compile["Compile<br/>TypeScript → JavaScript"]
    Bundle["Bundle<br/>esbuild"]
    Test["Test<br/>Vitest"]
    Output["Output<br/>Distribution"]
    
    Source -->|Check| Lint
    Lint -->|Pass| Compile
    Compile -->|Run| Test
    Test -->|Pass| Bundle
    Bundle --> Output
    
    style Source fill:#e3f2fd
    style Lint fill:#fff3e0
    style Compile fill:#f3e5f5
    style Bundle fill:#e8f5e9
    style Test fill:#fce4ec
    style Output fill:#c8e6c9
```

## Development Workflow

1. **Clone & Install**: `npm install`
2. **Build**: `npm run build`
3. **Run**: `npm run start` (development mode)
4. **Debug**: `npm run debug` (Node.js inspector enabled)
5. **Test**: `npm run test` (unit tests) or `npm run test:e2e` (integration tests)
6. **Validate**: `npm run preflight` (comprehensive checks before PR submission)

## Key Features

- **Terminal-First**: Optimized for CLI workflows
- **Extensible**: MCP support for custom integrations
- **Powerful**: Full access to Gemini's reasoning and code capabilities
- **Developer-Friendly**: Built-in debugging tools and comprehensive test suite
- **Cross-Platform**: Works on macOS, Linux, and Windows

---

*For more details, see the [Contributing Guide](../CONTRIBUTING.md) and [Project README](../README.md).*
