mablanco.lynis
==============

Role to deploy Lynis, an open source security auditing tool, from either its Git repo or the official tar package. It also schedules a weekly audit whose resulting report is sent to a customizable email account.

I recommend using the *'git'* method as it always installs the latest available version, while only using the *'tar'* method as a fallback in case the target machine doesn't have a git CLI client available.

Role Variables
--------------

- **lynis_url**: URL to fetch the tar file from. Defaults to *'https://cisofy.com/files'*
- **lynis_version**: Version of the tar package to fetch. Defaults to *'2.2.0'*
- **lynis_package**: Name of the tar file. Defaults to *'lynis-{{ lynis_version }}.tar.gz'*
- **lynis_download_dir**: Local directory where the tar file will be downloaded to. Defaults to *'/tmp'*
- **lynis_home**: Directory where Lynis will be installed. Defaults to *'/opt/lynis'*
- **lynis_git_repo**: Git repo URL. Defaults to *'https://github.com/CISOfy/lynis'*
- **deploy_method**: Deployment method. Defaults to *'tar'*
- **report_from**: sender email address for the weekly audit report. No valid default, you have to fill it in so the cron job doesn't fail.
- **report_to**: receiver email address for the weekly audit report. No valid default, you have to fill it in so the cron job doesn't fail.
- **test_to_skip**: Tests to skip in the audit runs. No default, fill it in at your convenience.

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

TODO
----

- Add a new deployment method: installing a distro package from the official repos

License
-------

GPLv3

Author Information
------------------

Marco Antonio Blanco
