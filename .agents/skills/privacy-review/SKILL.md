---
name: privacy-review
description: Review privacy, filesystem access, data handling, and destructive file operations when relevant changes are made.
---

# Privacy Review

For relevant changes, inspect only affected paths and assess:
- File and directory permissions; least-privilege access.
- Safe parsing, storage, temporary files, and cleanup.
- Whether user confirmation is needed before sensitive or destructive actions.
- Personal data collection, retention, exposure, and logging.
- Data sent to external AI providers, including what files/context leave the machine.
- Access beyond explicitly allowed directories and symlink/path traversal risks.
- File modification/deletion risks, backups, recovery, and scope.

Report concrete findings and severity. Distinguish prompt instructions from enforced OS/runtime sandbox permissions. Never include secrets or real user documents in examples or tests.
