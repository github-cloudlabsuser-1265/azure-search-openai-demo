# Azure Search + OpenAI Demo Backend

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](../../actions)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](../../LICENSE)
[![Python](https://img.shields.io/badge/python-3.11%2B-blue)](https://www.python.org/)

## Project Overview

This backend powers the Azure Search + OpenAI Demo, enabling advanced retrieval-augmented generation (RAG), chat, Q&A, and file management with seamless integration to Azure AI services. It is designed for cloud-native deployments and local development, supporting both enterprise and research scenarios.

## Architecture & Technology Stack

- **Framework:** [Quart](https://pgjones.gitlab.io/quart/) (async Flask-compatible)
- **Cloud Services:** Azure Cognitive Search, Azure OpenAI, Azure Blob Storage, Azure AD, CosmosDB (optional)
- **Containerization:** Docker support for easy deployment
- **Authentication:** Azure AD (with support for managed identity and local dev)
- **Other:** Streaming, agentic retrieval, vision (GPT-4V), and speech (Azure Speech)

```
+-------------------+         +-------------------+         +-------------------+
|   Frontend (UI)   | <-----> |   Backend (Quart) | <-----> |   Azure Services  |
+-------------------+         +-------------------+         +-------------------+
```

## Features

- **Chat and Q&A APIs:** Multi-turn chat and single-turn Q&A endpoints powered by Azure OpenAI and Azure Cognitive Search.
- **User File Uploads:** Secure file upload, listing, and deletion for authenticated users, with ingestion into Azure Storage and search index.
- **Vision and Speech:** Optional support for GPT-4V (vision) and Azure Speech Services for text-to-speech.
- **Authentication:** Azure AD-based authentication and access control, with support for global and per-user document access.
- **Agentic Retrieval:** Optional agent-based retrieval using Azure Cognitive Search Knowledge Agents.
- **Streaming Responses:** Supports streaming chat responses for real-time UX.
- **Cloud Native:** Designed for Azure App Service, Container Apps, and local development with Azure Developer CLI authentication.

## Directory Structure

- `main.py` — Entrypoint for the backend app.
- `app.py` — Main Quart app, API routes, and service setup.
- `config.py` — Configuration constants for dependency injection.
- `prepdocs.py` — Document ingestion and processing utilities.
- `approaches/` — Implements retrieval and chat approaches (RAG, agentic, vision, etc).
- `core/` — Core helpers for authentication, session, and image handling.
- `chat_history/` — CosmosDB-based chat history support.
- `prepdocslib/` — Utilities for file parsing, embeddings, and ingestion.

## Prerequisites

- Python 3.11+
- Azure Subscription with Cognitive Search, OpenAI, and Storage resources
- (Optional) Azure CosmosDB for chat history
- Docker (for containerized deployment)

## API Endpoints (Summary)

| Endpoint                | Method | Description                                      |
|-------------------------|--------|--------------------------------------------------|
| `/ask`                  | POST   | Single-turn Q&A                                  |
| `/chat`                 | POST   | Multi-turn chat                                  |
| `/chat/stream`          | POST   | Streaming chat responses                         |
| `/upload`               | POST   | Upload a file (authenticated)                    |
| `/list_uploaded`        | GET    | List user-uploaded files                         |
| `/delete_uploaded`      | POST   | Delete a user-uploaded file                      |
| `/content/<path>`       | GET    | Download content files (authenticated)           |
| `/speech`               | POST   | Text-to-speech (if enabled)                      |
| `/auth_setup`           | GET    | Get authentication config for the frontend       |
| `/config`               | GET    | Get feature flags/config for the frontend        |

## Environment Variables

The backend is configured via environment variables. Key variables include:

- `AZURE_STORAGE_ACCOUNT`, `AZURE_STORAGE_CONTAINER` — Azure Blob Storage for content.
- `AZURE_USERSTORAGE_ACCOUNT`, `AZURE_USERSTORAGE_CONTAINER` — (Optional) User file uploads.
- `AZURE_SEARCH_SERVICE`, `AZURE_SEARCH_INDEX` — Azure Cognitive Search service and index.
- `AZURE_OPENAI_SERVICE`, `AZURE_OPENAI_CHATGPT_MODEL`, etc. — Azure OpenAI configuration.
- `AZURE_TENANT_ID`, `AZURE_CLIENT_ID`, etc. — Azure AD authentication.
- `USE_GPT4V`, `USE_USER_UPLOAD`, `ENABLE_LANGUAGE_PICKER`, etc. — Feature flags.

See `app.py` for the full list and usage.

## Running Locally

1. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```
2. **Set environment variables:**
   Configure the required Azure and feature flags as environment variables.
3. **Start the backend:**
   ```bash
   python main.py
   ```
   Or with Gunicorn (recommended for production):
   ```bash
   gunicorn -b 0.0.0.0:8000 main:app
   ```
4. **Docker:**
   Build and run using the provided `Dockerfile`:
   ```bash
   docker build -t azure-search-backend .
   docker run -p 8000:8000 --env-file .env azure-search-backend
   ```

## Deployment

- Designed for Azure App Service, Azure Container Apps, or any containerized environment.
- Uses managed identity or Azure Developer CLI authentication for secure, passwordless access to Azure resources.

## Troubleshooting & Support

- Check logs for errors (Quart, Gunicorn, or Azure App Service logs)
- Ensure all required environment variables are set
- For Azure authentication, use `azd auth login` for local dev or managed identity in production
- See the [docs/](../../docs/) folder for more guides and troubleshooting tips

## Extending

- Add new approaches in `approaches/` for custom retrieval or chat logic.
- Extend authentication or session logic in `core/`.
- Add new file parsers or ingestion strategies in `prepdocslib/`.

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](../../CONTRIBUTING.md) for guidelines.

## License

See [LICENSE](../../LICENSE) for details.
