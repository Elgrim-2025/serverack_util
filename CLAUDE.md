# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**ServerLack** is a single-file web application for creating interactive server rack diagrams. It's a standalone HTML file that combines an editor and viewer in one, allowing users to:

1. Upload a server rack photo
2. Draw rectangular zones over server units
3. Annotate each zone with name, description, and custom attributes
4. Export a self-contained HTML viewer file

The application is written entirely in vanilla JavaScript with no external dependencies.

## Architecture

### Single-File Design
The entire application exists in `index.html` - this is a deliberate architectural choice. The file contains:
- HTML structure for editor UI and workspace
- Embedded CSS styles (dark theme UI, zone overlays, sidebar panels)
- Inline JavaScript for all application logic
- Export functionality that generates a standalone viewer HTML

### Key Components

**Editor Mode** (lines 1-536):
- Two-panel layout: workspace (left) + sidebar inspection panel (right)
- Toolbar with image upload, project import/export, and zone drawing toggle
- Drawing system using mouse events (mousedown/mousemove/mouseup) on workspace
- Zone storage as percentage-based coordinates for responsive scaling

**Viewer Mode** (generated on export, lines 432-522):
- Read-only version embedded in exported HTML
- Click zones to display server info in side panel
- Self-contained with base64-encoded image data and JSON zone data

### Data Model

Zones are stored as JavaScript objects:
```javascript
{
    id: number,           // Unique identifier
    x: number,           // Left position (% of image width)
    y: number,           // Top position (% of image height)
    w: number,           // Width (% of image width)
    h: number,           // Height (% of image height)
    name: string,        // Server name (displayed as label overlay)
    desc: string,        // Description/notes
    attrs: Array<{       // Custom key-value attributes
        key: string,
        val: string
    }>
}
```

Coordinates are stored as percentages (0-100) relative to the image dimensions, ensuring zones scale correctly when the image is resized.

### State Management

Global state variables (lines 143-149):
- `zones[]`: Array of all zone objects
- `isAddingMode`: Boolean for zone drawing mode
- `isDrawing`: Active drawing state
- `selectedZoneId`: Currently selected zone for editing
- `nextId`: Auto-increment counter for new zones

### File Format

**Export/Import**: Projects are saved as complete HTML files containing:
1. Base64-encoded image in `<img src="data:image/...">`
2. Zones data as `const zones = [...]` in embedded script
3. Complete viewer interface for sharing/presentation

The import function (lines 179-210) parses these HTML files using regex to extract image data and zone configuration.

## Development Notes

### Zone Rendering
The `renderZones()` function (lines 308-334) recreates all zone overlays from scratch on each update. Each zone includes:
- Clickable overlay div positioned absolutely over the image
- Label overlay showing zone name (lines 322-326)
- Selection highlighting via CSS classes

### Live Updates
The name input field updates zone labels in real-time without full re-render (lines 360-374) by directly manipulating the DOM element for the selected zone.

### Coordinate System
All zone positions use percentage-based coordinates calculated relative to the workspace dimensions (lines 282-292). This ensures zones maintain correct positions when:
- The browser window is resized
- The image is displayed at different scales
- The file is viewed on different devices

### Export Process
The `exportSingleHtml()` function (lines 422-533) generates a complete viewer by:
1. Reading the current image as base64 data URL
2. Serializing zones array to JSON
3. Embedding both into an HTML template string
4. Creating a downloadable blob

## Language and UI

The application is in Korean (한국어):
- All UI labels and messages are in Korean
- Comments in code are also in Korean
- Default zone names use "Server" prefix with incrementing numbers
