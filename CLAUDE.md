# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is `go-jira`, a command line client for Atlassian's Jira service written in Go. It provides a comprehensive CLI interface to interact with Jira instances, supporting both cloud and on-premise installations.

## Development Commands

### Build and Install
- `make build` - Build the jira binary in the current directory
- `make install` - Build and install jira binary to $HOME/bin
- `make all` - Cross-compile binaries for multiple platforms using gox

### Code Quality
- `make vet` - Run go vet on all packages (., jiracli, jiracmd, jiradata, cmd/jira)
- `make lint` - Run golint on all packages (installs golint if needed)
- `go test ./...` - Run Go unit tests (files ending in _test.go)

### Integration Tests
- `make prove` - Run full integration test suite using osht framework
- `./000setup.t` - Set up Docker-based test Jira instance (run first)
- `./<test-name>.t` - Run individual integration tests with optional `-v` (verbose) and `-a` (abort on first failure) flags

### Maintenance
- `make clean` - Remove build artifacts and password store files
- `make generate` - Regenerate schemas and data structures

## Architecture

### Core Packages

- **cmd/jira/** - Main entry point (main.go) and CLI initialization
- **jiracli/** - CLI utilities, logging, templating, password management, keyring integration
- **jiracmd/** - All CLI command implementations (create, list, edit, assign, etc.)
- **jiradata/** - Go structs representing Jira API data types and JSON marshaling
- **Root level** - Core Jira client logic (jira.go, session.go, search.go, etc.)

### Configuration System

Uses hierarchical configuration via `.jira.d/` directories:
1. Searches up directory tree for `.jira.d/` folders
2. Falls back to `$HOME/.jira.d/` if not found
3. Loads `<command>.yml` files for command-specific config
4. Merges with `config.yml` for global settings
5. Supports executable config files for dynamic configuration

### Template System

- Templates located in `.jira.d/templates/`
- Use Go's text/template syntax with custom helper functions
- Export defaults with `jira export-templates`
- Debug templates with `jira <command> -t debug`

### Testing Strategy

- **Unit Tests**: `*_test.go` files using standard Go testing
- **Integration Tests**: `_t/*.t` files using osht bash testing framework
- **Test Environment**: Docker-based Jira instance for integration testing
- Integration tests require running `000setup.t` first to start test service

### Custom Commands

Supports user-defined commands via `.jira.d/config.yml`:
- Shell script-based with documented options/arguments
- Template substitution for arguments and options
- Environment variable access (JIRA_* variables)

## Key Development Notes

- Go modules enabled (GO111MODULE=on when building)
- Supports multiple authentication methods (session, API token, bearer token)
- Password storage via keyring, pass, gopass, or stdin
- Cross-platform support (Windows, macOS, Linux)
- Extensive template customization for output formatting
- Context-aware configuration based on current working directory