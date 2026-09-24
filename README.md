# azure-infra-ansible

Configuration management with Ansible for Azure VMs provisioned by
[azure-infra-terraform](https://github.com/alderichoarau/azure-infra-terraform). Terraform's job
stops at creating the VM; this repo configures what runs on it.

## Analysis

### GitHub

[![CI - Lint](https://github.com/alderichoarau/azure-infra-ansible/actions/workflows/ci.yml/badge.svg)](https://github.com/alderichoarau/azure-infra-ansible/actions/workflows/ci.yml)

### Mirror

[![GitLab CI](https://img.shields.io/gitlab/pipeline-status/alderichoarau%2Fazure-infra-ansible?branch=main&label=GitLab%20CI&logo=gitlab)](https://gitlab.com/alderichoarau/azure-infra-ansible/-/pipelines)
[![Bitbucket Pipelines](https://img.shields.io/bitbucket/pipelines/alderic-hoarau/azure-infra-ansible/main?label=Bitbucket%20CI&logo=bitbucket)](https://bitbucket.org/alderic-hoarau/azure-infra-ansible/pipelines)

## Mirrors

GitHub is the source of truth. This repository is automatically push-mirrored (read-only) to:

- [GitLab](https://gitlab.com/alderichoarau/azure-infra-ansible)
- [Bitbucket](https://bitbucket.org/alderic-hoarau/azure-infra-ansible)

Issues and pull requests should be opened on GitHub.

## What's here

`roles/github_runner/` configures a Ubuntu 22.04 VM as a GitHub Actions self-hosted runner:

- Base hardening: `ufw` (deny incoming by default, SSH allowed only from one CIDR), `fail2ban`,
  `unattended-upgrades`
- Docker (the runner's own jobs may need it)
- Downloads the latest `actions/runner` release, registers it against a target repo, installs it
  as a systemd service

## Usage

The playbook is not chained automatically after a Terraform apply yet -- run it by hand once the VM
exists:

1. In `azure-infra-terraform`, apply `terraform-runner/` (see that repo's README) and note the
   `runner_vm_public_ip` and `runner_ssh_private_key` outputs.
2. Add two secrets on **this** repo (Settings → Secrets and variables → Actions):
   - `RUNNER_SSH_PRIVATE_KEY` -- the `runner_ssh_private_key` output above
   - `GH_RUNNER_REGISTER_PAT` -- a GitHub PAT scoped to `Administration: read/write` on the target
     repo (used only to mint short-lived runner registration tokens at run time, never stored on
     the VM)
   - `RUNNER_ADMIN_IP_CIDR` -- the same CIDR the VM's NSG already restricts SSH to (defense in
     depth: the host firewall enforces it too)
3. Run the **"Run playbook"** workflow (`workflow_dispatch`) with the VM's public IP and the target
   `owner/repo`.
4. Confirm the runner shows up as **Idle** under the target repo's Settings → Actions → Runners.

Re-running the playbook is safe (idempotent) -- it skips runner registration/service install if
already configured, but still re-applies hardening and package updates.

Collection versions used (`community.general`) are pinned in `requirements.yml` for reproducible
runs -- Dependabot doesn't cover Ansible Galaxy, so bump that file by hand when needed.
