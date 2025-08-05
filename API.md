# Superdesign MCP Server API Documentation

This document provides comprehensive API documentation for the Superdesign MCP Server tools. Each tool is designed to work with Cline through the Model Context Protocol (MCP) to provide sophisticated design generation and management capabilities.

## Table of Contents

- [MCP Protocol Basics](#mcp-protocol-basics)
- [Tool Categories](#tool-categories)
- [Design Generation Tools](#design-generation-tools)
- [File Management Tools](#file-management-tools)
- [Gallery Tools](#gallery-tools)
- [System Extraction Tools](#system-extraction-tools)
- [Utility Tools](#utility-tools)
- [Workflow Examples](#workflow-examples)
- [Error Handling](#error-handling)
- [Troubleshooting](#troubleshooting)

## MCP Protocol Basics

The Superdesign MCP Server communicates with Cline using the Model Context Protocol. Each tool call follows this structure:

### Request Format
```json
{
  "method": "tools/call",
  "params": {
    "name": "tool_name",
    "arguments": {
      "parameter1": "value1",
      "parameter2": "value2"
    }
  }
}
```

### Response Format
```json
{
  "content": [
    {
      "type": "text",
      "text": "Tool response content"
    }
  ]
}
```

## Tool Categories

### Design Generation Tools
- `superdesign_generate` - Generate new designs from prompts
- `superdesign_iterate` - Iterate on existing designs with feedback

### File Management Tools
- `superdesign_list` - List all designs in workspace
- `superdesign_delete` - Delete specific design files
- `superdesign_cleanup` - Clean up old design files

### Gallery Tools
- `superdesign_gallery` - Generate static HTML gallery
- `superdesign_live_gallery` - Start live gallery server

### System Extraction Tools
- `superdesign_extract_system` - Extract design systems from images

### Utility Tools
- `superdesign_check_files` - Check for file changes

---

## Design Generation Tools

### superdesign_generate

**Purpose**: Generate new design files based on prompts and specifications.

**Zod Schema**:
```typescript
const GenerateDesignSchema = z.object({
  prompt: z.string().describe("Design prompt describing what to create"),
  design_type: z.enum(["ui", "wireframe", "component", "logo", "icon"]).describe("Type of design to generate"),
  variations: z.number().min(1).max(5).default(3).describe("Number of design variations to create"),
  framework: z.enum(["html", "react", "vue"]).default("html").describe("Framework for UI components")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_generate",
    "arguments": {
      "prompt": "modern dashboard UI with dark theme",
      "design_type": "ui",
      "variations": 3,
      "framework": "html"
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "DESIGN SPECIFICATION FOR CLINE:\n\nIMPORTANT: You must generate and save the following design files based on these specifications.\n\n=== DESIGN PARAMETERS ===\n- Type: ui\n- Prompt: modern dashboard UI with dark theme\n- Framework: html\n- Files to create: 3 variations\n- File format: HTML\n\n=== FILES TO CREATE ===\n1. superdesign/design_iterations/modern_dashboard_ui_1.html\n2. superdesign/design_iterations/modern_dashboard_ui_2.html\n3. superdesign/design_iterations/modern_dashboard_ui_3.html\n\n=== SUPERDESIGN SYSTEM PROMPT ===\n[Complete Superdesign guidelines and constraints]\n\n=== EXECUTION INSTRUCTIONS ===\n1. Create the superdesign/design_iterations directory if it doesn't exist\n2. Generate 3 unique variations of the ui based on the prompt\n3. Save each variation with the exact filenames listed above\n4. Follow all Superdesign guidelines and constraints\n5. Use superdesign_gallery tool to generate and open the gallery"
    }
  ]
}
```

**Cline Usage Examples**:
- "Generate a modern dashboard UI design"
- "Create 3 variations of a login page wireframe"
- "Design a React component for a product card"
- "Make a minimalist logo for a tech startup"

**Design Types**:
- **ui**: Complete responsive interfaces with Tailwind CSS
- **wireframe**: Minimal black and white layouts (Balsamiq style)
- **component**: Individual UI components with mock data
- **logo**: SVG logo designs with vector graphics
- **icon**: SVG icon designs with clean, scalable vectors

**Framework Support**:
- **html**: Standalone HTML files with Tailwind CSS via CDN
- **react**: React components with JSX syntax and proper structure
- **vue**: Vue.js components with template, script, and style sections

**Validation Rules**:
- `prompt`: Required, non-empty string
- `design_type`: Must be one of the 5 supported types
- `variations`: Integer between 1-5 (default: 3)
- `framework`: Must be html, react, or vue (default: html)

**Common Errors**:
- `Invalid design_type`: Provided type not in allowed enum values
- `Variations out of range`: Number not between 1-5
- `Empty prompt`: Prompt parameter is required and cannot be empty

---

### superdesign_iterate

**Purpose**: Iterate on existing design files with feedback and improvements.

**Zod Schema**:
```typescript
const IterateDesignSchema = z.object({
  design_file: z.string().describe("Path to existing design file to iterate on"),
  feedback: z.string().describe("Feedback for improving the design"),
  variations: z.number().min(1).max(5).default(3).describe("Number of design variations to create")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_iterate",
    "arguments": {
      "design_file": "superdesign/design_iterations/dashboard_1.html",
      "feedback": "Make the sidebar more prominent and add better spacing between cards",
      "variations": 2
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "DESIGN ITERATION SPECIFICATION FOR CLINE:\n\nIMPORTANT: You must iterate on the existing design and save the improved versions.\n\n=== ITERATION PARAMETERS ===\n- Original file: superdesign/design_iterations/dashboard_1.html\n- Feedback: Make the sidebar more prominent and add better spacing between cards\n- Files to create: 2 improved variations\n- File format: HTML\n\n=== FILES TO CREATE ===\n1. superdesign/design_iterations/dashboard_1_1.html\n2. superdesign/design_iterations/dashboard_1_2.html\n\n=== ORIGINAL DESIGN ===\n[Complete original file content]\n\n=== ITERATION GUIDELINES ===\n1. Analyze the original design above\n2. Apply the following feedback: Make the sidebar more prominent and add better spacing between cards\n3. Create 2 different improvements based on the feedback\n4. Each variation should interpret the feedback slightly differently\n5. Maintain the core structure while implementing improvements"
    }
  ]
}
```

**Cline Usage Examples**:
- "Iterate on the dashboard design with better spacing"
- "Improve the login form with more modern styling"
- "Update the product card component to be more accessible"

**Input Validation**:
- Verifies design file exists before processing
- Validates feedback is non-empty string
- Ensures variations count is within 1-5 range

**File Naming Convention**:
- Original: `design_1.html`
- Iterations: `design_1_1.html`, `design_1_2.html`, etc.

**Common Errors**:
- `File not found`: Specified design_file doesn't exist
- `Empty feedback`: Feedback parameter is required
- `Invalid variations`: Number not between 1-5

---

## File Management Tools

### superdesign_list

**Purpose**: List all design files and design systems in the workspace.

**Zod Schema**:
```typescript
const ListDesignsSchema = z.object({
  workspace_path: z.string().optional().describe("Workspace path (defaults to current directory)")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_list",
    "arguments": {
      "workspace_path": "/path/to/project"
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "Superdesign workspace: /path/to/project/superdesign\n\nDesign iterations (8):\n  - calculator_glassmorphism_3.html\n  - calculator_minimalist_1.html\n  - calculator_neumorphic_2.html\n  - calculator_retro_4.html\n  - classic_winamp_1.html\n  - minimal_winamp_3.html\n  - neon_winamp_2.html\n  - wood_winamp_4.html\n\nDesign systems (2):\n  - design-system.json\n  - design-system_2.json"
    }
  ]
}
```

**Cline Usage Examples**:
- "List all my designs"
- "Show me what design files I have"
- "What's in my superdesign workspace?"

**Directory Structure**:
- `superdesign/design_iterations/` - Generated designs (HTML/SVG files)
- `superdesign/design_system/` - Extracted design systems (JSON files)
- `superdesign/metadata.json` - Design file metadata
- `superdesign/cleanup-settings.json` - Cleanup configuration

---

### superdesign_delete

**Purpose**: Delete a specific design file and update metadata.

**Zod Schema**:
```typescript
const DeleteDesignSchema = z.object({
  filename: z.string().describe("Name of the design file to delete"),
  workspace_path: z.string().optional().describe("Workspace path (defaults to current directory)")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_delete",
    "arguments": {
      "filename": "old_design_1.html"
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "Successfully deleted old_design_1.html"
    }
  ]
}
```

**Safety Features**:
- Verifies file exists before deletion
- Updates metadata.json atomically
- Provides clear error messages for failures

**Common Errors**:
- `File not found`: Specified filename doesn't exist
- `Permission denied`: Insufficient permissions to delete file

---

### superdesign_cleanup

**Purpose**: Clean up old design files based on age and count limits.

**Zod Schema**:
```typescript
const CleanupSchema = z.object({
  workspace_path: z.string().optional().describe("Workspace path (defaults to current directory)"),
  max_age_days: z.number().optional().describe("Delete designs older than X days (default: 30)"),
  max_count: z.number().optional().describe("Keep only the latest X designs (default: 50)"),
  dry_run: z.boolean().optional().describe("Show what would be deleted without actually deleting")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_cleanup",
    "arguments": {
      "max_age_days": 14,
      "max_count": 25,
      "dry_run": true
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "CLEANUP RESULTS:\n\nDRY RUN - No files were actually deleted\n\nFiles to be deleted (3):\n  - old_design_1.html\n  - old_design_2.html\n  - old_design_3.html\n\nFiles kept (22):\n  - recent_design_1.html\n  - recent_design_2.html\n  [... 20 more files]\n\nCleanup settings:\n  - Max age: 14 days\n  - Max count: 25 files\n  - Enabled: true"
    }
  ]
}
```

**Cline Usage Examples**:
- "Clean up old design files older than 14 days"
- "Keep only the latest 20 designs"
- "Show me what would be deleted in a dry run"

**Cleanup Logic**:
1. **Age-based**: Delete files older than `max_age_days`
2. **Count-based**: Keep only the latest `max_count` files
3. **Priority**: Newest files are always kept first

**Settings Persistence**:
- Settings saved in `superdesign/cleanup-settings.json`
- Default: 30 days max age, 50 files max count
- Customizable per workspace

---

## Gallery Tools

### superdesign_gallery

**Purpose**: Generate a static HTML gallery to view all designs in a browser.

**Zod Schema**:
```typescript
const GallerySchema = z.object({
  workspace_path: z.string().optional().describe("Workspace path (defaults to current directory)")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_gallery",
    "arguments": {
      "workspace_path": "/path/to/project"
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "GALLERY GENERATION SPECIFICATION FOR CLINE:\n\nIMPORTANT: You must create a gallery HTML file to view all designs in a browser.\n\n=== GALLERY PARAMETERS ===\n- Gallery file: /path/to/project/superdesign/gallery.html\n- Design files found: 8\n- Directory: /path/to/project/superdesign/design_iterations\n\n=== FILES TO DISPLAY ===\n1. calculator_glassmorphism_3.html\n2. calculator_minimalist_1.html\n[... 6 more files]\n\n=== GALLERY HTML CONTENT ===\n[Complete HTML gallery code with embedded previews]\n\n=== EXECUTION INSTRUCTIONS ===\n1. Create the gallery file at specified path\n2. Write the gallery HTML content to the file\n3. AUTOMATICALLY OPEN the gallery in the default browser\n4. Gallery features: smart refresh, auto-refresh, dark mode, metadata display"
    }
  ]
}
```

**Gallery Features**:
- **Visual previews** - Thumbnail previews in responsive grid
- **Theme toggle** - Dark/light mode with persistent settings
- **Smart refresh** - Detects file changes and offers refresh
- **Auto-refresh** - Configurable automatic refresh (5-second intervals)
- **Metadata display** - File sizes, creation dates, design types
- **Interactive actions** - View full-screen, copy paths, delete files
- **Mobile responsive** - Works on desktop, tablet, and mobile

**Cline Usage Examples**:
- "Show me the gallery of all my designs"
- "Generate a gallery to view my work"
- "Create a visual overview of my design files"

---

### superdesign_live_gallery

**Purpose**: Start a live gallery server with real-time updates and file watching.

**Zod Schema**:
```typescript
const LiveGallerySchema = z.object({
  workspace_path: z.string().optional().describe("Workspace path (defaults to current directory)"),
  port: z.number().optional().describe("Port for the live gallery server (default: 3000)")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_live_gallery",
    "arguments": {
      "port": 3001
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "LIVE GALLERY SERVER STARTED:\n\n🌐 Server URL: http://localhost:3001\n📁 Workspace: /path/to/project/superdesign\n🔴 Live Updates: Enabled\n📡 Port: 3001\n\n=== FEATURES ===\n✨ Real-time file watching\n🔄 Auto-refresh on file changes\n🌙 Dark/Light mode toggle\n📱 Responsive design\n🗂️ File metadata display\n\n=== USAGE ===\n1. Open http://localhost:3001 in your browser\n2. The gallery will automatically update when design files change\n3. Use the theme toggle to switch between light and dark modes\n4. Click \"View Full\" to open designs in new tabs\n5. Use \"Copy Path\" to copy file paths to clipboard\n\n=== LIVE UPDATES ===\nThe gallery will automatically detect:\n• New design files added to design_iterations/\n• Modified design files (with visual highlights)\n• Deleted design files (with smooth removal)\n\nServer is now running at: http://localhost:3001"
    }
  ]
}
```

**Live Features**:
- **Real-time file watching** - Server-Sent Events for efficient communication
- **Auto-refresh** - Gallery updates without manual intervention
- **Connection management** - Auto-reconnects if connection is lost
- **Multi-client support** - Multiple browsers can connect simultaneously
- **Visual feedback** - Toast notifications for all file operations
- **File change notifications** - Real-time alerts for added/modified/deleted files

**Technical Implementation**:
- **HTTP Server** - Serves gallery and design files
- **Server-Sent Events** - `/events` endpoint for real-time updates
- **File Watching** - Native file system events with polling fallback
- **CORS Support** - Cross-origin requests enabled

**Cline Usage Examples**:
- "Start a live gallery server to monitor my designs"
- "Launch the live gallery on port 3001"
- "Start real-time design monitoring"

---

## System Extraction Tools

### superdesign_extract_system

**Purpose**: Extract design systems from screenshots or images for reusable style foundations.

**Zod Schema**:
```typescript
const ExtractDesignSystemSchema = z.object({
  image_path: z.string().describe("Path to screenshot/image to extract design system from")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_extract_system",
    "arguments": {
      "image_path": "/path/to/screenshot.png"
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "DESIGN SYSTEM EXTRACTION SPECIFICATION FOR CLINE:\n\nIMPORTANT: You must analyze the image and extract a design system JSON file.\n\n=== EXTRACTION PARAMETERS ===\n- Image path: /path/to/screenshot.png\n- Output location: superdesign/design_system/design-system.json\n\n=== EXTRACTION GUIDELINES ===\nAnalyze the screenshot/image and extract:\n1. Color palette (primary, secondary, neutrals)\n2. Typography rules (font families, sizes, weights, line heights)\n3. Spacing system (margin/padding values)\n4. Layout structure (grid system, containers)\n5. Component styles (buttons, inputs, cards, etc.)\n6. Visual effects (shadows, borders, radius values)\n\n=== OUTPUT FORMAT ===\nCreate a JSON file with this structure:\n{\n  \"colors\": {\n    \"primary\": {...},\n    \"secondary\": {...},\n    \"neutrals\": {...}\n  },\n  \"typography\": {\n    \"fontFamilies\": {...},\n    \"sizes\": {...},\n    \"weights\": {...}\n  },\n  \"spacing\": {...},\n  \"components\": {\n    \"buttons\": {...},\n    \"inputs\": {...},\n    ...\n  },\n  \"effects\": {...}\n}\n\n=== EXECUTION INSTRUCTIONS ===\n1. Analyze the image at specified path\n2. Extract ONLY design patterns, not content\n3. Create a reusable design system\n4. Save as design-system.json (or design-system_2.json if exists)"
    }
  ]
}
```

**Extraction Focus Areas**:
- **Color Palette** - Primary, secondary, and neutral colors
- **Typography** - Font families, sizes, weights, line heights
- **Spacing System** - Margin/padding values and grid systems
- **Layout Structure** - Containers, grids, and responsive patterns
- **Component Styles** - Buttons, inputs, cards, navigation elements
- **Visual Effects** - Shadows, borders, border radius, gradients

**Output Structure**:
```json
{
  "colors": {
    "primary": "#007bff",
    "secondary": "#6c757d",
    "neutrals": {
      "white": "#ffffff",
      "gray-100": "#f8f9fa",
      "gray-900": "#212529"
    }
  },
  "typography": {
    "fontFamilies": {
      "primary": "Inter, sans-serif",
      "mono": "Monaco, monospace"
    },
    "sizes": {
      "xs": "12px",
      "sm": "14px",
      "base": "16px",
      "lg": "18px",
      "xl": "20px"
    }
  },
  "spacing": {
    "xs": "4px",
    "sm": "8px",
    "md": "16px",
    "lg": "24px",
    "xl": "32px"
  },
  "components": {
    "buttons": {
      "primary": {
        "backgroundColor": "#007bff",
        "color": "#ffffff",
        "borderRadius": "6px",
        "padding": "8px 16px"
      }
    }
  }
}
```

**Cline Usage Examples**:
- "Extract the design system from this screenshot"
- "Analyze this UI image and create a reusable design system"
- "Generate design tokens from this interface"

**Constraints**:
- Extracts design patterns only, not specific content
- Focuses on reusable style foundations
- Creates generalized rules for consistent UI development

---

## Utility Tools

### superdesign_check_files

**Purpose**: Check for file changes by comparing current files with a manifest (used for gallery refresh integration).

**Zod Schema**:
```typescript
const CheckFilesSchema = z.object({
  workspace_path: z.string().optional().describe("Workspace path (defaults to current directory)"),
  manifest: z.array(z.object({
    name: z.string(),
    size: z.number(),
    modified: z.number()
  })).describe("File manifest to compare against")
});
```

**Request Example**:
```json
{
  "method": "tools/call",
  "params": {
    "name": "superdesign_check_files",
    "arguments": {
      "manifest": [
        {
          "name": "design_1.html",
          "size": 5420,
          "modified": 1704067200000
        },
        {
          "name": "design_2.html",
          "size": 3890,
          "modified": 1704070800000
        }
      ]
    }
  }
}
```

**Response Example**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "{\"hasChanges\":true,\"changes\":[{\"file\":\"design_1.html\",\"type\":\"modified\"},{\"file\":\"design_3.html\",\"type\":\"added\"}]}"
    }
  ]
}
```

**Change Types**:
- **added** - New file detected
- **modified** - Existing file changed (size or timestamp)
- **deleted** - File no longer exists

**Use Cases**:
- Gallery refresh integration
- File synchronization checks
- Change detection for automated workflows

**Tolerance Settings**:
- Smart comparison with configurable tolerance
- Handles file system timestamp variations
- Efficient change detection algorithms

---

## Workflow Examples

### Basic Design Generation Workflow

1. **Generate Initial Designs**:
```json
{
  "name": "superdesign_generate",
  "arguments": {
    "prompt": "e-commerce product page",
    "design_type": "ui",
    "variations": 3,
    "framework": "html"
  }
}
```

2. **View Results in Gallery**:
```json
{
  "name": "superdesign_gallery",
  "arguments": {}
}
```

3. **Iterate on Best Design**:
```json
{
  "name": "superdesign_iterate",
  "arguments": {
    "design_file": "superdesign/design_iterations/e_commerce_product_2.html",
    "feedback": "Add more prominent call-to-action buttons and improve mobile layout",
    "variations": 2
  }
}
```

### Design System Extraction Workflow

1. **Extract Design System**:
```json
{
  "name": "superdesign_extract_system",
  "arguments": {
    "image_path": "/path/to/reference-design.png"
  }
}
```

2. **Generate Components Using System**:
```json
{
  "name": "superdesign_generate",
  "arguments": {
    "prompt": "button component following the extracted design system",
    "design_type": "component",
    "variations": 3,
    "framework": "react"
  }
}
```

### Live Development Workflow

1. **Start Live Gallery**:
```json
{
  "name": "superdesign_live_gallery",
  "arguments": {
    "port": 3000
  }
}
```

2. **Generate Designs** (gallery updates automatically):
```json
{
  "name": "superdesign_generate",
  "arguments": {
    "prompt": "dashboard widgets",
    "design_type": "component",
    "variations": 4
  }
}
```

3. **Iterate Based on Live Preview**:
```json
{
  "name": "superdesign_iterate",
  "arguments": {
    "design_file": "superdesign/design_iterations/dashboard_widgets_3.html",
    "feedback": "Make the charts more interactive and add hover effects"
  }
}
```

---

## Error Handling

### Common Error Types

#### Validation Errors
```json
{
  "content": [
    {
      "type": "text",
      "text": "Error parsing arguments: Invalid design_type. Expected one of: ui, wireframe, component, logo, icon"
    }
  ]
}
```

#### File System Errors
```json
{
  "content": [
    {
      "type": "text",
      "text": "Error: Design file dashboard_1.html does not exist"
    }
  ]
}
```

#### Server Errors
```json
{
  "content": [
    {
      "type": "text",
      "text": "Error starting live gallery server: Port 3000 is already in use"
    }
  ]
}
```

### Error Prevention

1. **Input Validation**: All parameters are validated using Zod schemas
2. **File Existence Checks**: Files are verified before operations
3. **Permission Checks**: File system permissions are validated
4. **Port Availability**: Server ports are checked before binding
5. **Graceful Degradation**: Fallback behaviors for non-critical failures

---

## Troubleshooting

### Tool Not Available
**Problem**: Superdesign tools don't appear in Cline
**Solution**: 
1. Verify MCP server configuration
2. Restart Cline completely
3. Check server registration in logs

### File Permission Errors
**Problem**: Cannot create or delete files
**Solution**:
```bash
# Add execute permissions
chmod +x dist/index.js

# Check directory permissions
ls -la superdesign/
```

### Gallery Not Loading
**Problem**: Gallery shows empty or doesn't load designs
**Solution**:
1. Verify design files exist in `superdesign/design_iterations/`
2. Check browser console for JavaScript errors
3. Ensure file paths are correct

### Live Gallery Connection Issues
**Problem**: Live gallery shows "Disconnected" status
**Solution**:
1. Check if port is available
2. Verify firewall settings
3. Try different port number
4. Check server logs for errors

### Performance Issues
**Problem**: Gallery loads slowly with many designs
**Solution**:
1. Use cleanup tool to remove old designs
2. Enable auto-refresh instead of manual refresh
3. Consider using live gallery for better performance

---

## Best Practices

### Design Generation
- Use descriptive prompts for better results
- Start with 2-3 variations, then iterate
- Choose appropriate framework for your project
- Use wireframes for initial concepts, UI for final designs

### File Management
- Regular cleanup to maintain performance
- Use meaningful filenames in prompts
- Organize designs by project or feature
- Keep design systems separate from iterations

### Gallery Usage
- Use live gallery during active development
- Static gallery for sharing or archiving
- Enable auto-refresh for real-time feedback
- Use dark mode for better design visibility

### Workflow Optimization
- Extract design systems early in projects
- Iterate incrementally rather than major changes
- Use component type for reusable elements
- Combine tools for complete design workflows

This API documentation provides comprehensive coverage of all Superdesign MCP Server tools, enabling effective integration and usage with Cline for sophisticated design generation and management workflows.
