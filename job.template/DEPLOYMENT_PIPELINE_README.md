# Ansible Deployment Pipeline

This directory contains a 3-step deployment pipeline for managing ansible playbooks through a git workflow.

## Overview

The pipeline consists of three ansible playbooks that automate the process of:
1. Copying a template to a plan folder with timestamp and creating a new branch
2. Moving the script from plan to production and merging to main branch
3. Executing the production script from the main branch

## Playbooks

### 1. deploy-step-01-template-to-plan.yml
**Purpose**: Copy a template ansible playbook to plan folder with timestamp and push to new branch

**What it does**:
- Copies a template file from `job.template/` to `plan/` folder
- Names the file with timestamp format: `YYYY-MM-DD_HH-MM-SS.yml`
- Creates a new git branch: `template-YYYY-MM-DD_HH-MM-SS`
- Commits and pushes the new branch
- Returns to main branch

**Usage**:
```bash
ansible-playbook job.template/deploy-step-01-template-to-plan.yml \
  --extra-vars "template_to_copy=change-request-create.yml"
```

**Variables**:
- `template_to_copy`: Template file to copy (default: `change-request-create.yml`)

### 2. deploy-step-02-plan-to-prod.yml
**Purpose**: Move ansible script from plan to prod folder and merge to main branch

**What it does**:
- Validates required variables from previous step
- Moves file from `plan/` to `prod/` folder
- Merges the template branch into main
- Pushes changes to remote main branch
- Cleans up the template branch

**Usage**:
```bash
ansible-playbook job.template/deploy-step-02-plan-to-prod.yml \
  --extra-vars "template_branch=template-2024-12-24_15-30-45" \
  --extra-vars "plan_file=plan/2024-12-24_15-30-45.yml" \
  --extra-vars "timestamp=2024-12-24_15-30-45"
```

**Required Variables**:
- `template_branch`: Branch name from step 1
- `plan_file`: Path to file in plan folder
- `timestamp`: Timestamp from step 1

### 3. deploy-step-03-run-prod-script.yml
**Purpose**: Execute the ansible script from prod folder in main branch

**What it does**:
- Ensures execution from main branch
- Validates the production file exists
- Performs syntax check on the playbook
- Executes the production ansible playbook
- Logs execution results
- Commits execution log to git

**Usage**:
```bash
ansible-playbook job.template/deploy-step-03-run-prod-script.yml \
  --extra-vars "prod_file=prod/2024-12-24_15-30-45.yml" \
  --extra-vars "timestamp=2024-12-24_15-30-45"
```

**Optional Variables**:
- `extra_vars`: Additional variables to pass to the executed playbook
- `inventory_file`: Custom inventory file (default: localhost)

## Complete Workflow Example

### Step 1: Template to Plan
```bash
ansible-playbook job.template/deploy-step-01-template-to-plan.yml \
  --extra-vars "template_to_copy=incident-create.yml"
```

### Step 2: Plan to Prod (using output from step 1)
```bash
ansible-playbook job.template/deploy-step-02-plan-to-prod.yml \
  --extra-vars "template_branch=template-2024-12-24_15-30-45" \
  --extra-vars "plan_file=plan/2024-12-24_15-30-45.yml" \
  --extra-vars "timestamp=2024-12-24_15-30-45"
```

### Step 3: Execute Production Script
```bash
ansible-playbook job.template/deploy-step-03-run-prod-script.yml \
  --extra-vars "prod_file=prod/2024-12-24_15-30-45.yml" \
  --extra-vars "timestamp=2024-12-24_15-30-45" \
  --extra-vars "extra_vars='snow_instance=dev12345.service-now.com snow_username=admin'"
```

## Workflow Integration

These playbooks are designed to work together and can be integrated into:
- AWX/Ansible Tower workflows
- GitLab CI/CD pipelines
- Jenkins pipelines
- Manual execution

### AWX Workflow Template

Create a workflow template with three job templates:
1. **Template to Plan** → **Plan to Prod** → **Execute Prod**
2. Use `set_stats` output from each step as input to the next
3. Configure proper credentials and inventories

## Directory Structure

After running the complete pipeline:
```
├── job.template/
│   ├── change-request-create.yml
│   ├── incident-create.yml
│   ├── debug_message_step_01.yml
│   ├── debug_message_step_02.yml
│   ├── deploy-step-01-template-to-plan.yml
│   ├── deploy-step-02-plan-to-prod.yml
│   └── deploy-step-03-run-prod-script.yml
├── plan/
│   └── (temporary files during deployment)
├── prod/
│   └── YYYY-MM-DD_HH-MM-SS.yml
└── logs/
    └── execution-YYYY-MM-DD_HH-MM-SS.log
```

## Error Handling

Each playbook includes:
- Input validation
- File existence checks
- Git operation error handling
- Syntax validation for ansible playbooks
- Detailed error messages

## Security Considerations

- Ensure proper git credentials are configured
- Use ansible vault for sensitive variables
- Restrict access to production branches
- Review all changes before merging to main

## Troubleshooting

### Common Issues

1. **Git authentication errors**: Ensure SSH keys or credentials are properly configured
2. **File not found errors**: Check that template files exist in job.template/
3. **Branch conflicts**: Ensure working directory is clean before running
4. **Syntax errors**: Validate ansible playbook syntax before deployment

### Debug Mode

Add `-vvv` flag for verbose output:
```bash
ansible-playbook job.template/deploy-step-01-template-to-plan.yml -vvv