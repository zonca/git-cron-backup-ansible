git based cron backup
=============

Git is very convenient for backing up folders of text files, for example `/var/log`,
on many servers is just a few tens of MB.

Git offers a easy way to setup a offsite incremental backup for free, accessible
via browser and with good search functionality.

This Ansible playbook sets up a remote Ubuntu/Debian server with a hourly cron job
to run the backup operation and push to a remote repository.

## Configure the remote server

* Make sure it runs Ubuntu or Debian
* Make sure it has Python 3 (or 2) preinstalled
* Copy your SSH keys so that you can ssh with no need to type a password
* Configure `sudo` to run without a password so `ansible` can become `root`

## Configure your local machine

Install `ansible`, at least 2.4.0, but it might work with older versions

## Configure the repository

Create a private repository for example on Github or Bitbucket. For example <https://github.com/yourusername/logbackup>
Create a new ssh keypair with `ssh-keygen` on your local machine and add the public key as a **deploy key** in the settings,
make sure to grant write privileges.

## Configure the playbook

* Clone the repository on your local machine
* Rename the `hosts.example` file to `hosts`
* Configure the paramters of the server you would like to configure:
    * `SERVERURL`
    * `private_deploy_key=./id_rsa`, path to the **private** key you created above, it will be copied in `/root/.ssh` on the remote server
    * `git_repository=git@github.com:GITHUBUSERNAME/logbackup`, use the SSH path
    * `ansible_user=SSHUSERNAME`, if you remove this, it will use `ubuntu` as username
    * `folder_to_backup=/var/log`

## Run the playbook

On your local machine, from the repository root folder, run:

    ansible-playbook site.yml

This will install the deploy key on root, initialize the repo in the remote folder and prepare the remote.
**It will not push immediately**.

It will install a script in `/etc/cron.hourly`, within one hour, the backup should automatically run.
