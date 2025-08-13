# Superdesign MCP Server for Ai Assistants

This is an Model Context Protocol(MCP) server that integrates [Superdesign](https://github.com/superdesigndev/superdesign) - an open source AI design agent by [@jasonzhou1993](https://twitter.com/jasonzhou1993) and [@jackjack_eth](https://twitter.com/jackjack_eth) - with your Code Editor or IDE as a set of native tools in your Ai Assistant. This version was originally from [superdesign-mcp-claude-code](https://github.com/jonthebeef/superdesign-mcp-claude-code). Currently the main difference was updating Claude Code references to appear more native. And updating the Read Me to include more details about the project.

This MCP Server operates as a "design orchestrator" that provides structured specifications for the LLM to execute, enabling Superdesign's sophisticated User Interface design capabilities without requiring separate API keys.

## Key Benefits

- **No API Keys Required**: Works directly with the Ai Assistant's built-in LLM connection
- **Local Execution**: Runs entirely on your machine as an MCP server
- **Seamless Integration**: Native tool integration with MCP support
- **Based on Open Source**: Built on top of [Superdesign.dev](https://www.superdesign.dev), an open source AI design system. And [superdesign-mcp-claude-code](https://github.com/jonthebeef/superdesign-mcp-claude-code) who converted it to an MCP.

## Installation

1. Install dependencies:
```bash
npm install
```

2. Build the server:
```bash
npm run build
```

## MCP Server Integration

1. **Locate your MCP configuration file:**
   - On Windows: `%APPDATA%\Cline\mcp_servers.json` or similar
   - The exact location may vary depending on your Cline installation

2. **Add the MCP server configuration:**
   Edit your MCP configuration file to include:
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
   Copy `mcp-config.template.json` to your Ai Assistant's MCP configuration file and update the path to match your installation

4. **Restart your Ai Assistant**

The MCP server will provide these Superdesign orchestrator tools in your Ai Assistant:
- `superdesign_generate` - Returns specifications for the Ai Assistant to generate designs
- `superdesign_iterate` - Returns instructions for the Ai Assistant to iterate on existing designs
- `superdesign_extract_system` - Returns instructions for the Ai Assistant to extract design systems
- `superdesign_list` - Lists all created designs in the workspace
- `superdesign_gallery` - Generate interactive HTML gallery
- `superdesign_delete` - Delete specific design files
- `superdesign_cleanup` - Clean up old design files
- `superdesign_live_gallery` - Start live gallery server with real-time updates
- `superdesign_stop_live_gallery` - Stops a running live gallery server
- `superdesign_check_files` - Check for file changes (for gallery integration)

## Development

Run in development mode:
```bash
npm run dev
```

## Superdesign Tools Available

### superdesign_generate
Returns structured specifications for the Ai Assistant to generate designs:
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
- **HTML**: Generates HTML5 files with Tailwind CSS
- **React**: Creates React components with JSX syntax and proper component structure
- **Vue**: Generates Vue.js components with template, script, and style sections

**Output**: Detailed specifications including Superdesign system prompt, file naming patterns, and design guidelines for the Ai Assistant to execute.

### superdesign_iterate
Returns iteration instructions for the Ai Assistant to improve existing designs:
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

**Output**: Iteration specifications including original design content, feedback to apply, and Superdesign guidelines for the Ai Assistant to execute.

### superdesign_extract_system
Returns instructions for the Ai Assistant to extract design systems from screenshots:
- Provides analysis framework for visual design patterns
- Guides extraction of color palettes, typography, spacing
- Specifies JSON structure for reusable design systems

Parameters:
- `image_path`: Path to screenshot/image to extract design system from

**Input Validation:**
- Verifies image file exists at specified path
- Supports common image formats (PNG, JPG, SVG, etc.)

**Output**: Extraction specifications and JSON schema for the Ai Assistant to analyze images and create design system files in `design_system/` directory.

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

**Output**: Starts HTTP server and returns URL (e.g., `http://localhost:3000`). The server runs until stopped with the `superdesign_stop_live_gallery` tool or manually.

### superdesign_stop_live_gallery
Stops a running live gallery server.

Parameters:
- `port`: The port number of the server to stop (e.g., 3000)

**Output**: Confirmation that the server on the specified port has been stopped.

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


## How the Orchestrator Works

This MCP server operates as a **design orchestrator** rather than a direct generator:

1. **User Request**: "Generate a modern dashboard UI"
2. **MCP Server**: Returns detailed specifications with:
   - Complete Superdesign system prompt and guidelines
   - Exact file paths and naming conventions
   - Design type-specific instructions
   - Number of variations to create
3. **Ai Assistant**: Receives specifications and:
   - Generates the actual HTML/SVG/React code
   - Saves files to specified locations
   - Follows all Superdesign design principles

### How the Ai Agent Thinks: The Role of the System Prompt

This MCP server doesn't just generate code; it uses a sophisticated system prompt to guide the Ai Agent's "thinking" process. This prompt acts as a comprehensive rulebook and style guide, ensuring every design is consistent and high-quality. Here’s how it works:

**1. It Defines the Agent's Persona**
The prompt begins with the command: `You are a **senior front-end designer**.` This is a powerful technique called "role-playing."
- It instructs the AI to access the part of its knowledge base related to expert-level front-end design.
- The word "**senior**" sets a high standard, encouraging best practices, attention to detail, and robust, well-crafted code.
- The `**` (bold markdown) adds emphasis, signaling to the AI that this is a critical, core instruction.

A **Front-End Designer** is a specialist who bridges the gap between visual design and front-end development. This persona instructs the agent to be an expert in both designing the UI and implementing it directly with code (HTML, CSS, and framework-specific syntax).

**2. It Guides the Agent's Process**
Phrases like `"think deeply of the design style first, and then implement UI bit by bit"` are not just suggestions. They are cognitive forcing functions that:
- **Prime the AI for complexity** and a more considered approach.
- **Enforce a sequence**, ensuring style is considered *before* implementation begins.
- **Encourage a step-by-step workflow**, which leads to more structured and reliable output.

**3. It Specifies the Technology Stack**
The prompt hard-codes the agent's primary technology stack to ensure consistency:
- **Structure**: Plain HTML
- **Styling**: Tailwind CSS (delivered via CDN for portability)
- **Frameworks**: While HTML is the default, the agent can be instructed to generate components for **React** (using JSX) and **Vue** when the `design_type` is `component`.

By understanding these principles, you can better predict the agent's output and even modify the system prompt in `src/index.ts` to customize its behavior.

## Usage in your Ai Assistant

Once configured, you can use Superdesign through your Ai Assistant with natural language:

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
- An Ai Assistant with MCP support
- No API keys needed (uses the Ai Assistant's existing LLM connection)

**File Organization:**
Designs are automatically saved to `superdesign/` directory (visible folder):
- `design_iterations/` - Generated designs (HTML/SVG files)
- `design_system/` - Extracted design systems (JSON files)
- `metadata.json` - Design file metadata and statistics
- `cleanup-settings.json` - Cleanup configuration
- `gallery.html` - Generated gallery file

**Benefits:**
- No API key configuration required
- Uses the Ai Assistant's existing LLM capabilities
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
If Superdesign tools don't appear in your Ai Assistant after installation:
1. Ensure you've completely quit your Ai Assistant (not just closed the window)
2. Restart your Ai Assistant
3. Check the tools are available by asking your Ai Assistant: "What tools do you have available?"

### Server Registration Issues
If the server fails to register, verify your MCP configuration file is correct and restart your Ai Assistant.

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

While Superdesign offers an IDE extension that works across multiple editors, this MCP server specifically enhances Ai Assistants by:
- **Eliminating the need for API keys** - uses the Ai Assistant's built-in LLM connection
- **Providing native tool integration** - no manual prompt copying required
- **Enabling direct tool calls** - seamless workflow without copy/paste

### How it's Different

| Aspect | Superdesign Extension | This MCP Server |
|--------|----------------------|-----------------|
| **Integration Type** | IDE Extension | Native MCP Tools |
| **Ai Assistant Access** | Manual prompt copying | Direct tool invocation |
| **API Requirements** | Separate API key needed | Uses the Ai Assistant's existing connection |
| **User Experience** | Copy/paste workflow | Automated orchestration |
| **Real-time Updates** | Manual refresh | Live gallery with auto-updates |
| **File Management** | Manual organization | Automatic cleanup and metadata |

## License

This project follows the same license as the original Superdesign project. Please refer to the [Superdesign repository](https://www.superdesign.dev) for license details.
