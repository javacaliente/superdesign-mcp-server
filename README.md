# Superdesign MCP Server for Cline

An MCP (Model Context Protocol) server that brings [Superdesign](https://github.com/superdesigndev/superdesign) - an open source AI design agent by [@jasonzhou1993](https://twitter.com/jasonzhou1993) and [@jackjack_eth](https://twitter.com/jackjack_eth) - to Cline as native tools. This Cline version was originally from [superdesign-mcp-claude-code](https://github.com/jonthebeef/superdesign-mcp-claude-code). Currently the main difference was updating Claude Code references to Cline. And updateing the Read Me to include more details about the project. This server operates as a "design orchestrator" that provides structured specifications for Cline to execute, enabling Superdesign's sophisticated design capabilities without requiring separate API keys.

## Key Benefits

- **No API Keys Required**: Works directly with Cline's built-in LLM connection
- **Local Execution**: Runs entirely on your machine as an MCP server
- **Seamless Cline Integration**: Native tool integration with Cline's MCP support
- **Based on Open Source**: Built on top of [Superdesign.dev](https://www.superdesign.dev), an open source AI design system. And  [superdesign-mcp-claude-code](https://github.com/jonthebeef/superdesign-mcp-claude-code).

## Installation

1. Install dependencies:
```bash
npm install
```

2. Build the server:
```bash
npm run build
```

## Cline Integration

1. **Locate your Cline MCP configuration file:**
   - On Windows: `%APPDATA%\Cline\mcp_servers.json` or similar
   - The exact location may vary depending on your Cline installation

2. **Add the MCP server configuration:**
   Edit your Cline MCP configuration file to include:
   ```json
   {
     "mcpServers": {
       "superdesign": {
         "command": "node",
         "args": ["path/to/your/superdesign-mcp-server/dist/index.js"],
         "env": {}
       }
     }
   }
   ```

3. **Alternative: Use the provided template:**
   Copy `cline-mcp-config.template.json` to `cline-mcp-config.json` and update the path to match your installation

4. **Restart Cline**

The MCP server will provide these Superdesign orchestrator tools in Cline:
- `superdesign_generate` - Returns specifications for Cline to generate designs
- `superdesign_iterate` - Returns instructions for Cline to iterate on existing designs
- `superdesign_extract_system` - Returns instructions for design system extraction
- `superdesign_list` - Lists all created designs in the workspace
- `superdesign_gallery` - Generate interactive HTML gallery
- `superdesign_delete` - Delete specific design files
- `superdesign_cleanup` - Clean up old design files
- `superdesign_live_gallery` - Start live gallery server with real-time updates
- `superdesign_check_files` - Check for file changes (for gallery integration)

## Development

Run in development mode:
```bash
npm run dev
```

## Superdesign Tools Available

### superdesign_generate
Returns structured specifications for Cline to generate designs:
- **UI designs**: Complete responsive interfaces
- **Wireframes**: Minimal black and white layouts  
- **Components**: Individual UI components (HTML/React/Vue)
- **Logos**: SVG logo designs
- **Icons**: SVG icon designs

Parameters:
- `prompt`: Description of what to create
- `design_type`: Type of design (ui, wireframe, component, logo, icon)
- `variations`: Number of variations to generate (1-5, default 3)
- `framework`: Framework for components (html, react, vue, default: html)

**Framework Support Details:**
- **HTML**: Generates standalone HTML files with Tailwind CSS
- **React**: Creates React components with JSX syntax and proper component structure
- **Vue**: Generates Vue.js components with template, script, and style sections

**Output**: Detailed specifications including Superdesign system prompt, file naming patterns, and design guidelines for Cline to execute.

### superdesign_iterate
Returns iteration instructions for Cline to improve existing designs:
- Reads existing design files  
- Provides structured feedback application guidelines
- Maintains design consistency through Superdesign principles

Parameters:
- `design_file`: Path to existing design file
- `feedback`: Improvement instructions
- `variations`: Number of variations to create (1-5, default 3)

**Input Validation:**
- Verifies design file exists before processing
- Validates feedback is non-empty
- Ensures variations count is within limits

**Output**: Iteration specifications including original design content, feedback to apply, and Superdesign guidelines for Cline to execute.

### superdesign_extract_system
Returns instructions for Cline to extract design systems from screenshots:
- Provides analysis framework for visual design patterns
- Guides extraction of color palettes, typography, spacing
- Specifies JSON structure for reusable design systems

Parameters:
- `image_path`: Path to screenshot/image to extract design system from

**Input Validation:**
- Verifies image file exists at specified path
- Supports common image formats (PNG, JPG, SVG, etc.)

**Output**: Extraction specifications and JSON schema for Cline to analyze images and create design system files in `design_system/` directory.

### superdesign_list
List all Superdesign creations in workspace:
- Shows design iterations with file details
- Shows extracted design systems
- Displays file organization and metadata
- Provides file counts and directory structure

Parameters:
- `workspace_path`: Workspace directory (optional, defaults to current directory)

### superdesign_gallery
Generate an interactive HTML gallery to view all designs:
- **Browser-based gallery** - creates HTML file for browser viewing
- **Visual previews** - see design thumbnails in a responsive grid
- **Interactive features** - click to view full-screen, copy paths
- **Mobile responsive** - works on desktop, tablet, and mobile
- **Auto-discovery** - finds all HTML/SVG files in design_iterations/
- **Smart refresh** - detects file changes and offers refresh functionality
- **Theme toggle** - dark/light mode with persistent settings

Parameters:
- `workspace_path`: Workspace directory (optional, defaults to current directory)

**Gallery Features:**
- **Metadata display** - Shows file sizes, creation dates, and design types
- **Auto-refresh toggle** - Configurable automatic refresh (5-second intervals)
- **Visual indicators** - Highlights updated files with pulse animations
- **Toast notifications** - User feedback for all operations
- **File management** - Copy paths, delete files, view full-screen

**Output**: Gallery HTML file with embedded previews and JavaScript interactions. Use `open gallery.html` to view in your browser.

### superdesign_live_gallery
Start a live gallery server with real-time updates:
- **Real-time file watching** - Automatically detects file changes using Server-Sent Events
- **Live updates** - Gallery refreshes without manual intervention
- **Connection management** - Auto-reconnects if connection is lost
- **Visual feedback** - Toast notifications for all file operations
- **Interactive interface** - All gallery features plus live capabilities

Parameters:
- `workspace_path`: Workspace directory (optional, defaults to current directory)
- `port`: Port for the live gallery server (optional, default: 3000)

**Live Features:**
- **File change notifications** - Real-time alerts for added/modified/deleted files
- **Connection status indicator** - Shows live connection state
- **Automatic reconnection** - Handles network interruptions gracefully
- **Multi-client support** - Multiple browsers can connect simultaneously

**Output**: Starts HTTP server and returns URL (e.g., `http://localhost:3000`). Server runs until manually stopped.

### superdesign_delete
Delete a design file and update metadata:
- Removes file from design_iterations directory
- Updates metadata.json to remove file entry
- Provides confirmation of deletion

Parameters:
- `filename`: Name of the design file to delete
- `workspace_path`: Workspace directory (optional, defaults to current directory)

**Safety Features:**
- Verifies file exists before deletion
- Updates metadata atomically
- Provides clear error messages for failures

### superdesign_cleanup
Clean up old design files based on age and count limits:
- **Age-based cleanup** - Delete files older than specified days
- **Count-based cleanup** - Keep only the latest N files
- **Dry-run capability** - Preview what would be deleted without actually deleting
- **Detailed reporting** - Shows what was deleted, kept, and any errors

Parameters:
- `workspace_path`: Workspace directory (optional, defaults to current directory)
- `max_age_days`: Delete designs older than X days (optional, default: 30)
- `max_count`: Keep only the latest X designs (optional, default: 50)
- `dry_run`: Show what would be deleted without actually deleting (optional, default: false)

**Cleanup Settings:**
- Settings are persisted in `superdesign/cleanup-settings.json`
- Default settings: 30 days max age, 50 files max count
- Settings can be customized per workspace

### superdesign_check_files
Check for file changes by comparing current files with a manifest:
- **Manifest-based comparison** - Compares current files against provided manifest
- **Change detection** - Identifies added, modified, and deleted files
- **Integration support** - Used by gallery refresh functionality
- **Tolerance settings** - Smart comparison with configurable tolerance

Parameters:
- `workspace_path`: Workspace directory (optional, defaults to current directory)
- `manifest`: Array of file objects with name, size, and modified timestamp

**Output**: JSON object with `hasChanges` boolean and `changes` array detailing specific changes.

## Advanced Features

### Live Gallery Server
- **Real-time monitoring** - Automatically detects file changes in design_iterations/
- **Server-Sent Events** - Uses SSE for efficient real-time communication
- **Connection resilience** - Auto-reconnects if connection is lost
- **Multi-client support** - Multiple browsers can connect simultaneously
- **File watching** - Native file system events for efficient monitoring

### Smart Gallery System
- **Theme persistence** - Dark/light mode preference saved in localStorage
- **Auto-refresh** - Configurable automatic refresh intervals (default: 5 seconds)
- **Change detection** - Visual indicators for updated files with pulse animations
- **Metadata display** - File sizes, creation dates, and design types
- **Smart refresh** - Detects changes and offers manual or automatic refresh

### Metadata Management
- **Automatic tracking** - All designs automatically tracked in metadata.json
- **File statistics** - Size, creation date, design type, prompt, framework
- **Persistent storage** - Metadata survives file operations and cleanup
- **Design history** - Track design evolution and iterations

### File Management
- **Automatic cleanup** - Age and count-based file management
- **Safe operations** - Dry-run mode for testing cleanup operations
- **Directory structure** - Automatic creation of required directories
- **Error handling** - Robust error handling with detailed error messages

## Configuration

### Cleanup Settings
Default settings (customizable per workspace):
- **Max age**: 30 days (files older than this are eligible for deletion)
- **Max count**: 50 files (keep only the latest N files)
- **Settings file**: `superdesign/cleanup-settings.json`

Example cleanup-settings.json:
```json
{
  "maxAgeDays": 30,
  "maxCount": 50,
  "enabled": true
}
```

### Gallery Settings
- **Live server default port**: 3000 (configurable)
- **Auto-refresh interval**: 5 seconds (configurable in gallery UI)
- **Theme preference**: Saved in browser localStorage
- **File change tolerance**: 1 second (for detecting modifications)

## How the Orchestrator Works

This MCP server operates as a **design orchestrator** rather than a direct generator:

1. **User Request**: "Generate a modern dashboard UI"
2. **MCP Server**: Returns detailed specifications with:
   - Complete Superdesign system prompt and guidelines
   - Exact file paths and naming conventions
   - Design type-specific instructions
   - Number of variations to create
3. **Cline**: Receives specifications and:
   - Generates the actual HTML/SVG/React code
   - Saves files to specified locations
   - Follows all Superdesign design principles

## Usage in Cline

Once configured, you can use Superdesign through Cline with natural language:

**Example Usage:**
- "Generate a modern dashboard UI design"
- "Create 3 variations of a login page wireframe"  
- "Design a React component for a product card"
- "Make a minimalist logo for a tech startup"
- "Iterate on the dashboard design with better spacing"
- "Show me the gallery of all my designs"
- "Start a live gallery server to monitor my designs"
- "Clean up old design files older than 14 days"

**Requirements:**
- Cline with MCP support
- No API keys needed (uses Cline's existing LLM connection)

**File Organization:**
Designs are automatically saved to `superdesign/` directory (visible folder):
- `design_iterations/` - Generated designs (HTML/SVG files)
- `design_system/` - Extracted design systems (JSON files)
- `metadata.json` - Design file metadata and statistics
- `cleanup-settings.json` - Cleanup configuration
- `gallery.html` - Generated gallery file

**Benefits:**
- No API key configuration required
- Uses Cline's existing LLM capabilities
- Maintains all of Superdesign's sophisticated design methodology
- Proper file organization and naming conventions
- Full design iteration workflow support
- Real-time gallery updates and file monitoring

## Known Issues & Troubleshooting

### File Permissions Error
If you encounter permission errors when running the MCP server:
```bash
# Add execute permissions to the built file
chmod +x dist/index.js
```

### MCP Tools Not Appearing
If Superdesign tools don't appear in Cline after installation:
1. Ensure you've completely quit Cline (not just closed the window)
2. Restart Cline
3. Check the tools are available by asking Cline: "What tools do you have available?"

### Server Registration Issues
If the server fails to register, verify your MCP configuration file is correct and restart Cline.

### Live Gallery Server Issues
If the live gallery server fails to start:
1. Check if port 3000 is already in use
2. Try specifying a different port: `superdesign_live_gallery` with `"port": 3001`
3. Ensure no firewall is blocking the connection

### Gallery Not Updating
If the gallery doesn't show file changes:
1. Try manual refresh using the refresh button
2. Enable auto-refresh toggle in the gallery
3. Check browser console for JavaScript errors
4. Verify files are being saved to the correct `design_iterations/` directory

### Build Errors
Ensure you have Node.js 16+ installed:
```bash
node --version  # Should be v16.0.0 or higher
```

## 🤝 Relationship to Superdesign

This MCP server provides a complementary integration for [Superdesign](https://github.com/superdesigndev/superdesign) by [@jasonzhou1993](https://twitter.com/jasonzhou1993) and [@jackjack_eth](https://twitter.com/jackjack_eth).

While Superdesign offers an IDE extension that works across multiple editors, this MCP server specifically enhances Cline by:
- **Eliminating the need for API keys** - uses Cline's built-in LLM connection
- **Providing native tool integration** - no manual prompt copying required
- **Enabling direct tool calls** - seamless workflow without copy/paste

### How it's Different

| Aspect | Superdesign Extension | This MCP Server |
|--------|----------------------|-----------------|
| **Integration Type** | IDE Extension | Native MCP Tools |
| **Cline Access** | Manual prompt copying | Direct tool invocation |
| **API Requirements** | Separate API key needed | Uses Cline's existing connection |
| **User Experience** | Copy/paste workflow | Automated orchestration |
| **Real-time Updates** | Manual refresh | Live gallery with auto-updates |
| **File Management** | Manual organization | Automatic cleanup and metadata |

## License

This project follows the same license as the original Superdesign project. Please refer to the [Superdesign repository](https://www.superdesign.dev) for license details.
