# mablanco.lynis

Ansible role to deploy Lynis, an open source security auditing tool, from either its Git repo, the official tar archive or the official Linux packages. It also schedules a weekly audit whose resulting report is sent to a customizable email account.

I recommend using the _'git'_ method as it always installs the latest available version, while only using the _'tar'_ method as a fallback in case the target machine doesn't have a git CLI client available. If you prefer the best integration with your Linux distribution, then you can choose the _'pkg'_ method.

## Role Variables

- **lynis_deploy_method**: Deployment method. Defaults to _'tar'_. Accepted values: _'tar'_, _'git'_, _'pkg'_. Currently supported Linux distros: Debian, Ubuntu, RHEL, Fedora, CentOS, openSuSE and SLES.
- **lynis_home**: Directory where Lynis will be installed. Defaults to _'/opt/lynis'_
- **lynis_url**: URL to fetch the tar archive from. Defaults to _'<https://cisofy.com/files>'_
- **lynis_version**: Version of the tar archive to fetch. Currently defaults to _'2.5.8'_
- **lynis_package**: Name of the tar archive. Defaults to _'lynis-{{ lynis_version }}.tar.gz'_
- **lynis_git_repo**: Git repo URL. Defaults to _'<https://github.com/CISOfy/lynis>'_
- **lynis_cron_hour**: Hour of execution of the cron job. Defaults to _'6'_.
- **lynis_cron_minute**: Minute of execution of the cron job. Defaults to _'30'_.
- **lynis_cron_dow**: Day of week of execution of the cron job. Defaults to _'7'_.
- **lynis_report_from**: Sender email address for the weekly audit report. No valid default.
- **lynis_report_to**: Receiver email address for the weekly audit report. No valid default.
- **lynis_log_expire**: Days before logs are purged. Defaults to _'90'_.
- **lynis_tests_to_skip**: Tests to skip in the audit runs. No default, fill it in at your convenience with a list of test codes.

## Example Playbook

Examples of how to use this role, depending on the deployment method of choice:

```
- hosts: lynis-tar
  roles:
     - { role: mablanco.lynis, lynis_deploy_method: tar }

- hosts: lynis-git
  roles:
     - { role: mablanco.lynis, lynis_deploy_method: git }

- hosts: lynis-pkg
  roles:
     - { role: mablanco.lynis, lynis_deploy_method: pkg }
```

You can also use the **lynis_deploy_method** variable in your inventory as follows:

```
[lynis-tar]
server01

[lynis-tar:vars]
lynis_deploy_method=tar
```

If you want to skip tests that make no sense in your servers, you can assign the **lynis_tests_to_skip** variable with a list of the tests codes in any of the usual places in Ansible. For example, in the _'vars/main.yml'_ file:

```
---
# vars file for mablanco.lynis

lynis_tests_to_skip:
  - SSH-7408
  - KRNL-6000
  - HOME-9350
```

## License

GPLv3
