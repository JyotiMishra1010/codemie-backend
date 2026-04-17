# Implementation Plan: Add SFTP Data Source for Legacy System Integration (codemie-backend)

## Overview
This plan covers the codemie-backend implementation for the SFTP data source feature. The feature connects to SFTP servers, securely authenticates, downloads CSV files, and processes them for indexing.

## User Story
**As a** CodeMie administrator,
**I want to** configure an SFTP data source in the setup wizard,
**so that** I can index CSV files from legacy systems for AI assistant semantic search.

### Acceptance Criteria
1. SFTP Connection Validator API.
2. Secure Vault Credentials.
3. Background Jobs for Polling (Every 15 min).
4. CSV Parsing & Indexing.
5. Audit Logging.

### Assumptions
- Vault Encryption integration used.
- SFTP Protocol Only.

## Research Findings
### Existing Codebase Patterns
- **Similar Features**: `CodeDatasourceProcessor`, `ConfluenceDatasourceProcessor`.
- **Reusable Components**: `VaultEncryptionService`, existing loader batching logic.
- **Established Patterns**: API routers in `src/codemie/rest_api/routers`, models in `src/codemie/rest_api/models/index.py`.

## Technical Context
- **Repository**: codemie-backend
- **Tech Stack**: Python, FastAPI, paramiko, Elasticsearch.

## API Contracts

```yaml
openapi: 3.0.3
info:
  title: CodeMie SFTP API
  version: 1.0.0
paths:
  /v1/index/knowledge_base/sftp:
    post:
      summary: Create SFTP data source
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/SFTPRequest'
acomponents:
  schemas:
    SFTPRequest:
      type: object
      properties:
        hostname: { type: string }
        port: { type: integer }
        username: { type: string }
        password: { type: string }
        directory_path: { type: string }
        file_pattern: { type: string }
```

## Architecture & Design
### Component Overview
- `SFTPDatasourceProcessor`: Handles lifecycle.
- `SFTPConnector`: paramiko client wrapper.
- `CSVParser`: Processes downloaded CSVs into embeddings.

### Security Considerations
- Encrypt passwords via `VaultEncryptionService`.

## Implementation Phases
### Phase 0: Research & Discovery
- [x] Review existing datasource processors

### Phase 1: Design & Contracts
- [x] Finalize OpenAPI spec

### Phase 2: Implementation
- [ ] Create APIendpoints
- [ ] Implement SFTP connection module
- [ ] Implement CSV parsing pipeline
- [ ] Build background polling job
- [ ] Integrate Vault for credential storage

### Phase 3: Testing & Documentation
- [ ] Unit & Integration tests
- [ ] Update Developer Documentation

## Expected Artifacts
- `plan-backend.md`
- Backend modules and API updates