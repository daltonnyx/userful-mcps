[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/daltonnyx-userful-mcps-badge.png)](https://mseep.ai/app/daltonnyx-userful-mcps)

# Useful Model Context Protocol Servers (MCPS)

A collection of standalone Python scripts that implement Model Context Protocol
(MCP) servers for various utility functions. Each server provides specialized
tools that can be used by AI assistants or other applications that support the
MCP protocol.

## What is MCP?

The Model Context Protocol (MCP) is a standardized way for AI assistants to
interact with external tools and services. It allows AI models to extend their
capabilities by calling specialized functions provided by MCP servers.
Communication happens via standard input/output (stdio) using JSON messages.

## Available Servers

Each MCP server is designed to be run using a Python environment manager like
`uv`.

### YouTube Data Extractor (`ytdlp`)

A server that extracts information from YouTube videos using yt-dlp.

**Tools:**

- **Extract Chapters**: Get chapter information from a YouTube video.
- **Extract Subtitles**: Get subtitles from a YouTube video for specific
  chapters or the entire video.

**MCP Server Configuration:**

```json
"mcpServers": {
  "ytdlp": {
    "name": "youtube", // Optional friendly name for the client
    "command": "uv",
    "args": [
      "run",
      "--directory", "<path/to/repo>/useful-mcps/ytdlp", // Path to the MCP directory containing pyproject.toml
      "--", // Separator before script arguments, if any
      "ytdlp_mcp" // Match the script name defined in pyproject.toml [project.scripts]
    ]
    // 'cwd' is not needed when using --directory
  }
}
```

### Word Document Processor (`docx_replace`)

A server for manipulating Word documents, including template processing and PDF
conversion.

**Tools:**

- **Process Template**: Replace placeholders in Word templates and manage
  content blocks.
- **Get Template Keys**: Extract all replacement keys from a Word document
  template.
- **Convert to PDF**: Convert a Word document (docx) to PDF format.

**MCP Server Configuration:**

```json
"mcpServers": {
  "docx_replace": {
    "name": "docx", // Optional friendly name
    "command": "uv",
    "args": [
      "run",
      "--directory", "<path/to/repo>/useful-mcps/docx_replace", // Path to the MCP directory
      "--",
      "docx_replace_mcp" // Match the script name defined in pyproject.toml
    ]
  }
}
```

### PlantUML Renderer (`plantuml`)

A server for rendering PlantUML diagrams using a PlantUML server (often run via
Docker).

**Tools:**

- **Render Diagram**: Convert PlantUML text to diagram images (e.g., PNG).

**MCP Server Configuration:**

```json
"mcpServers": {
  "plantuml": {
    "name": "plantuml", // Optional friendly name
    "command": "uv",
    "args": [
      "run",
      "--directory", "<path/to/repo>/useful-mcps/plantuml", // Path to the MCP directory
      "--",
      "plantuml_server" // Match the script name defined in pyproject.toml
    ]
  }
}
```

_(Note: Requires a running PlantUML server accessible, potentially managed via
Docker as implemented in the service)._

### Mermaid Renderer (`mermaid`)

A server for rendering Mermaid diagrams using the mermaidchart.com API.

**Tools:**

- **Render Mermaid Chart**: Convert Mermaid code into a PNG image by creating a
  document on mermaidchart.com.

**MCP Server Configuration:**

```json
"mcpServers": {
  "mermaid": {
    "name": "mermaid", // Optional friendly name
    "command": "uv",
    "args": [
      "run",
      "--directory", "<path/to/repo>/useful-mcps/mermaid", // Path to the MCP directory
      "--",
      "mermaid_mcp" // Match the script name defined in pyproject.toml
    ],
    "env": { // Environment variables needed by the MCP
        "MERMAID_CHART_ACCESS_TOKEN": "YOUR_API_TOKEN_HERE"
    }
  }
}
```

_(Note: Requires a Mermaid Chart API access token set as an environment
variable)._

### Rss feed to markdown (`rss2md`)

A server for Convert rss feed content to markdown format with date filtering.

**Tools:**

- **fetch_rss_to_markdown**: Fetches an RSS feed, filters articles by date, and
  returns matching articles formatted as a Markdown list..

**MCP Server Configuration:**

```json
"mcpServers": {
  "mermaid": {
    "name": "rss2md", // Optional friendly name
    "command": "uv",
    "args": [
      "run",
      "--directory", "<path/to/repo>/useful-mcps/rss2md", // Path to the MCP directory
      "--",
      "rss2md_mcp" // Match the script name defined in pyproject.toml
    ],
    "env": { // Environment variables needed by the MCP
    }
  }
}
```

## Installation

1. **Clone the repository:**

   ```bash
   git clone https://github.com/daltonnyx/useful-mcps.git # Replace with the actual repo URL if different
   cd useful-mcps
   ```

2. **Install `uv`:** If you don't have `uv`, install it:

   ```bash
   pip install uv
   # or follow instructions at https://github.com/astral-sh/uv
   ```

3. **Dependencies:** Dependencies are managed per-MCP via `pyproject.toml`.
   `uv run` will typically handle installing them automatically in a virtual
   environment when you run an MCP for the first time using `--directory`.

## Usage

### Running a Server

It's recommended to run each MCP server using `uv run --directory <path>`
pointing to the specific MCP's directory. `uv` handles the virtual environment
and dependencies based on the `pyproject.toml` found there.

Example (from the root `useful-mcps` directory):

```bash
# Run the YouTube MCP
uv run --directory ./ytdlp ytdlp_mcp

# Run the Mermaid MCP (ensure token is set in environment)
uv run --directory ./mermaid mermaid_mcp
```

Alternatively, configure your MCP client (like the example JSON configurations
above) to execute the `uv run --directory ...` command directly.

### Connecting to a Server

Configure your MCP client application to launch the desired server using the
`command` and `args` structure shown in the "MCP Server Configuration" examples
for each server. Ensure the `command` points to your `uv` executable and the
`args` correctly specify `--directory` with the path to the MCP's folder and the
script name to run. Pass necessary environment variables (like API tokens) using
the `env` property.

## Tool-Specific Usage Examples

These show example `arguments` you would send to the `call_tool` function of the
respective MCP server.

### YouTube Data Extractor

#### Extract Chapters

```json
{
  "url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
}
```

#### Extract Subtitles

```json
{
  "url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
  "language": "en",
  "chapters": [
    {
      "title": "Introduction",
      "start_time": "00:00:00",
      "end_time": "00:01:30"
    }
  ]
}
```

### Word Document Processor

#### Process Template

```json
{
  "template_file": "/path/to/template.docx",
  "replacements": {
    "name": "John Doe",
    "date": "2023-05-15"
  },
  "blocks": {
    "optional_section": true,
    "alternative_section": false
  },
  "output_filename": "/path/to/output.docx"
}
```

_(Note: `template_file` and `docx_file` can also accept base64 encoded strings
instead of paths)_

#### Get Template Keys

```json
{
  "template_file": "/path/to/template.docx"
}
```

#### Convert to PDF

```json
{
  "docx_file": "/path/to/document.docx",
  "pdf_output": "/path/to/output.pdf"
}
```

### PlantUML Renderer

#### Render Diagram

```json
{
  "input": "participant User\nUser -> Server: Request\nServer --> User: Response",
  "output_path": "/path/to/save/diagram.png"
}
```

_(Note: `input` can also be a path to a `.puml` file)_

### Mermaid Renderer

#### Render Mermaid Chart

```json
{
  "mermaid_code": "graph TD;\n    A-->B;\n    A-->C;\n    B-->D;\n    C-->D;",
  "output_path": "/path/to/save/mermaid.png",
  "theme": "default" // Optional, e.g., "default", "dark", "neutral", "forest"
}
```

## Development

### Adding a New MCP Server

1. Create a new directory for your MCP (e.g., `my_new_mcp`).
2. Inside the directory, create:
   - `pyproject.toml`: Define project metadata, dependencies, and the script
     entry point (e.g., `[project.scripts]` section mapping
     `my_new_mcp = "my_new_mcp:main"`).
   - `pyrightconfig.json`: (Optional) For type checking.
   - Your main Python file (e.g., `my_new_mcp.py`): Implement the MCP logic
     using the `mcp` library (see template below).
3. Implement the required classes and functions (`serve`, `list_tools`,
   `call_tool`).

Basic template (`my_new_mcp.py`):

```python
import json
import logging
import asyncio
from typing import List, Dict, Any, Optional
# Assuming mcp library is installed or available
# from mcp import Server, Tool, TextContent, stdio_server
# Placeholder imports if mcp library structure is different
from typing import Protocol # Using Protocol as placeholder

# Placeholder definitions if mcp library isn't directly importable here
class Tool(Protocol):
    name: str
    description: str
    inputSchema: dict

class TextContent(Protocol):
    type: str
    text: str

class Server:
    def __init__(self, name: str): pass
    def list_tools(self): pass # Decorator
    def call_tool(self): pass # Decorator
    def create_initialization_options(self): pass
    async def run(self, read_stream, write_stream, options): pass

# Placeholder context manager
class stdio_server:
    async def __aenter__(self): return (None, None) # Dummy streams
    async def __aexit__(self, exc_type, exc, tb): pass


# Pydantic is often used for schema definition
# from pydantic import BaseModel
# class MyInput(BaseModel):
#     param1: str
#     param2: int

class MyInputSchema: # Placeholder if not using Pydantic
    @staticmethod
    def model_json_schema():
      return {"type": "object", "properties": {"param1": {"type": "string"}, "param2": {"type": "integer"}}, "required": ["param1", "param2"]}


class MyTools:
    TOOL_NAME = "my.tool"

class MyService:
    def __init__(self):
        # Initialize resources if needed
        pass

    def my_function(self, param1: str, param2: int) -> dict:
        # Implement your tool functionality
        logging.info(f"Running my_function with {param1=}, {param2=}")
        # Replace with actual logic
        result_content = f"Result: processed {param1} and {param2}"
        return {"content": result_content}

async def serve() -> None:
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
    server = Server("mcp-my-service")
    service = MyService()

    @server.list_tools()
    async def list_tools() -> list[Tool]:
        logging.info("list_tools called")
        return [
            Tool(
                name=MyTools.TOOL_NAME,
                description="Description of my tool",
                # Use Pydantic's schema or manually define
                inputSchema=MyInputSchema.model_json_schema(),
            ),
        ]

    @server.call_tool()
    async def call_tool(name: str, arguments: dict) -> list[TextContent]:
        logging.info(f"call_tool called with {name=}, {arguments=}")
        try:
            if name == MyTools.TOOL_NAME:
                # Add validation here if not using Pydantic
                param1 = arguments.get("param1")
                param2 = arguments.get("param2")
                if param1 is None or param2 is None:
                     raise ValueError("Missing required arguments")

                result = service.my_function(param1, int(param2)) # Ensure type conversion if needed
                logging.info(f"Tool executed successfully: {result=}")
                return [TextContent(type="text", text=json.dumps(result))] # Return JSON string
            else:
                logging.warning(f"Unknown tool requested: {name}")
                raise ValueError(f"Unknown tool: {name}")
        except Exception as e:
            logging.error(f"Error executing tool {name}: {e}", exc_info=True)
            # Return error as JSON
            error_payload = json.dumps({"error": str(e)})
            return [TextContent(type="text", text=error_payload)]

    options = server.create_initialization_options()
    logging.info("Starting MCP server...")
    async with stdio_server() as (read_stream, write_stream):
        await server.run(read_stream, write_stream, options)
    logging.info("MCP server stopped.")

def main():
    # Entry point defined in pyproject.toml `[project.scripts]`
    try:
        asyncio.run(serve())
    except KeyboardInterrupt:
        logging.info("Server interrupted by user.")

if __name__ == "__main__":
    # Allows running directly via `python my_new_mcp.py` for debugging
    main()
```

### Testing

Run tests using pytest from the root directory:

```bash
pytest tests/
```

_(Ensure test dependencies are installed, potentially via
`uv pip install pytest` or by adding `pytest` to the dev dependencies in one of
the `pyproject.toml` files)._

## License

[MIT License](LICENSE)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
