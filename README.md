# AiDD MCP Server

An MCP server that provides a comprehensive set of tools for AI-driven development workflows. Features include file system operations, code analysis using tree-sitter for multiple programming languages, Git operations, code execution, and system information retrieval. Designed to enhance AI's capability to assist in software development tasks.

## Installation

```bash
# Using mcp-get
npx @michaellatman/mcp-get@latest install mcp-server-aidd

# Using pip
pip install mcp-server-aidd

# Using uv
uvx mcp-server-aidd
```

## Claude Desktop Setup

Add to your `claude_desktop_config.json`:

```json
{
    "mcpServers": {
        "mcp-server-aidd": {
            "command": "uvx",
            "args": ["mcp-server-aidd"]
        }
    }
}
```

## Key Features

-   File system operations (read, write, edit, move, delete)
-   Directory management and traversal
-   Multi-language code analysis using tree-sitter
-   Multi-language code execution with safety measures
-   Git operations (status, diff, commit, branch management)
-   Security controls with configurable workspace boundaries

## Available Tools

### Basic File Operations

| Tool                | Parameters                          | Returns                                       |
| ------------------- | ----------------------------------- | --------------------------------------------- |
| read_file           | path: string                        | File content                                  |
| read_multiple_files | paths: string[]                     | Multiple file contents with headers           |
| write_file          | path: string, content: string       | Success confirmation                          |
| move_file           | source: string, destination: string | Success confirmation                          |
| delete_file         | path: string                        | Success confirmation                          |
| get_file_info       | path: string                        | File metadata (size, timestamps, permissions) |

Common usage:

```bash
# Read file
aidd-cli --tool read_file --args '{"path": "src/main.py"}'

# Write file
aidd-cli --tool write_file --args '{"path": "output.txt", "content": "Hello World"}'

# Get file info
aidd-cli --tool get_file_info --args '{"path": "src/main.py"}'
```

### Complex File Operations

#### edit_file

Pattern-based file editing with preview support:

```json
{
    "path": "src/main.py",
    "edits": [
        {
            "oldText": "def old_function():",
            "newText": "def new_function():"
        }
    ],
    "dryRun": false,
    "options": {
        "preserveIndentation": true,
        "normalizeWhitespace": true,
        "partialMatch": true
    }
}
```

Returns: Diff of changes or preview in dry run mode.

### Directory Operations

| Tool                     | Parameters                                               | Returns                        |
| ------------------------ | -------------------------------------------------------- | ------------------------------ |
| get_allowed_directory    | none                                                     | Current allowed directory path |
| update_allowed_directory | directory: string (absolute path)                        | Success confirmation           |
| list_directory           | path: string                                             | Directory contents list        |
| create_directory         | path: string                                             | Success confirmation           |
| search_files             | pattern: string, path?: string, include_hidden?: boolean | Matching files list            |

#### directory_tree

Generates complete directory structure:

```json
{
    "path": "src",
    "include_hidden": false
}
```

Returns: JSON tree structure of directory contents.

Common usage:

```bash
# List directory
aidd-cli --tool list_directory --args '{"path": "."}'

# Search for Python files
aidd-cli --tool search_files --args '{"pattern": ".py", "path": "src"}'
```

### Git Operations

| Tool         | Parameters                             | Returns                          |
| ------------ | -------------------------------------- | -------------------------------- |
| git_init     | path: string, initial_branch?: string  | Repository initialization status |
| git_status   | repo_path: string                      | Working directory status         |
| git_add      | repo_path: string, files: string[]     | Staging confirmation             |
| git_reset    | repo_path: string                      | Unstaging confirmation           |
| git_checkout | repo_path: string, branch_name: string | Branch switch confirmation       |

#### Complex Git Operations

##### git_commit

```json
{
    "repo_path": ".",
    "message": "feat: add new feature"
}
```

Returns: Commit hash and confirmation.

##### git_diff

```json
{
    "repo_path": ".",
    "target": "main"
}
```

Returns: Detailed diff output.

##### git_log

```json
{
    "repo_path": ".",
    "max_count": 10
}
```

Returns: Array of commit entries with hash, author, date, and message.

Common usage:

