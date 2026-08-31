# MarkLogic MCP

An MCP (Model Context Protocol) server that lets MCP-compatible clients (e.g., Claude,
Copilot, or any MCP-aware agent) search, retrieve, and cite documents stored in
**MarkLogic Server**, backed by the MarkLogic Retrieval API (MRA).

This repository hosts the **distribution package** — the deployer and example
configuration files you need to run the MarkLogic MCP server against your own
MarkLogic instance. The server itself is distributed as a prebuilt Docker image on
Docker Hub.

## What you need

| Artifact | Where to get it | What it's for |
|---|---|---|
| `marklogic-mcp-<version>.zip` | [Releases](../../releases) (this repo) | `mra-deployer/` (installs MRA into your MarkLogic Server) + `examples/marklogic-mcp/` (sample `.env`/`config.yaml`/`docker-compose.yaml`) + reference docs |
| MCP server image | [Docker Hub: `marklogic/marklogic-mcp`](https://hub.docker.com/r/marklogic/marklogic-mcp) | The prebuilt MarkLogic MCP server container |

## Prerequisites

- A running MarkLogic Server with a REST app server configured, and data already loaded.
- Docker (or Rancher Desktop).
- Java and Gradle (used by `mra-deployer`).

## Quick start

1. **Download and unzip the latest release**

   ```bash
   curl -LO https://github.com/<org>/<repo>/releases/latest/download/marklogic-mcp-<version>.zip
   unzip marklogic-mcp-<version>.zip
   cd marklogic-mcp-<version>
   ```

2. **Deploy the MarkLogic Retrieval API (MRA) to your MarkLogic Server**

   ```bash
   cd mra-deployer
   vi gradle.properties   # set mlHost, mlRestPort, mlUsername, mlPassword, mlAppName, etc.
   ./gradlew installMra -i
   ```

   Then add the `mra-reader` role to your application's reader role so users can query
   through MRA.

3. **Pull the MCP server image**

   ```bash
   docker pull marklogic/marklogic-mcp:<version>
   ```

4. **Configure and start the MCP server**

   ```bash
   cd ../examples/marklogic-mcp
   cp .env.example .env
   ```

   Edit `.env` to point `MARKLOGIC_MCP_IMAGE` at the tag you pulled, and edit
   `config.yaml` to set `marklogic_url`/`auth_method` for your MarkLogic host and
   credentials (all three tool workflows must match). Then:

   ```bash
   docker compose up -d
   curl http://localhost:8088/.well-known/oauth-protected-resource
   ```

5. **Connect an MCP client** using the ephemeral session endpoint, e.g.:

   ```json
   "MarkLogicMCP-headers": {
     "url": "http://localhost:8088/api/v1/agent/mra-agent/session/ephemeral/mcp",
     "type": "http",
     "headers": { "Authorization": "Basic <base64 username:password>" }
   }
   ```

For full details — including bash/Python alternatives to Gradle for deployment, OAuth
setup for MCP clients, and troubleshooting — see `MARKLOGIC-MCP-QUICKSTART.md` and the
`docs/` folder included in the ZIP.

## Documentation

- [MarkLogic Retrieval API docs](https://github.com/<org>/<repo>) *(link to hosted docs
  if available)*
- Full reference documentation is also included in the ZIP under `docs/` and
  `mra-api-docs/`.

## Support

*(Add support/contact information here — e.g., link to a support portal, issue tracker,
or contact email.)*

## License

Copyright © 2026 Progress Software Corporation and/or its subsidiaries or affiliates.
Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.
