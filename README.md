# ansible-github-runner

Platform and architecture independent ansible role for deyploying github-runners.

[![ansible-lint](https://github.com/PortalMario/ansible-github-runner/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/PortalMario/ansible-github-runner/actions/workflows/ansible-lint.yml)

# Usage - Linux
## Requirements
- sudo priviliges on target hosts

### Variables
Fill "repo" and "auth_token" variable at: `./vars/repo.yml`.
```
repo: <owner/repo>
auth_token: <github-auth-token>

# Fill, to add runner as an organization runner
organization: None
org_runner_group: Default

# Comma seperated lables
runner_lables: ansible_deployed
```
- **repo** = must contain the github owner and the github repo      name like this: portalmario/ansible-roles
- **auth_token** = Must be one of [these](https://docs.github.com/en/rest/actions/self-hosted-runners?apiVersion=2022-11-28#create-a-registration-token-for-a-repository--fine-grained-access-tokens) token kinds or a classic PAT. (Note, that [organization runners](https://docs.github.com/en/rest/actions/self-hosted-runners?apiVersion=2022-11-28#create-a-registration-token-for-an-organization) need different token scopes.)
- **organization** (Optional) = Name of the organization, which this runner will be added to. (Repo variable will be ignored)
- **org_runner_group** (Optional) = Name of the organization's runner group, which this runner will be added to. (NOTE: The group needs to exist before adding the runner to it.)
- **runner_lables** (Optional) = Comma sperated list of additional custom lables for the runner. (e.g: `ansible_deployed,test-env`)

### Inventory and Execution
After you defined your [ansible inventory](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html) and a suitable [ansible playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html) (see `example_playbook.yml`) you could use the role like this:

#### Example for localhost:
```
ansible-playbook -i example_inventory example_playbook.yml --connection=local
```
This will add a user (`github-runner`) and a systemd service (`github-runner.service`) for the runner. Files will only be stored at: `/srv/actions-runner`. Runners added by this ansible role will receive the lable: `ansible_deployed` if the "runner_lables" variable is unaltered.

# Remove Runner
To remove the runner, just add `-e "remove_runner=true"` to the end of the "ansible-playbook" command.
```
ansible-playbook -i example_inventory example_playbook.yml --connection=local -e "remove_runner=true"
```
This will fully the runner and all of its files. (Keep in mind that systemd journal logs will persist if runner was a linux system.)

# Sensitive Data
Please keep in mind that both the github auth token (like a classic PAT) and the runner registration token are considered as sensitive data which should not be kept as plaintext within logs or files (like `vars/repo.yml`). The `no_log` option should be enabled on all relevent tasks, which is already done within this role. (However it is not guaranteed that all occurences of sensetive data within logs are covered by this role)

You could also consider using ansible-vault to store your sensitve credentials.

# Limitations
Please check the github issues tab within the repo to see future features. Some can be considered as limitation within the current state of the repo.