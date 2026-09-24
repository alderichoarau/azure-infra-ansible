## Description

<!-- What does this change configure or fix on the runner VM? -->

## Checklist

- [ ] `ansible-playbook playbook.yml --syntax-check` passes
- [ ] `ansible-lint playbook.yml` passes
- [ ] `yamllint .` passes
- [ ] No secret/credential value committed (PAT, SSH key, IP) -- passed via `-e` / GitHub secrets only
- [ ] Tested against the real VM via `run-playbook.yml`, and re-running it stayed idempotent (no
      unexpected `changed` tasks on the second run)

## Impacted tasks/roles

<!-- List of tasks or roles added / modified -->
