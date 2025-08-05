# Superdesign MCP Server for Cline

An MCP (Model Context Protocol) server that brings [Superdesign](https://github.com/superdesigndev/superdesign) - an open source AI design agent by [@jasonzhou1993](https://twitter.com/jasonzhou1993) and [@jackjack_eth](https://twitter.com/jackjack_eth) - to Cline as native tools. This server operates as a "design orchestrator" that provides structured specifications for Cline to execute, enabling Superdesign's sophisticated design capabilities without requiring separate API keys.

## Key Benefits

- **No API Keys Required**: Works directly with Cline's built-in LLM connection
- **Local Execution**: Runs entirely on your machine as an MCP server
- **Seamless Cline Integration**: Native tool integration with Cline's MCP support
- **Based on Open Source**: Built on top of [Superdesign.dev](https://www.superdesign.dev), an open source AI design system

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
         "args": ["C:\\Files\\Documents\\Cline\\superdesign-mcp-server\\dist\\index.js"],
         "env": {}
       }
     }
   }
   ```

3. **Alternative: Use the provided config file:**
   Copy the `mcp-config.json` file from this project to your Cline configuration directory

4. **Restart Cline**

3. The MCP server will provide these Superdesign orchestrator tools in Cline:
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
- `framework`: Framework for components (html, react, vue)

**Output**: Detailed specifications including Superdesign system prompt, file naming patterns, and design guidelines for Cline to execute.

### superdesign_iterate
Returns iteration instructions for Cline to improve existing designs:
- Reads existing design files  
- Provides structured feedback application guidelines
- Maintains design consistency through Superdesign principles

Parameters:
- `design_file`: Path to existing design file
- `feedback`: Improvement instructions
- `variations`: Number of variations to create

**Output**: Iteration specifications including original design content, feedback to apply, and Superdesign guidelines for Cline to execute.

### superdesign_extract_system
Returns instructions for Cline to extract design systems from screenshots:
- Provides analysis framework for visual design patterns
- Guides extraction of color palettes, typography, spacing
- Specifies JSON structure for reusable design systems

**Output**: Extraction specifications and JSON schema for Cline to analyze images and create design system files.

### superdesign_list
List all Superdesign creations in workspace:
- Shows design iterations
- Shows extracted design systems
- Displays file organization

### superdesign_gallery
Generate an interactive HTML gallery to view all designs:
- **Browser-based gallery** - opens in your default browser
- **Visual previews** - see design thumbnails in a responsive grid
- **Interactive features** - click to view full-screen, copy paths
- **Mobile responsive** - works on desktop, tablet, and mobile
- **Auto-discovery** - finds all HTML/SVG files in design_iterations/

Parameters:
- `workspace_path`: Workspace directory (optional, defaults to current directory)

**Output**: Gallery HTML file with embedded previews and JavaScript interactions. The gallery opens automatically in your browser, providing a Superdesign-like canvas experience.

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

**Requirements:**
- Cline with MCP support
- No API keys needed (uses Cline's existing LLM connection)

**File Organization:**
Designs are automatically saved to `superdesign/` directory (visible folder):
- `design_iterations/` - Generated designs (HTML/SVG files)
- `design_system/` - Extracted design systems (JSON files)

**Benefits:**
- No API key configuration required
- Uses Cline's existing LLM capabilities
- Maintains all of Superdesign's sophisticated design methodology
- Proper file organization and naming conventions
- Full design iteration workflow support

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

## License

This project follows the same license as the original Superdesign project. Please refer to the [Superdesign repository](https://www.superdesign.dev) for license details.
