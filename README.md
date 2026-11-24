# Google Drive Auditor

Privacy-first browser application for recursive Drive inventory, exact duplicate detection and recoverable remediation.

## Deployment

Enable GitHub Pages in **Settings > Pages > Build and deployment > Deploy from a branch**, select **main / (root)** and save. Expected URL: https://psehgaft.github.io/google_drive_auditor/ . Alternatively use the included Pages workflow after selecting GitHub Actions as source.

## Google Cloud setup

1. Create a Google Cloud project and enable Google Drive API.
2. Configure Google Auth Platform branding, audience and test users. For personal testing use an External app in Testing status with your own account as a test user.
3. Create an OAuth **Web application** client (not Desktop).
4. Add the authorized JavaScript origin `https://psehgaft.github.io` (no path). For local testing add `http://localhost:8000`.
5. Configure Drive readonly and Drive scopes. The full Drive scope is restricted and may require verification for broader public use. Never publish a client secret or access token.
6. Paste the client ID and your folder link into the application. Authorize read-only, run audit, review the proposed plan, then authorize remediation and confirm the exact count.

## Safety and privacy

- All session state, tokens and binary buffers reside in browser memory. No backend, analytics, local storage or credentials in Git.
- Google provides OAuth and Drive requests; files are not uploaded to GitHub.
- Size is only a prefilter. SHA-256 groups candidates; before trash, both originals are downloaded and SHA-256 plus direct byte comparison is required.
- Distinct accreditation variants are never treated as duplicates merely because names, text or certificate IDs match.
- Protect shared and non-owned files, preserve a canonical copy, exclude native Workspace files and shortcuts, skip oversized files, check current inventory and version before modification.
- No permanent deletion. Drive Trash retention applies. Avoid concurrent changes: Drive does not provide an atomic compare-and-trash transaction here.
- Sequential bounded processing with retry/backoff and cancellation. Individual file limit defaults to 64 MiB; comparison can use several times that much RAM.
- A closed/reloaded tab loses session state; export reports before leaving. If token expires, reconnect and rerun audit or retry remaining work after review. Do not assume a failed request means no change; reconcile with a fresh audit.
- Audit errors and protected files require manual review. No semantic duplicate detection, issuer classification or automatic renaming is implemented in this version.

## Reports

Export JSON for full inventory, plan, hashes, events and errors; CSV for file inventory. Reports contain private names and identifiers: do not commit them to this repository.

## Local API

`window.DriveAuditor.audit()`, `apply()`, `summary()`, `report()`, `stop()` are available in the same browser tab. Write remediation still requires OAuth and explicit confirmation. This is not a remote HTTP API and cannot be directly invoked from ChatGPT through the GitHub connector. A future authenticated MCP service would require a separate secure runtime and explicit user authorization; do not relay OAuth tokens through GitHub issues or chat.

## Local development

```sh
python3 -m http.server 8000
```

Open http://localhost:8000 and use the configured web client ID. Live OAuth and Drive remediation must be tested with disposable files before use on production data.
