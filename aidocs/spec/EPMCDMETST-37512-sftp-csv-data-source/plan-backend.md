# Implementation Plan: SFTP Data Source with CSV Indexing for RAG (Backend)

## Overview
Implementation plan for SFTP Data Source with CSV Indexing for RAG (Backend).

**Repository**: codemie-backend 
**Jira Ticket**: https://jiraeu.epam.com/browse/EPMCDMETST-37512

## Requirements from Transcript (June 2, 2025)
- Connect to SFTP server (hostname, port 22, username, password)
- Retrieve CSV files from directory_path matching file_pattern
- Parse CSV and index into Elasticsearch for RAG
- Store credentials encrypted via KMS/Vault
- Handle 500 GB initial + 2-3 GB daily incremental loads
- Support scheduler (5 min - 24 hours, default 15 min)
- Audit logging for file transfers and authentication
- Test connection validation

## Components to Implement
1. **SftpDatasourceLoader** - SFTP connection, file retrieval, CSV parsing
2. **SftpDatasourceProcessor** - Orchestration, indexing, scheduler
3. **IndexInfo Model Extension** - Database schema + migration
4. **API Endpoints** - Create, update, test connection
5. **Unit Tests** - Loader and processor tests (>80% coverage)

## API Contracts

### POST /v1/index (Create SFTP Datasource)
```json
{
  "project_name": "my-project",
  "repo_name": "sftp-legacy-data",
  "index_type": "sftp",
  "embeddings_model": "text-embedding-ada-002",
  "sftp": {
    "hostname": "sftp.example.com",
    "port": 22,
    "username": "codemie_user",
    "password": "secure_password",
    "directory_path": "/data/exports",
    "file_pattern": "*.csv"
  },
  "cron_expression": "0 */15 * * *"
}
```

### POST /v1/index/test-connection
```json
{
  "hostname": "sftp.example.com",
  "port": 22,
  "username": "codemie_user",
  "password": "secure_password",
  "directory_path": "/data/exports",
  "file_pattern": "*.csv"
}
```

Response:
```json
{
  "status": "success",
  "message": "Successfully connected to SFTP server",
  "files_count": 150,
  "total_size_bytes": 524288000
}
```

## Security
- Credentials encrypted via KMS/Vault
- Plaintext only in memory during API calls
- RBAC enforced for credential access
- SFTP protocol only (SSH-based, secure)

## Implementation Phases

### Phase 1: SftpDatasourceLoader
- Implement SFTPconnection using paramiko
- Implement lazy_load() for batch file processing
- Implement fetch_remote_stats() for file count
- Add connection timeout and retry logic

### Phase 2: SftpDatasourceProcessor
- Inherit from BaseDatasourceProcessor
- Implement process() for indexing
- Integrate with EncryptionFactory
- Add scheduler integration

### Phase 3: API Endpoints
- Extend POST /v1/index for SFTP type
- Add POST /v1/index/test-connection
- Update PUT/v1/index/{id} for SFTP updates

### Phase 4: Testing
- Unit tests for loader and processor
- Integration tests with test SFTP server
- End-to-end test: Create datasource → Index → Query

## Timeline
- Phase 1: 2 days
- Phase 2: 3 days
- Phase 3: 2 days
- Phase 4: 3 days
- **Total**: ~2 weeks

## Dependencies
- paramiko library for SFTP client
- Existing encryption services (KMS/Vault)
- Elasticsearch indexing pipeline
- Frontend UI (separate implementation)