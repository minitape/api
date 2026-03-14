# Agent Notes

This file contains instructions for AI agents working with this codebase.

## Repository Structure

This is a protobuf-based API repository that generates code for both Go and TypeScript clients.

```
proto/          Protocol buffer definitions
go/             Generated Go code and Connect-RPC server implementations
js/             Generated TypeScript code (regenerated each build)
script/         Repository automation scripts
```

## Dependency Management

**NPM conventions:**
- Never use `npm install` directly
- Always use `./script/install_codegen_deps` to install or reinstall npm dependencies
- This ensures proper symlink creation for protoc generators in `node_modules/.bin/`

**Go dependencies:**
- Managed via standard `go mod` commands
- Key generators: `protoc-gen-go`, `protoc-gen-connect-go`

## Code Generation Workflow

1. **Install dependencies** (first time or when dependencies are broken):
   ```bash
   ./script/install_codegen_deps
   ```

2. **Generate code** from protobuf definitions:
   ```bash
   ./script/generate
   ```

   This script:
   - Cleans the `js/` directory
   - Formats proto files with `buf format`
   - Updates proto dependencies with `buf dep update`
   - Runs `buf generate` to create Go and TypeScript code
   - Runs `go mod tidy` to update Go dependencies

3. **Test**:
   ```bash
   ./script/test
   ```

4. **Lint**:
   ```bash
   ./script/lint
   ```

## Commit Conventions

**This repository follows [Conventional Commits](https://www.conventionalcommits.org/)** to enable semantic versioning automation.

Commit message format:
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

Common types:
- `feat`: A new feature (triggers MINOR version bump)
- `fix`: A bug fix (triggers PATCH version bump)
- `docs`: Documentation only changes
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `test`: Adding missing tests or correcting existing tests
- `chore`: Changes to the build process or auxiliary tools

Breaking changes:
- Add `!` after type/scope: `feat!: breaking change description`
- Or include `BREAKING CHANGE:` in the footer (triggers MAJOR version bump)

Examples:
```
feat(query): add trace search operator
fix(ingest): handle nil pointer in OTLP conversion
docs: update AGENTS.md with commit conventions
feat!: remove deprecated Trim RPC
```

## Important Conventions

- **Always use scripts in `script/`** for repository operations
- The `js/` directory is fully regenerated on each build - never edit files there manually
- Go code in `go/` contains both generated code and hand-written implementations
- Package management follows the scripts; don't use npm/go commands directly unless you know what you're doing
