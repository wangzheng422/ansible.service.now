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

## Build the aap EE

```bash

subscription-manager repos --list > list

cat list | grep ansible
# .....
# Repo ID:   ansible-automation-platform-2.5-for-rhel-9-x86_64-rpms
# .....

subscription-manager repos --enable "ansible-automation-platform-2.5-for-rhel-9-x86_64-rpms"

dnf install -y /usr/bin/ansible /usr/bin/ansible-builder

git clone https://github.com/wangzheng422/ansible.service.now

cd ansible.service.now/

ansible-builder build -f ee/service.now.env.yml -t quay.io/wangzheng422/qimgs:ansible.service.now-2025.05.30-v01 -vvv

# podman push quay.io/wangzheng422/qimgs:ansible.service.now-2025.05.29-v01

podman push quay.io/wangzheng422/qimgs:ansible.service.now-2025.05.30-v01

```

## how to use with AAP 2.5

Following docs here:
- https://github.com/wangzheng422/docker_env/blob/dev/redhat/ocp4/4.18/2025.05.ansible.service.now.md