# 🐉 Nessie Graph Explorer

> A modular, plugin-driven platform for loading, visualizing, and analyzing graphs in the browser.

---

## What is Nessie?

**Nessie Graph Explorer** is an open-source developer tool for graph visualization and analysis. It is built around a plugin system — every piece of functionality (loading data, rendering the UI, filtering, visualizing nodes and edges) is provided by a Python plugin that registers handlers for named actions.

- **Connect any data source** — SQLite databases, npm registries, Python AST, CSV files, or your own API
- **Multiple visualizers** — swap between rendering styles without reloading
- **Attribute filtering** — filter nodes by any attribute with `==`, `!=`, `>`, `>=`, `<`, `<=` operators
- **Full-text search** — search across all node attributes simultaneously
- **Multi-workspace** — open multiple graphs side by side in tabs
- **In-browser CLI** — create, edit, and delete nodes and edges live from the console panel
- **D3-powered force layout** — interactive, draggable, zoom-pannable graphs
- **CLI tooling** — npm-based CLI for project setup, plugin management, and server startup

---

## Quick Start

```bash
# Install the CLI
npm install -g @nessie-org/cli

# Create a project (scaffolds venv, clones core, installs plugins)
nessie setup my-project

# Start the Flask web server
cd my-project
nessie start flask
```

Open [http://localhost:8000](http://localhost:8000) — click **+** to load your first graph.

---

## Architecture

```
┌──────────────────────────────────────────────────────────┐
│              Web Server (Flask or Django)                 │
├──────────────────────────────────────────────────────────┤
│   Datasource    │   Visualizer    │   Manipulation       │  Plugins
│   load_graph    │ visualise_graph │   filter_graph       │
│   Nessie Web    │   nessie-cli    │   ···                │
├──────────────────────────────────────────────────────────┤
│         Platform: PluginManager · WorkspaceManager       │
│                       Context                            │
├──────────────────────────────────────────────────────────┤
│   nessie-api: Graph · Node · Edge · Action · Plugin      │
└──────────────────────────────────────────────────────────┘
```

Plugins communicate through **named actions**. The PluginManager discovers all installed plugins via Python entry points at startup — no manual wiring.

---

## Repositories

### Core

| Repo | Description |
|---|---|
| [`nessie-api`](https://github.com/Nessie-org/nessie-api) | Shared models: Graph, Node, Edge, Plugin, Action, FilterExpression, Context protocol |
| [`Nessie`](https://github.com/Nessie-org/Nessie) | Platform runtime: PluginManager, WorkspaceManager, Context implementation |

### Tooling

| Repo | Description |
|---|---|
| [`@nessie-org/cli`](https://github.com/Nessie-org/nessie-cli-npm) | npm CLI — project setup, plugin management, server start |

### Web Servers

| Repo | Description |
|---|---|
| [`flask-web-nessie`](https://github.com/Nessie-org/flask-web-nessie) | Flask HTTP server |
| [`django-web-nessie`](https://github.com/Nessie-org/django-web-nessie) | Django HTTP server |

### Plugins

| Repo | Action | Description |
|---|---|---|
| [`nessie-web`](https://github.com/Nessie-org/nessie-web) | `render` | Web UI renderer — D3 graph, sidebar, minimap, console, filters |
| [`nessie-relationaldb-datasource-plugin`](https://github.com/Nessie-org/nessie-relationaldb-datasource-plugin) | `load_graph` | SQLite → Graph (tables = nodes, FK = edges) |
| [`nessie-npm-dependencies-plugin`](https://github.com/Nessie-org/nessie-npm-dependencies-plugin) | `load_graph` | npm dependency tree crawler |
| [`nessie-python-datasource-plugin`](https://github.com/Nessie-org/nessie-python-datasource-plugin) | `load_graph` | Python AST → Graph (classes, functions, call edges) |
| [`nessie-graph-manipulation-plugin`](https://github.com/Nessie-org/nessie-graph-manipulation-plugin) | `filter_graph` | Attribute filters + full-text search |
| [`nessie-simple-visualizer-plugin`](https://github.com/Nessie-org/nessie-simple-visualizer-plugin) | `visualise_graph` | Circle-based SVG visualizer |
| [`neisse-graph-visualiser-block`](https://github.com/Nessie-org/neisse-graph-visualiser-block) | `visualise_graph` | Block/card SVG visualizer showing all attributes |
| [`nessie-cli`](https://github.com/Nessie-org/nessie-cli) | `cli_execute` | In-browser graph editing CLI (textX grammar) |

---

## Building a Plugin

Any Python package that declares a `nessie_plugins` entry point and handles one or more actions is a Nessie plugin. The minimum viable plugin:

```python
from nessie_api.models import Graph, GraphType, plugin, Action, SetupRequirementType
from nessie_api.protocols import Context

def load_graph(action: Action, context: Context) -> Graph:
    path = action.payload["File Path"]
    # ... build and return a Graph object ...

@plugin("My Datasource")
def my_plugin():
    return {
        "handlers": {"load_graph": load_graph},
        "requires": [],
        "setup_requires": {"File Path": SetupRequirementType.STRING},
    }
```

```toml
# pyproject.toml
[project.entry-points."nessie_plugins"]
my_plugin = "my_package:my_plugin"
```

📖 **Full documentation:** [nessie-docs](https://github.com/Nessie-org/nessie-docs)

---

## License

All Nessie repositories are licensed under Apache 2.0 or MIT.  
Copyright © 2026 [Nessie-org](https://github.com/Nessie-org)
