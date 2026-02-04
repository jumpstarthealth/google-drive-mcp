# Google Drive MCP Server (Jumpstart Health Fork)

> **Fork Notice**: This is the [Jumpstart Health](https://github.com/jumpstarthealth) fork of [piotr-agier/google-drive-mcp](https://github.com/piotr-agier/google-drive-mcp). This fork adds **Shared Drive scoping** via the `driveId` parameter, enabling search and listing operations to be constrained to specific Shared Drives.

A Model Context Protocol (MCP) server that provides secure integration with Google Drive, Docs, Sheets, and Slides. It allows Claude Desktop and other MCP clients to manage files in Google Drive through a standardized interface.

## Fork Enhancements

### Shared Drive Scoping with `driveId`

This fork adds the `driveId` parameter to `search` and `listFolder` operations, allowing you to scope results to a specific Shared Drive. This is essential for organizations with multiple Shared Drives where you need to search within a specific drive rather than across all accessible drives.

**Why this matters:**
- Without `driveId`: Search returns files from My Drive AND all Shared Drives you have access to
- With `driveId`: Search returns ONLY files from the specified Shared Drive

**Finding your Shared Drive ID:**
1. Open the Shared Drive in Google Drive web interface
2. The URL will be: `https://drive.google.com/drive/u/0/folders/DRIVE_ID`
3. Copy the `DRIVE_ID` portion

**Example usage:**
```
Search for "Q4 Report" in our company Shared Drive only
(using driveId: 0AAFO-VRdS316Uk9PVA)
```

## Features

- **Multi-format Support**: Work with Google Docs, Sheets, Slides, and regular files
- **File Management**: Create, update, delete, rename, and move files and folders
- **Advanced Search**: Search across your entire Google Drive
- **Shared Drives Support**: Full access to Google Shared Drives (formerly Team Drives) in addition to My Drive
- **Shared Drive Scoping**: Scope search/list operations to specific Shared Drives via `driveId` parameter *(Fork enhancement)*
- **Folder Navigation**: List and navigate through folder hierarchies with path support (e.g., `/Work/Projects`)
- **MCP Resource Protocol**: Files accessible as MCP resources for reading content
- **Secure Authentication**: OAuth 2.0 with automatic token refresh

## Example Usage

This MCP server enables powerful file management workflows through natural language:

### 1. **Document Creation and Organization**
```
Create a new Google Doc called "Project Plan" in the folder /Work/Projects
with an outline for our Q1 initiatives including milestones and deliverables.
```

### 2. **File Search and Organization**
```
Search for files containing "budget" and organize them by moving each one
to the appropriate folder in your Drive hierarchy.
```

### 3. **Scoped Shared Drive Search** *(Fork enhancement)*
```
Search for "portfolio" in the JSF Shared Drive (driveId: 0AAFO-VRdS316Uk9PVA)
```

### 4. **Spreadsheet Creation**
```
Create a Google Sheet called "Sales Analysis 2024" with columns for Date, Product,
Quantity, and Revenue to track your sales data.
```

### 5. **Presentation Creation**
```
Create a presentation called "Product Roadmap" with slides outlining
our Q1 milestones, key features, and timeline.
```

### 6. **Spreadsheet Updates**
```
Update the "Team Contacts" spreadsheet with new employee information
by modifying specific cells or ranges with the provided data.
```

### 7. **Document Search**
```
Search for documents in the /Reports folder and create a summary
document listing the files you found.
```

### 8. **Folder and Document Creation**
```
Create a Templates folder and add standard documents like
a Meeting Notes template, Project Proposal template,
and Budget Spreadsheet template.
```

## Requirements

- **Node.js**: Version 18 or higher (LTS recommended)
- **Google Cloud Project**: With the following APIs enabled:
  - Google Drive API
  - Google Docs API
  - Google Sheets API
  - Google Slides API
- **OAuth 2.0 Credentials**: Desktop application type (Client ID only - no client secret required)

## Installation

### Option 1: Clone from Jumpstart Health Fork (Recommended for Shared Drive scoping)

```bash
git clone https://github.com/jumpstarthealth/google-drive-mcp.git
cd google-drive-mcp
npm install
npm run build
```

### Option 2: Use upstream with npx (Does NOT include driveId feature)

```bash
# Note: npm package does not yet include driveId parameter
npx @piotr-agier/google-drive-mcp
```

### Set up credentials

```bash
# Copy the example file
cp gcp-oauth.keys.example.json gcp-oauth.keys.json

# Edit gcp-oauth.keys.json with your OAuth client ID
```

### Authenticate

```bash
npm run auth
```

Note: Authentication happens automatically on first run of an MCP client if you skip this step.

## Usage with Claude Desktop

Add the server to your Claude Desktop configuration:

**macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
**Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

### Using Jumpstart Health Fork (with driveId support):
```json
{
  "mcpServers": {
    "google-drive": {
      "command": "/opt/homebrew/bin/node",
      "args": ["/path/to/google-drive-mcp/dist/index.js"],
      "env": {
        "GOOGLE_DRIVE_OAUTH_CREDENTIALS": "/path/to/your/gcp-oauth.keys.json"
      }
    }
  }
}
```

**Important Notes:**
- Use the full path to `node` (e.g., `/opt/homebrew/bin/node` on macOS with Homebrew)
- Replace `/path/to/google-drive-mcp/` with your actual clone location
- Replace `/path/to/your/gcp-oauth.keys.json` with your OAuth credentials path

## Available Tools

### Search and Navigation

- **search** - Search for files across Google Drive
  - `query`: Search terms (required)
  - `pageSize`: Number of results per page (optional, default 50, max 100)
  - `pageToken`: Pagination token for next page (optional)
  - `driveId`: Shared Drive ID to scope search to a specific Shared Drive (optional) *(Fork enhancement)*

- **listFolder** - List contents of a folder
  - `folderId`: Folder ID (optional, defaults to root)
  - `pageSize`: Number of results (optional, max 100)
  - `pageToken`: Pagination token (optional)
  - `driveId`: Shared Drive ID to scope listing to a specific Shared Drive (optional) *(Fork enhancement)*

### File Management
- **createTextFile** - Create a text or markdown file
  - `name`: File name (must end with .txt or .md)
  - `content`: File content
  - `parentFolderId`: Parent folder ID (optional)

- **updateTextFile** - Update existing text file
  - `fileId`: File ID to update
  - `content`: New content
  - `name`: New name (optional)

- **deleteItem** - Move a file or folder to trash (not a permanent deletion - items can be restored from Google Drive trash)
  - `itemId`: Item ID to move to trash

- **renameItem** - Rename a file or folder
  - `itemId`: Item ID to rename
  - `newName`: New name

- **moveItem** - Move a file or folder
  - `itemId`: Item ID to move
  - `destinationFolderId`: Destination folder ID

### Folder Operations
- **createFolder** - Create a new folder
  - `name`: Folder name
  - `parent`: Parent folder ID or path (optional)

### Google Workspace
- **createGoogleDoc** - Create a Google Doc
- **updateGoogleDoc** - Update a Google Doc
- **getGoogleDocContent** - Get document content with text indices
- **formatGoogleDocText** - Apply text formatting to a range
- **formatGoogleDocParagraph** - Apply paragraph formatting to a range
- **createGoogleSheet** - Create a Google Sheet
- **updateGoogleSheet** - Update a Google Sheet
- **createGoogleSlides** - Create a presentation
- **updateGoogleSlides** - Update an existing presentation

### Google Sheets Formatting Tools
- **getGoogleSheetContent** - Get spreadsheet content with cell information
- **formatGoogleSheetCells** - Format cell properties
- **formatGoogleSheetText** - Apply text formatting to cells
- **formatGoogleSheetNumbers** - Apply number/date formatting
- **setGoogleSheetBorders** - Configure cell borders
- **mergeGoogleSheetCells** - Merge cells in a range
- **addGoogleSheetConditionalFormat** - Add conditional formatting rules

### Google Slides Formatting Tools
- **getGoogleSlidesContent** - Get presentation content with element IDs
- **formatGoogleSlidesText** - Apply text formatting to slide elements
- **formatGoogleSlidesParagraph** - Apply paragraph formatting
- **styleGoogleSlidesShape** - Style shapes and elements
- **setGoogleSlidesBackground** - Set slide background color
- **createGoogleSlidesTextBox** - Create formatted text box
- **createGoogleSlidesShape** - Create styled shape

## Google Cloud Setup

### 1. Create a Google Cloud Project
- Go to the [Google Cloud Console](https://console.cloud.google.com)
- Click "Select a project" > "New Project"
- Name your project (e.g., "Google Drive MCP")
- Note the Project ID for later

### 2. Enable Required APIs
- In your project, go to "APIs & Services" > "Library"
- Search for and enable each of these APIs:
  - **Google Drive API**
  - **Google Docs API**
  - **Google Sheets API**
  - **Google Slides API**
- Wait for each API to be enabled before proceeding

### 3. Configure OAuth Consent Screen
- Go to "APIs & Services" > "OAuth consent screen"
- Under 'Branding' fill in the required fields:
  - App name: "My Personal Google Drive MCP"
  - User support email: Your email
  - Developer contact: Your email
- Under 'Audience':
  - Choose "External" (default choice) or "Internal" for Google Workspace accounts
  - Add your email as a test user
- Under 'Data Access' add scopes:
  - `./auth/drive.file`
  - `.../auth/documents`
  - `.../auth/spreadsheets`
  - `.../auth/presentations`
  - `.../auth/drive`
  - `.../auth/drive.readonly`

### 4. Create OAuth 2.0 Credentials
- Go to "APIs & Services" > "Credentials"
- Click "+ CREATE CREDENTIALS" > "OAuth client ID"
- Application type: **Desktop app** (Important!)
- Name: "Google Drive MCP Client"
- Click "Create"
- Download the JSON file
- Rename it to `gcp-oauth.keys.json`

## Configuration

### OAuth Credentials Configuration

The server supports multiple methods for providing OAuth credentials (in order of priority):

#### 1. **Environment Variable** (Recommended)
```bash
export GOOGLE_DRIVE_OAUTH_CREDENTIALS="/path/to/your/gcp-oauth.keys.json"
```

#### 2. **Default File Location**
Place `gcp-oauth.keys.json` in the project root directory

### Token Storage

Authentication tokens are stored securely following the XDG Base Directory specification:

| Priority | Location | Configuration |
|----------|----------|---------------|
| 1 | Custom path | Set `GOOGLE_DRIVE_MCP_TOKEN_PATH` environment variable |
| 2 | XDG Config | `$XDG_CONFIG_HOME/google-drive-mcp/tokens.json` |
| 3 | Default | `~/.config/google-drive-mcp/tokens.json` |

## Authentication Flow

The server uses OAuth 2.0 for secure authentication:

### Automatic Authentication (First Run)
1. Server detects missing tokens and starts local auth server
2. Your browser opens to Google's consent page
3. Grant the requested permissions
4. Tokens are saved securely to `~/.config/google-drive-mcp/tokens.json`
5. Server continues startup

### Manual Re-authentication

```bash
npm run auth
```

## Troubleshooting

### Common Issues

#### "OAuth credentials not found"
- Download credentials from Google Cloud Console
- Either set the environment variable or place the file in the project root

#### "Tokens expired" or "Invalid grant"
```bash
rm ~/.config/google-drive-mcp/tokens.json
npm run auth
```

#### MCP "No result received" errors
- Ensure you're using the full path to node in Claude Desktop config
- Example: `/opt/homebrew/bin/node` instead of just `node`

## Development

### Building
```bash
npm run build    # Compile TypeScript
npm run watch    # Compile and watch for changes
```

### Project Structure
```
google-drive-mcp/
├── src/
│   ├── index.ts           # Main server implementation
│   ├── auth.ts            # Main authentication module
│   └── auth/              # Authentication components
├── dist/                  # Compiled JavaScript (generated)
├── gcp-oauth.keys.json    # OAuth credentials (create from example)
└── README.md
```

## Contributing

### To Jumpstart Health Fork
1. Fork this repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

### To Upstream
Consider contributing enhancements back to the [upstream repository](https://github.com/piotr-agier/google-drive-mcp).

## License

MIT - See LICENSE file for details

## Acknowledgments

- Original project: [piotr-agier/google-drive-mcp](https://github.com/piotr-agier/google-drive-mcp)
- Built on [Model Context Protocol](https://modelcontextprotocol.io)
- Uses [Google APIs Node.js Client](https://github.com/googleapis/google-api-nodejs-client)
