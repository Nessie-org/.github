# Nessie

**Nessie** is a plugin-based graph exploration and visualization platform written in Python. It provides a lightweight core runtime, a well-defined API contract, and a growing ecosystem of interchangeable plugins for sourcing, manipulating, and rendering graphs.

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                  nessie-platform                    │
│         (plugin registry & action dispatcher)       │
└───────────────────────┬─────────────────────────────┘
                        │ depends on
                        ▼
             ┌─────────────────────┐
             │      nessie-api     │
             │  (shared contracts) │
             └─────────────────────┘
                        │
          ┌─────────────┼──────────────┐
          │             │              │
          ▼             ▼              ▼
   Datasource      Visualizer    Manipulation
    Plugins         Plugins        Plugins
```

The platform discovers and loads plugins at runtime via Python entry points (`nessie_plugins`). Plugins declare the **actions** they handle; the platform selects the best one through an optional prioritization plugin.

---

## Repositories

### Core

| Repository | Package | Description |
|---|---|---|
| [Nessie](https://github.com/Nessie-org/Nessie) | `nessie-platform` | Core platform: plugin registry, action dispatcher, and prioritization engine. |
| [nessie-api](https://github.com/Nessie-org/nessie-api) | `nessie-api` | Shared models, interfaces, and contracts used by all plugins and the platform. |

### Interfaces

| Repository | Package | Description |
|---|---|---|
| [nessie-cli](https://github.com/Nessie-org/nessie-cli) | `nessie-cli` | Command-line interface for Nessie, built with a [textX](https://textx.io/) DSL. |
| [nessie-web](https://github.com/Nessie-org/nessie-web) | `nessie-web` | Plugin that renders the graph UI in a web browser using Jinja2 templates. |
| [django-web-nessie](https://github.com/Nessie-org/django-web-nessie) | — | Django-based web client implementation for Nessie. |

### Datasource Plugins

Datasource plugins source a graph from an external system and expose it to the platform via the `Source_*` actions.

| Repository | Package | Description |
|---|---|---|
| [nessie-npm-dependencies-plugin](https://github.com/Nessie-org/nessie-npm-dependencies-plugin) | `nessie-npm-dependencies-plugin` | Builds a dependency graph starting from a given npm package by querying the npm registry. |
| [nessie-python-datasource-plugin](https://github.com/Nessie-org/nessie-python-datasource-plugin) | `nessie-python-datasource-plugin` | Sources a graph from a Python module or a JSON file. |
| [nessie-relationaldb-datasource-plugin](https://github.com/Nessie-org/nessie-relationaldb-datasource-plugin) | `nessie-relationaldb-datasource-plugin` | Loads a graph from a relational database. |

### Visualizer Plugins

Visualizer plugins render a graph for the user.

| Repository | Package | Description |
|---|---|---|
| [nessie-simple-visualizer-plugin](https://github.com/Nessie-org/nessie-simple-visualizer-plugin) | `nessie-simple-visualizer-plugin` | Lightweight plugin for simple graph visualization. |
| [neisse-graph-visualiser-block](https://github.com/Nessie-org/neisse-graph-visualiser-block) | `nessie-block-visualiser` | Renders a graph as a blocks view, displaying all node and edge attributes. |

### Manipulation Plugins

Manipulation plugins transform or filter an in-memory graph.

| Repository | Package | Description |
|---|---|---|
| [nessie-graph-manipulation-plugin](https://github.com/Nessie-org/nessie-graph-manipulation-plugin) | `nessie-graph-manipulation-plugin` | Provides graph filtering and manipulation operations. |

---

## Getting Started

1. **Install the platform and API:**
   ```bash
   pip install nessie-platform nessie-api
   ```

2. **Install the plugins you need**, for example:
   ```bash
   pip install nessie-npm-dependencies-plugin nessie-web
   ```

3. **Use the CLI** (see [nessie-cli](https://github.com/Nessie-org/nessie-cli)) or **embed the platform** in your own application:
   ```python
   from nessie_platform import Platform

   platform = Platform()
   graph = platform.get_plugin("Source_Python").handle(action)
   ```

   Plugins are discovered automatically via entry points — no manual registration required.

---

## License

Each repository carries its own license. The core components use the **Apache-2.0** license; some plugins use the **MIT** license. See the individual repositories for details.