## What is rvm1-ansible? [![Build Status](https://secure.travis-ci.org/rvm/rvm1-ansible.png)](http://travis-ci.org/rvm/rvm1-ansible)

It is an [Ansible](http://www.ansible.com/home) role to install and manage ruby versions using rvm.

### Why should you use rvm?

In production it's useful because compiling a new version of ruby can easily
take upwards of 10 minutes. That's 10 minutes of your CPU being pegged at 100%.

rvm has pre-compiled binaries for a lot of operating systems. That means you can
install ruby in about 1 minute, even on a slow micro instance.

This role even adds the ruby binaries to your system path when doing a system
wide install. This allows you to access them as if they were installed without
using a version manager while still benefiting from what rvm has to offer.

## Installation

`$ ansible-galaxy install rvm_io.rvm1-ruby`

## Role variables

Below is a list of default values that you can configure:

```
---

# Install 1 or more versions of ruby
rvm1_rubies:
  - 'ruby-2.1.2'

# Delete a specific version of ruby (ie. ruby-2.1.0)
rvm1_delete_ruby:

# Install path for rvm (defaults to system wide)
rvm1_install_path: '/usr/local/lib/rvm'

# Add or remove any install flags
# NOTE: If you are doing a USER BASED INSTALL then
#       make sure you ADD the --user-install flag below
rvm1_install_flags: '--auto-dotfiles'

# Should rvm always be upgraded?
rvm1_rvm_force_upgrade_installer: False

# URLs for the latest installer and version
rvm1_rvm_latest_installer: 'https://raw.githubusercontent.com/wayneeseguin/rvm/master/binscripts/rvm-installer'
rvm1_rvm_stable_version_number: 'https://raw.githubusercontent.com/wayneeseguin/rvm/master/VERSION'

# Time in seconds before re-running apt-get update
# This is only used to download the httplib library so Ansible's URI module works
apt_cache_valid_time: 86400
```

## Example playbook

```
---

- name: Configure servers with ruby support
  hosts: all

  roles:
    - { role: rvm_io.rvm1-ruby, tags: ruby, sudo: True }
```

#### System wide installation

The above example would setup ruby system wide. It's very important that you
run the play with sudo because it will need to write to `/usr/local/lib/rvm`.

#### To the same user as `ansible_ssh_user`

You do not need to include `sudo: True` in this case, just overwrite `rvm_install_path` and set the `--user-install` flag:

```
rvm1_install_flags: '--auto-dotfiles --user-install'
rvm_install_path: '/home/{{ ansible_ssh_user }}/.rvm'
```

#### To a user that is not `ansible_ssh_user`

You **will need sudo here** because you will be writing outside the ansible
user's home directory. Other than that it's the same as above, except you will
supply a different user account:

```
rvm1_install_flags: '--auto-dotfiles --user-install'
rvm_install_path: '/home/someuser/.rvm'
```

## Upgrading and removing old versions of ruby

A common work flow for upgrading your ruby version would be:

1. Install the new version
2. Run your application role so that bundle install re-installs your gems
3. Delete the previous version of ruby

### Leverage ansible's `--extra-vars`

Just add `--extra-vars 'rvm1_delete_ruby=ruby-2.1.0'` to the end of your play book command and that version will be removed.

## Requirements

Tested on ubuntu 12.04 LTS but it should work on other versions that are similar.

## Ansible galaxy

You can find it on the official [ansible galaxy](https://galaxy.ansible.com/list#/roles/1087) if you want to rate it.

## License

MIT