```bash
# Check status
aidd-cli --tool git_status --args '{"repo_path": "."}'

# Create and switch to new branch
aidd-cli --tool git_create_branch --args '{"repo_path": ".", "branch_name": "feature/new-branch"}'
```

### Code Analysis

#### tree_sitter_map

Analyzes source code structure:

```json
{
    "path": "src"
}
```

Returns:

-   Classes and their methods
-   Functions and parameters
-   Module structure
-   Code organization statistics
-   Inheritance relationships

Supported Languages:

-   Python (.py)
-   JavaScript (.js, .jsx, .mjs, .cjs)
-   TypeScript (.ts, .tsx)
-   Java (.java)
-   C++ (.cpp, .hpp, .cc)
-   Ruby (.rb, .rake)
-   Go (.go)
-   Rust (.rs)
-   PHP (.php)
-   C# (.cs)
-   Kotlin (.kt, .kts)

### System Information

| Tool            | Parameters | Returns                      |
| --------------- | ---------- | ---------------------------- |
| get_system_info | none       | Comprehensive system details |

Returns:

```json
{
  "working_directory": "/path/to/project",
  "system": {
    "os", "os_version", "architecture", "python_version"
  },
  "wifi_network": "MyWiFi",
  "cpu": {
    "physical_cores", "logical_cores", "total_cpu_usage"
  },
  "memory": { "total", "available", "used_percentage" },
  "disk": { "total", "free", "used_percentage" },
  "mac_details": {  // Only present on macOS
    "model": "Mac mini",
    "chip": "Apple M2",
    "serial_number": "XXX"
  }
}
```

Provides essential system information in a clean, readable format.

```bash
# Get system information
aidd-cli --tool get_system_info
```

### Code Execution

#### execute_code

Executes code in various programming languages with safety measures and restrictions.

```json
{
    "language": "python",
    "code": "print('Hello, World!')",
    "timeout": 5
}
```

**Supported Languages:**

-   Python (python3)
-   JavaScript (Node.js)
-   Ruby
-   PHP
-   Go
-   Rust

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|---------|----------|---------------------------------------|
| language | string | Yes | Programming language to use |
| code | string | Yes | Code to execute |
| timeout | integer | No | Maximum execution time (default: 5s) |

**Example Usage:**

```bash
# Python example
aidd-cli --tool execute_code --args '{
    "language": "python",
    "code": "print(sum(range(10)))"
}'

# JavaScript example
aidd-cli --tool execute_code --args '{
    "language": "javascript",
    "code": "console.log(Array.from({length: 5}, (_, i) => i*2))"
}'

# Ruby example
aidd-cli --tool execute_code --args '{
    "language": "ruby",
    "code": "puts (1..5).reduce(:+)"
}'

# Go example
aidd-cli --tool execute_code --args '{
    "language": "go",
    "code": "fmt.Println(\"Hello, Go!\")"
}'
```

**Requirements:**

-   Respective language runtimes must be installed
-   Commands must be available in system PATH
-   Proper permissions for temporary file creation

⚠️ **Security Warning:**
This tool executes arbitrary code on your system. Always:

1. Review code thoroughly before execution
2. Understand the code's purpose and expected outcome
3. Never execute untrusted code
4. Be aware of potential system impacts
5. Monitor execution output

## Configuration

Configuration file: `~/.aidd/config.json`

```json
{
    "allowed_directory": "/path/to/workspace"
}
```

## CLI Usage

Basic command structure:

```bash
aidd-cli --tool <tool_name> --args '<json_arguments>'

# List available tools
aidd-cli --list-tools

# Enable debug output
aidd-cli --debug --tool <tool_name> --args '<json_arguments>'
```

## Debugging

Use MCP Inspector for debugging:

```bash
npx @modelcontextprotocol/inspector run
```

## Security

-   Operations restricted to configured allowed directory
-   Path traversal prevention
-   File permission preservation
-   Safe operation handling

## Upcoming Features

-   GitHub tools:
    -   PR Description Generator
    -   Code Review
    -   Actions Manager
-   Pivotal Tracker tools:
    -   Story Generator
    -   Story Manager

## Development Status

Currently in active development. Features and API may change.

## License

MIT License - see [LICENSE](LICENSE)
