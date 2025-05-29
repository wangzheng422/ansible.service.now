# ansible.service.now

Ansible Execution Environment for ServiceNow ITSM operations.

## Overview

This execution environment provides the necessary dependencies and collections for working with ServiceNow ITSM through Ansible.

## Files

- `ee/service.now.env.yml` - Main execution environment configuration
- `ee/bindep.txt` - System package dependencies (fixes systemd-python compilation issues)
- `ee/requirements.txt` - Python package dependencies

## Building the Execution Environment

```bash
ansible-builder build -t servicenow-ee:latest -f ee/service.now.env.yml
```

## Included Collections

- `ansible.netcommon` - Network automation utilities
- `servicenow.itsm` - ServiceNow ITSM operations

## Troubleshooting

If you encounter `systemd-python` compilation errors, ensure that:
1. The `bindep.txt` file includes the necessary system dependencies
2. The base image has access to the required development packages

The current configuration includes system dependencies to resolve common compilation issues with Python packages that require system libraries.