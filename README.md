# ansible-user-management

Ansible User management
===============
Configure users, group, SSH keys and administrative sudo access. The role can:
* Add and remove groups
* Add and remove users
* Install SSH keys for users
* Configure sudo to allow root access for the administration group

Requirements
------------

It will work with:
* All Ubuntu
* All Debian
* All Redhat

Role Variables
--------------

### Users management playbook:

ansible-playbook -i hosts playbooks/manage_users.yaml -vvvv

### Users management playbook inventory file:

host
[test_instance]
ip of instance where playbook will be deployed

* manage_users role
A list containing dictionaries of configuration. Each list item can have the
following elements. *Italic text* indicates the default.

  * name **required**
  * full_name (*Unknown user*)
  * state (*present*)
  * uid
  * group (*item.name*)
  * groups
  * shell (*{{ users_default_shell }}*)
  * password (*{{ users_default_password }}*)
  * home (*/home/{{item.name}}*)
  * createhome (*{{ users_create_homedirs }}*)
  * system
  * is_admin

All other variable defaults are given below.

*is_admin* is a boolean. If set, the user is added to the OSs admin group.

All other items correspond to the ansible users module parameters.

#### Example

```yaml
users:
  - name: nowshad
    full_name: Nowshad Ibne Amin
    is_admin: true
    uid: 1001
    groups:
      - 'users'
      - 'ubuntu'
      - 'bin'
      - 'devs'
    ssh_key:
      - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDvv9z60YR1YE4Z2OsPrpQMlGttU7ZXOeKwPXPEYAkmpa1LHlvR+Bzg2SkmGm6RJiYQn+UWIiHqqASQ/2IcCnE1+JeYny/5Rxn7rkPFAydNc9Me24ssdetLGhOdwhQ6EwuRr3T5jieVQO8lb6bcoXglbblRUN9+oUVg8o2upkex0IFI2uYqzIyQTWMnGENUbV5rbih0fkkT+QAgILhEyv9DR2/Vs2AC7abGIBcDtk3tYTAI332Cd7RbUf2A49FXPdz+RPj+mJGelse1ok9qf3dzWJQ+fckvXuh+e+/RpgV/ldRnfbAxcnqW/ErZZclaMGsiGMay2fhD9YkcQDc66g3C70wtKT2EmILRqNlmtkPGrD14nME+FN5cj4vImYGb5PzMY0jfwAJSbuOEu5WAQFDAsOp7NiHSgc/gPhkQNiuxY6FR+nrS+goQV4iXdq3pdRiP2uf3SVGlZ06txBVeQpv4EexgF+51uH9ppHRX+gnZ59oxpGgBhzIQa7p/yL7feoo95aC6/BEh39jkplfTzJSy6v8VImyyBbo1xyXmXyg4GojfUlq0i2nf9Lk79ffv7xms9NlRK6nrTY9/ROalXeaZokR2seBqVAj0ozDT+RL7d1luW/qgljVfFr+S+kb0XRcBusZzrZS9JLfpahUKxgI7fmn/lsnoAWevJU33XjkV8w== your_email@example.com"
```

### Groups

* users_groups

A list of user groups to create. Each list item can have the following elements.
*Italic text* indicates the default.

  * name **required**
  * state (*present*)
  * system
  * gid

#### Example
  
```yaml
users_groups:
  - name: devs
    gid: 1003789
  - name: ops
    gid: 10104
```

### Tuning configuration

* users_default_shell

The default shell for a user if none is specified. Defaults to `/bin/bash`

* users_create_homedirs

Create home dirs for new users? Set this to false if you manage home directories
in some other way. Defaults to *true*.

* users_per_user_groups

Create groups for users with the same name as the users group. Defaults to
*true*

### Sudo configuration

* users_manage_admin_sudoers

If true, create sudo configuration to allow users in the admin group to become
root via sudo. Defaults to *true*

* users_admin_uses_ansible

If true, assume the admin group is also used to run Ansible jobs. This disables
requiretty for the admin group in the sudoers configuration. Defaults to *true*

* users_admin_sudo_password

If true require a password for sudo, false to not require a password. Defaults
to *true*

### Ansible Task Profiling

Usage
^^^^^

Make a directory called `callback_plugins` next to your playbook and put `profile_tasks.py` inside of it.

.. code-block:: bash

    mkdir callback_plugins
    cd callback_plugins
    wget https://raw.githubusercontent.com/jlafon/ansible-profile/master/callback_plugins/profile_tasks.py

Usage
^^^^^

Make a directory called `callback_plugins` next to your playbook and put `profile_tasks.py` inside of it.

.. code-block:: bash

    mkdir callback_plugins
    cd callback_plugins
    wget https://raw.githubusercontent.com/jlafon/ansible-profile/master/callback_plugins/profile_tasks.py

Now, run your playbook just as you normally would!

.. code-block:: bash

   ansible <args here>
   <normal output here>
   PLAY RECAP ********************************************************************
   manage_users | Create groups for each user with their name -------------- 7.17s
   manage_users | Create users --------------------------------------------- 6.78s
   sudo-permission | Admin sudoers ----------------------------------------- 4.59s
   manage_users | Create groups -------------------------------------------- 4.09s
   manage_users | SSH keys ------------------------------------------------- 3.83s
   sudo-permission | Sudo installed ---------------------------------------- 2.47s
   manage_users | Add admin users to admin group --------------------------- 2.44s
   manage_users | Get OS specific values ----------------------------------- 0.02s
   Playbook finished: Sat Nov 14 18:41:10 2015, 8 total tasks.  0:00:31 elapsed.

If for some reason you want to disable this temporarily, set the
environment variable `ANSIBLE_PROFILE_DISABLE` to any value (even an
empty string).

