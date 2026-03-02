# HaLOS Cockpit Config - Technical Specification

**Last Modified**: 2025-12-29

## Project Overview

HaLOS-specific configuration package for the Cockpit web interface. This package integrates Cockpit into the HaLOS ecosystem by providing branding and Homarr dashboard integration.

## Goals

- Provide consistent HaLOS branding across all Cockpit installations
- Integrate Cockpit into the Homarr dashboard
- Package all configuration for easy installation and distribution
- Handle conflicts with existing cockpit-ws package files safely
- Integrate seamlessly into HaLOS image builds
- Follow Debian packaging best practices

## Core Features

### Branding Files

The package provides the following branding assets:

- **CSS Styling**: Custom CSS that defines HaLOS branding colors and layout
- **Logo (SVG)**: Scalable vector graphics logo as primary format
- **Logo (PNG)**: Raster fallback logo at 100x100 pixels
- **Apple Touch Icon**: iOS home screen icon at 180x180 pixels
- **Favicon**: Browser tab icon in ICO format

### Visual Branding Elements

- **Badge (#badge)**: Hat Labs logo displayed in upper-right corner of login screen (100x100px)
- **Brand Text (#brand)**: "HaLOS" displayed prominently above login fields
- **Accent Color**: Custom blue-gray color (#576679) matching Hat Labs brand identity

### Homarr Dashboard Integration

The package provides a registry entry (`/etc/halos/webapps.d/cockpit.toml`) that:

- Registers Cockpit in the Homarr dashboard
- Provides the path-based URL for accessing Cockpit
- Configures the dashboard card layout and category

## Technical Requirements

### Packaging

- Package name: `halos-cockpit-config`
- Architecture: `all` (no platform-specific binaries)
- Depends on: `cockpit`
- Provides: `halos-cockpit-branding`, `cockpit-branding-halos` (for backwards compatibility)
- License: MIT
- Maintainer: Matti Airas <matti.airas@hatlabs.fi>

### File Installation

- Branding files: `/usr/share/cockpit/branding/debian/`
- Dashboard registry: `/etc/halos/webapps.d/cockpit.toml`
- Use `dpkg-divert` to safely replace branding files owned by cockpit-ws package

### Build System

- Docker-based build environment for consistency
- Debian Trixie base image
- Standard Debian packaging tools (debhelper, devscripts, lintian)
- Build script (`./run`) for convenience

## Key Constraints

### File Ownership Conflicts

Cockpit's default branding files are owned by the cockpit-ws package. To replace them without causing dpkg conflicts, the package must:

- Use `dpkg-divert` in preinst script to move original files aside
- Install replacement files to the same location
- Clean up diversions in postrm script when package is removed

### Cockpit Integration

Cockpit loads branding from `/usr/share/cockpit/branding/debian/` by default on Debian-based systems. Files must be installed to this exact location to be recognized by Cockpit without configuration changes.

## Non-Functional Requirements

### Build Quality

- Package must build cleanly without errors
- Lintian checks should pass (except expected warning about ITP bug for non-Debian packages)
- All files should have correct permissions

### Installation

- Package must install without errors
- Must handle upgrades and downgrades gracefully
- Removal should restore original cockpit-ws branding files
- mDNS service should be enabled automatically

### Testing

- Package must be tested on actual Cockpit installation
- Branding must be visible after installation and Cockpit restart
- Package removal must restore original branding

## Out of Scope

### Future Enhancements

- Dynamic branding configuration (colors, logos) - current version uses fixed branding
- Multiple branding themes - only HaLOS branding provided
- Automatic theme switching - no dark mode support
- Custom login screen layouts - only modifies existing elements

### Not Included

- Modifications to Cockpit functionality - only visual branding and integration
- Custom Cockpit modules - separate concern
- Traefik routing configuration - handled by traefik-container package
