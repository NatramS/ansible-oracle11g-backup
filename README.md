# Ansible — Oracle 11g RMAN Backup

**Repository purpose**

This repo contains an Ansible playbook and supporting role to perform RMAN backups of Oracle 11g databases running on Linux compute instances. The playbook creates timestamped backups, packages the backup, uploads to Oracle Cloud Infrastructure (OCI) Object Storage (optional), and rotates local copies.

> README style follows structure and tone used in `NatramS/devops-laboratory-setup`. Reference: https://github.com/NatramS/devops-laboratory-setup. citeturn0view0

---

## Overview

A reliable, repeatable Ansible role that performs:

- RMAN full backup (level 0) of the database
- Controlfile and archived log backups
- Compression into tar.gz
- Optional upload to OCI Object Storage
- Local retention/rotation of backups

This is intended for Oracle 11g (11.2.x) installations on Linux. Test thoroughly in a staging environment before applying to production.

---

## Architecture

- Ansible controller executes `playbooks/oracle11g_backup.yml` against hosts in the `oracle_db` group.
- Each DB host runs RMAN locally under the `oracle` OS user to perform the backup.
- Backups are staged under `{{ backup_base_dir }}` and optionally uploaded to OCI Object Storage.

---

## Prerequisites

1. Oracle database 11g installed and running.
2. Oracle user and environment scripts configured on DB hosts (e.g. `/etc/profile.d/oracle.sh`).
3. RMAN available under `$ORACLE_HOME/bin/rman`.
4. Ansible 2.9+ on the controller.
5. (If using OCI upload) OCI CLI configured on DB host or instance principal enabled.
6. Sufficient disk space under `backup_base_dir`.

---

## Variables (group_vars/oracle_backup.yml)

Key variables you should review and change as needed before running:

- `oracle_user` - OS user that owns Oracle (default: `oracle`)
- `oracle_home` - Oracle home path
- `oracle_sid` - Database SID
- `backup_base_dir` - Base directory for backups on DB host
- `oci_upload` - Whether to upload backup to OCI
- `oci_bucket`, `oci_namespace`, `oci_profile` - OCI details for upload
- `keep_local_copies` - Number of local backups to keep

---

## Usage

1. Update `inventories/hosts.ini` with your DB hosts and SSH connectivity.
2. Edit `group_vars/oracle_backup.yml` to set correct Oracle paths and OCI details.
3. From the controller run:

```bash
ansible-playbook -i inventories/hosts.ini playbooks/oracle11g_backup.yml
