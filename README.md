# mablanco.lynis

[![CI](https://github.com/mablanco/ansible-lynis/actions/workflows/ci.yml/badge.svg)](https://github.com/mablanco/ansible-lynis/actions/workflows/ci.yml)

Ansible role to deploy [Lynis](https://cisofy.com/lynis/), an open source security auditing tool, and schedule a weekly audit whose report is mailed to an address of your choice.

## Supported platforms

Debian 12 and 13, Ubuntu 24.04 and 26.04, EL 9 and 10, and openSUSE Leap 16.0, all tested in CI with every installation method.

On openSUSE, the role needs the `community.general` collection (`ansible-galaxy collection install community.general`).

The hosts need a working `mail` command and mail transport agent for the reports to be delivered.

## Installation methods

Set **lynis_deploy_method** to one of these:

| Method | Installs | Version |
|---|---|---|
| `tar` (default) | GitHub's tarball of the release tag, checked against its SHA-256 sum, in `lynis_home` | `lynis_version` |
| `git` | A clone of the release tag from GitHub, in `lynis_home` | `lynis_version` |
| `pkg` | The package from [CISOfy's official repository](https://packages.cisofy.com/) | The latest release |
| `distro` | The distribution's own package (from EPEL on EL) | Depends on the distribution, and can be several releases behind |

`tar` and `git` stay on the version you set until you change it. `pkg` follows CISOfy's releases with the rest of your system updates.

On EL, the `distro` method installs `epel-release` on Rocky Linux, AlmaLinux and other rebuilds. On RHEL, enable EPEL before running it.

## Role variables

- **lynis_deploy_method**: installation method, see above. Defaults to `tar`.
- **lynis_home**: installation directory for the `tar` and `git` methods. Defaults to `/opt/lynis`.
- **lynis_version**: version for the `tar` and `git` methods. Defaults to `3.1.7`.
- **lynis_tar_checksum**: checksum of the tarball, as `sha256:<hash>`. Update it together with `lynis_version`. CISOfy's own tarball is signed with GPG, but `downloads.cisofy.com` rejects Ansible's downloads (HTTP 403), so the role uses GitHub's tarball of the release tag. Before you trust the checksum of a new version, you can compare that tarball with the signed one from `https://downloads.cisofy.com/lynis/`.
- **lynis_url**: where to download the tarball from. Defaults to `https://github.com/CISOfy/lynis/archive/refs/tags`.
- **lynis_package**: name of the tarball at that URL. Defaults to `{{ lynis_version }}.tar.gz`.
- **lynis_git_repo**: Git repository for the `git` method. Defaults to `https://github.com/CISOfy/lynis`.
- **lynis_report_from**: sender address of the weekly report. Required.
- **lynis_report_to**: receiver address of the weekly report. Required.
- **lynis_cron_hour**, **lynis_cron_minute**, **lynis_cron_dow**: when the weekly audit runs. Default to `6`, `30` and `7` (Sunday at 06:30).
- **lynis_log_expire**: days before old reports in `/var/log/lynis` are deleted. Defaults to `90`.
- **lynis_tests_to_skip**: list of Lynis test codes to skip, written to `/etc/lynis/custom.prf`. Defaults to none.

## Example playbook

```yaml
- hosts: servers
  vars:
    lynis_deploy_method: pkg
    lynis_report_from: sender@example.com
    lynis_report_to: receiver@example.com
    lynis_tests_to_skip:
      - SSH-7408
      - KRNL-6000
  roles:
    - role: mablanco.lynis
```

## Upgrading from older versions

- The role works again with current Ansible: older versions used `include`, which ansible-core 2.16 removed.
- **git** now clones the tag of `lynis_version` instead of following `master`, and the weekly job no longer runs `git pull`. Change `lynis_version` to upgrade.
- **pkg** now uses CISOfy's `stable` suite, signed with the key in `/etc/apt/keyrings` instead of `apt-key`. The role removes the repository that older versions added for the release codename, which CISOfy no longer publishes and which makes `apt-get update` fail.
- **distro** is new: older versions only had CISOfy's repository.
- The weekly report is sent as the body of the mail, so it no longer depends on mail client options to attach it.
- The weekly job keeps the machine-readable report with `--report-file` in `/var/log/lynis`.
- Fedora and SUSE Linux Enterprise are no longer tested.

## Development

The role is tested with [Molecule](https://ansible.readthedocs.io/projects/molecule/) in Docker containers of every supported platform, and linted with [ansible-lint](https://ansible.readthedocs.io/projects/lint/). CI runs both on every change and every week.

```bash
pip install -r requirements-dev.txt
ansible-lint
LYNIS_DEPLOY_METHOD=pkg molecule test   # tar, git, pkg or distro
```

## License

GPLv3
