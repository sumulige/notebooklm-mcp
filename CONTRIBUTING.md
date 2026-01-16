# Contributing to NotebookLM MCP

Thank you for your interest in contributing! This guide will help you get started.

---

## Table of Contents

- [Quick Start](#quick-start)
- [Development Setup](#development-setup)
- [Project Structure](#project-structure)
- [Development Workflow](#development-workflow)
- [Testing](#testing)
- [Code Style](#code-style)
- [Submitting Changes](#submitting-changes)
- [Reporting Issues](#reporting-issues)

---

## Quick Start

```bash
# Fork and clone the repository
git clone https://github.com/YOUR_USERNAME/notebooklm-mcp.git
cd notebooklm-mcp

# Install dependencies
npm install

# Start development server
npm run dev

# Run tests
npm test

# Build for production
npm run build
```

---

## Development Setup

### Prerequisites

- **Node.js** 18.0.0 or higher
- **npm** or **yarn**
- **Git**

### Recommended Tools

- **VS Code** with these extensions:
  - ESLint
  - Prettier
  - TypeScript Language Features

### Environment Setup

1. Clone your fork:
   ```bash
   git clone https://github.com/YOUR_USERNAME/notebooklm-mcp.git
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

---

## Project Structure

```
notebooklm-mcp/
├── src/
│   ├── index.ts              # MCP server entry point
│   ├── types.ts              # Shared TypeScript types
│   ├── auth/                 # Authentication management
│   ├── config/               # Configuration & validation
│   ├── session/              # Session & browser management
│   ├── library/              # Notebook library persistence
│   ├── tools/                # MCP tool definitions & handlers
│   ├── resources/            # MCP resource handlers
│   └── utils/                # Utility functions
├── tests/
│   └── unit/                 # Unit tests (Vitest)
├── docs/                     # Documentation
├── dist/                     # Compiled output (gitignored)
└── package.json
```

### Key Modules

| Module | Responsibility |
|--------|---------------|
| `auth/` | Google authentication, session persistence |
| `config/` | Environment parsing, validation with Zod |
| `session/` | Browser automation, session lifecycle |
| `library/` | Notebook metadata storage and retrieval |
| `tools/` | MCP tool definitions and request handlers |

---

## Development Workflow

### Available Scripts

```bash
# Development
npm run dev          # Start server in watch mode (tsx)
npm run watch        # Compile TypeScript in watch mode

# Building
npm run build        # Compile to dist/
npm run prepare      # Build before publish (npm hook)

# Testing
npm test             # Run all tests

# Inspection
npm run check        # TypeScript type checking
```

### Making Changes

1. **Create a branch** from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/your-bug-fix
   ```

2. **Make your changes** following the [Code Style](#code-style) guidelines

3. **Test your changes**:
   ```bash
   npm test
   npm run build
   ```

4. **Commit with a clear message**:
   ```bash
   git add .
   git commit -m "feat: add new tool for XYZ"
   ```

5. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Open a Pull Request** on GitHub

---

## Testing

### Test Framework

We use **Vitest** for unit testing.

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npx vitest watch

# Run tests with coverage
npx vitest --coverage

# Run specific test file
npx vitest tests/unit/config/parsers.test.ts
```

### Writing Tests

Tests are located in `tests/unit/`. Follow this pattern:

```typescript
import { describe, it, expect } from 'vitest';

describe('MyModule', () => {
  it('should do something correctly', () => {
    const result = myFunction('input');
    expect(result).toBe('expected output');
  });

  it('should handle edge cases', () => {
    expect(() => myFunction(null)).toThrow();
  });
});
```

### Test Coverage

- Aim for **80%+ coverage** on new code
- Test both happy paths and error cases
- Mock external dependencies (browser, network)

---

## Code Style

### Guiding Principles

We follow **Linus Torvalds'** programming philosophy:

1. **Eliminate Special Cases**
   - Use elegant data structures to make edge cases disappear naturally
   - Avoid patching with if/else

2. **Minimalism**
   - Functions should do one thing well
   - Use clear, precise naming

3. **Pragmatism**
   - Solve real problems, avoid over-engineering
   - Don't add features "just in case"

### Quality Standards

| Metric | Limit |
|--------|-------|
| Max lines per file | 800 |
| Max files per directory | 8 |
| Circular dependencies | 0 |
| Code duplication | 0 |

### TypeScript Conventions

```typescript
// Use explicit exports
export function myFunction() { }

// Prefer interfaces for object shapes
export interface MyInterface {
  readonly id: string;
  name: string;
}

// Use type aliases for unions
export type MyType = 'option1' | 'option2';

// Async functions return Promise
export async function fetchData(): Promise<Data> { }
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Files | kebab-case | `auth-manager.ts` |
| Classes/Interfaces | PascalCase | `SessionManager` |
| Functions/Variables | camelCase | `getSession` |
| Constants | UPPER_SNAKE_CASE | `MAX_SESSIONS` |
| Types | PascalCase | `SessionConfig` |

---

## Submitting Changes

### Pull Request Guidelines

1. **One PR per feature/fix** - Keep changes focused

2. **Include tests** for new functionality

3. **Update documentation** if behavior changes

4. **Pass all checks**:
   - Tests must pass
   - Build must succeed
   - No new linting errors

### PR Title Format

Use conventional commit format:

| Type | Description |
|------|-------------|
| `feat:` | New feature |
| `fix:` | Bug fix |
| `docs:` | Documentation only |
| `refactor:` | Code change without functional change |
| `test:` | Adding or updating tests |
| `chore:` | Maintenance tasks |

**Examples:**
```
feat: add automatic session recovery
fix: resolve memory leak in browser cleanup
docs: update troubleshooting guide
refactor: simplify config parsing logic
```

### PR Description Template

```markdown
## Summary
Brief description of what this PR does and why.

## Changes
- Change 1
- Change 2

## Testing
- [ ] Tests pass locally
- [ ] Manual testing performed

## Checklist
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] Tests included/updated
```

---

## Reporting Issues

### Before Reporting

1. **Search existing issues** - Your problem may already be reported
2. **Check the docs** - Solutions may exist in [docs/](./docs/)
3. **Try the latest version** - Your issue may be fixed

### Issue Report Template

```markdown
## Description
Clear description of the problem or feature request.

## Steps to Reproduce (for bugs)
1. Step 1
2. Step 2
3. ...

## Expected Behavior
What should happen.

## Actual Behavior
What actually happens.

## Environment
- OS: [e.g. macOS 14.0]
- Node.js: [e.g. 20.10.0]
- Package version: [e.g. 1.2.1]

## Additional Context
Logs, screenshots, or other relevant information.
```

---

## Getting Help

- **GitHub Issues**: For bug reports and feature requests
- **GitHub Discussions**: For questions and community discussion
- **Documentation**: Check [docs/](./docs/) first

---

## License

By contributing, you agree that your contributions will be licensed under the **MIT License**.

---

<div align="center">

**Happy Coding!** 🚀

Questions? Open an issue or start a discussion.

</div>
