# ArchiveSafe Master Index
**Owner:** Janell  
**Purpose:** Operator-grade index of all sensitive, archival, and project-critical assets.  
**Last Updated:** <fill in date>

---

## 1. Vault (Critical Security Zone)
Location: `ArchiveSafe/Vault/`

### 1.1 keys/
Purpose: Centralized storage for all API keys, passwords, encrypted secrets, and .asc files.  
Contents:
- cloudflare-api.asc
- cloudflare-api.txt.asc
- cloudflare-login.txt.asc
- cloudflare-origin-ca.txt.asc
- privatekey-backup.asc
- privatekey.asc.asc
- revoke.asc.asc
- master.md.asc
- pubkey.asc
- pass.txt
- test.asc
- test.txt.asc

### 1.2 credentials/
Purpose: Login files, tokens, session exports, auth configs.  
Contents:
- (add as discovered)

### 1.3 identity/
Purpose: GPG keys, SSH keys, certificates, identity material.  
Contents:
- (add as discovered)

### 1.4 backups/
Purpose: Encrypted backups, vault-backup, historical snapshots.  
Contents:
- vault-backup/

### 1.5 metadata/
Purpose: Checksums, manifests, integrity logs, audit trails.  
Contents:
- (future: checksum logs, manifest.json)

---

## 2. Personal
Location: `ArchiveSafe/Personal/`

### 2.1 SheetMusic/
Purpose: Personal sheet music and encrypted music archives.  
Contents:
- SheetMusic/
- SheetMusic.zip

### 2.2 Documents/
Purpose: Personal documents, PDFs, notes.  
Contents:
- (add as discovered)

---

## 3. Projects
Location: `ArchiveSafe/Projects/`

### 3.1 FAFO/
Purpose: Scripts, modules, launcher, enrichment pipelines.  
Contents:
- (add as discovered)

### 3.2 OSINT/
Purpose: OSINT tools, results, configs.  
Contents:
- (add as discovered)

### 3.3 Tools/
Purpose: Utility scripts, installers, wrappers.  
Contents:
- (add as discovered)

---

## 4. Archives
Location: `ArchiveSafe/Archives/`

### 4.1 zip/
Purpose: ZIP archives, encrypted bundles.  
Contents:
- (add as discovered)

### 4.2 tar/
Purpose: TAR archives, compressed tool backups.  
Contents:
- (add as discovered)

### 4.3 old_versions/
Purpose: Deprecated versions of tools, configs, or documents.  
Contents:
- (add as discovered)

---

## 5. Pending Ingest
Location: `ArchiveSafe/incoming/` (optional)

Purpose: Temporary holding area for new sensitive files before classification.  
Contents:
- (add as discovered)

---

## 6. Notes
- All key files are **copied**, not moved, to avoid breaking active services.
- Vault/keys/ is the authoritative source for sensitive material.
- Future automation: secure-ingest script, checksum generation, manifest builder.
