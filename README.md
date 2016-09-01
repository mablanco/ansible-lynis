mablanco.lynis
==============

Ansible role to deploy Lynis, an open source security auditing tool, from either its Git repo or the official tar package. It also schedules a weekly audit whose resulting report is sent to a customizable email account.

I recommend using the *'git'* method as it always installs the latest available version, while only using the *'tar'* method as a fallback in case the target machine doesn't have a git CLI client available.

Role Variables
--------------

- **deploy_method**: Deployment method. Defaults to *'tar'*. Accepted values: *'tar'*, *'git'*, *'pkg'*
- **lynis_home**: Directory where Lynis will be installed. Defaults to *'/opt/lynis'*
- **lynis_url**: URL to fetch the tar archive from. Defaults to *'https://cisofy.com/files'*
- **lynis_version**: Version of the tar archive to fetch. Defaults to *'2.3.3'*
- **lynis_package**: Name of the tar archive. Defaults to *'lynis-{{ lynis_version }}.tar.gz'*
- **lynis_package_checksum**: Checksum of the tar archive to be downloaded. No valid default, look for it at https://cisofy.com/download/lynis/
- **lynis_download_dir**: Local directory where the tar archive will be downloaded to. Defaults to *'/tmp'*
- **lynis_git_repo**: Git repo URL. Defaults to *'https://github.com/CISOfy/lynis'*
- **cron_hour**: Hour of execution of the cron job. Defaults to *'6'*.
- **cron_minute**: Minute of execution of the cron job. Defaults to *'30'*.
- **cron_dow**: Day of week of execution of the cron job. Defaults to *'7'*.
- **report_from**: Sender email address for the weekly audit report. No valid default, you have to fill it in so the cron job doesn't fail.
- **report_to**: Receiver email address for the weekly audit report. No valid default, you have to fill it in so the cron job doesn't fail.
- **tests_to_skip**: Tests to skip in the audit runs. No default, fill it in at your convenience with a list of test codes.

Example Playbook
----------------

Examples of how to use this role, depending on the deployment method of choice:

    - hosts: lynis-tar
      roles:
         - { role: mablanco.lynis, deploy_method: tar }

    - hosts: lynis-git
      roles:
         - { role: mablanco.lynis, deploy_method: git }

You can also use the **deploy_method** variable in your inventory as follows:

    [lynis-tar]
    server01

    [lynis-tar:vars]
    deploy_method=tar

If you want to skip tests that make no sense in your servers, you can assign the **tasks_to_skip** variable with a list of the tests codes in any of the usual places in Ansible. For example, in the *'vars/main.yml'* file:

    ---
    # vars file for mablanco.lynis
    
    tests_to_skip:
      - SSH-7408
      - KRNL-6000
      - HOME-9350

TODO
----

- Add a new deployment method: installing a distro package from the official repos

License
-------

GPLv3

Author Information
------------------

Marco Antonio Blanco <mablanco@correolibre.eu>
