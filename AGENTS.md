# HaLOS Cockpit Config - Agentic Coding Guide

**LAST MODIFIED**: 2026-02-22

**Document Purpose**: Guide for AI assistants working on halos-cockpit-config.

## For Agentic Coding: Use the HaLOS Workspace

**IMPORTANT**: When using Claude Code or other AI assistants, work from the halos workspace repository for full context across all HaLOS repositories.

```bash
# Work from workspace
cd halos/
# This repo is available as: halos-cockpit-config/
```

**Development Workflows**: See the [halos workspace docs](https://github.com/halos-org/halos/tree/main/docs) for `DEVELOPMENT_WORKFLOW.md` (detailed Claude Code workflows), `PROJECT_PLANNING_GUIDE.md`, and `IMPLEMENTATION_CHECKLIST.md`.

## About This Project

HaLOS-specific configuration for the Cockpit web interface. This package integrates Cockpit into the HaLOS ecosystem by providing:

- **Branding**: Hat Labs logo, colors, CSS, and favicon
- **Dashboard integration**: Homarr registry entry (cockpit.toml)

## Git Workflow Policy

**MANDATORY**: PRs must ALWAYS have all checks passing before merging. No exceptions.

**Branch Workflow:** Never push to main directly - always use feature branches and PRs.

## Quick Start

```bash
# Build package
./run build-debtools  # First time only
./run build-deb

# Check quality
./run lint-deb

# Clean build artifacts
./run clean
```

## Version Management

**VERSION file** is the canonical version source (e.g., `0.5.4`). Managed by `./run bumpversion [patch|minor|major]`.

**`.bumpversion.cfg`** tracks the current version and updates the VERSION file. The working directory must be clean before bumping — bumpversion automatically commits the change.

**`debian/changelog`** is generated dynamically by CI at build time (see [shared-workflows `build-release.yml`](https://github.com/halos-org/shared-workflows/blob/main/.github/workflows/build-release.yml)). The checked-in version is only used for local dev builds and will typically be out of sync with VERSION. This is expected — never manually edit `debian/changelog`.

**CI enforcement**: VERSION bumps are *per release cycle*, not per PR. Default: do NOT bump `VERSION` in feature PRs — CI auto-increments the `+N` revision in release tags (e.g., `v0.5.6+1`, `+2`, `+3`) between stable releases. Bump `VERSION` only when starting a new release cycle, i.e., when `VERSION` currently matches the latest stable tag and this PR is opening the next cycle. If `VERSION` already differs from the latest stable tag, no further bump is needed regardless of how many package-affecting files this PR touches. Docs, tests, CI config, and dev tooling are automatically excluded from the check.

## Project Structure

```
halos-cockpit-config/
├── docs/
│   ├── SPEC.md              # Technical specification
│   └── ARCHITECTURE.md      # System architecture
├── debian/                  # Debian package files
│   ├── control             # Package metadata
│   ├── changelog           # Package changelog
│   ├── copyright           # License information
│   ├── install             # File installation rules
│   ├── preinst             # Pre-install script (dpkg-divert)
│   ├── postrm              # Post-remove script (cleanup)
│   ├── rules               # Build rules
│   └── source/format       # Source format
├── docker/                  # Build container
│   ├── Dockerfile.debtools
│   └── docker-compose.debtools.yml
├── etc/
│   ├── cockpit/branding/   # Branding source files
│   │   ├── branding.css
│   │   ├── logo.svg
│   │   ├── logo.png
│   │   ├── apple-touch-icon.png
│   │   └── favicon.ico
│   └── halos/
│       └── webapps.d/
│           └── cockpit.toml  # Homarr dashboard registry
└── run                      # Build script
```

## Related Packages

- **traefik-container**: Provides path-based redirect to Cockpit's native HTTPS
- **homarr-container-adapter**: Reads cockpit.toml for dashboard integration
