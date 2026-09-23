# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability (exposed secret, misconfigured permission, insecure default), please **do not open a public issue**.

Report it privately by emailing: **alderic.hoarau@gmail.com**

Include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact

You will receive a response within 48 hours.

## Security practices in this project

- Playbooks never hardcode secrets (SSH private keys, GitHub PATs) -- they're passed in at run time
  from GitHub Actions secrets and written to disk only transiently (temp files, `chmod 600`, never
  committed)
- SSH host key checking is enabled (`ansible.cfg`) -- host keys are pinned on first connect rather
  than disabled outright
- The GitHub PAT used to mint runner registration tokens is scoped to the minimum required
  (`Administration: read/write` on the target repo only), never a broad/classic `repo`-scoped token
  if a fine-grained one covers it
- Target VMs are provisioned by [azure-infra-terraform](https://github.com/alderichoarau/azure-infra-terraform)
  with least-privilege OIDC authentication; this repo only configures what already exists, it never
  provisions infrastructure itself
