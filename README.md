# [Common Ansible Role][1]

[![test & release][2]][3]

This role does two things. Firstly, it installs a few packages that are required
to run some essential Ansible modules, but are sometimes absent from managed
nodes. Secondly it manages, and can create, the agent user (`ansible_user`).
The role can not bootstrap a host on its own though. If used to create the
permanent `ansible_user` it requires a privileged "bootstrap" user (which can
optionally be removed as part of this role) and a python executable on the
target machine.

## Requirements

None.

## Role Variables

<table>
<tr><th>Name</th><th>Required</th><th>Type / Choices</th><th>Description</th></tr>
<tr><td><code>common_agent_user_name</code></td>
<td>no</td>
<td>string</td>
<td>

Name of the managed user. During normal operations this should be the same as
the `ansible_user` for the host.

**Default:** `"ansible"`
</td></tr>


<tr><td><code>common_agent_user_home</code></td>
<td>no</td>
<td>path</td>
<td>

Home directory of the managed user.

**Default:** `"/var/ansible"`
</td></tr>


<tr><td><code>common_agent_user_shell</code></td>
<td>no</td>
<td>path</td>
<td>

Login shell for the managed user.

**Default:** `"/bin/bash"`
</td></tr>


<tr><td><code>common_agent_user_create_local</code></td>
<td>no</td>
<td>boolean</td>
<td>

Use the [`local`][5] option during user creation. This requires the `luseradd`
command to be present on the target host. This role will install this command
if possible, but some Linux distributions (eg. ArchLinux) do not have an install
target for the `luseradd` executable in their package sources.

**Default:** `false`
</td></tr>


<tr><td><code>common_bootstrap_user_name</code></td>
<td>no</td>
<td>string</td>
<td>

Name of a dedicated bootstrap user that WILL BE REMOVED as part of this role.
This variable has no effect if the given user does not exist on the system.

The idea is, that during the bootstrapping process ansible can log in to the new
target host using a throwaway user that is set up with unsave credentials during
system installation. This role will than create the permanent agent user, switch
over to that new user for the rest of the play, and then, finally, remove the
temporary user.

**Default:** `"ansible_bootstrap"`
</td></tr>


<tr><td><code>common_remove_unused_sshd</code></td>
<td>no</td>
<td>boolean</td>
<td>

Remove the openssh-server package if the ansible connection does not require it.
This is option is currently only considered for the `lxd` connection type.

**Default:** `true`
</td></tr>


<tr><td><code>common_configure_connection</code></td>
<td>no</td>
<td>`"lxd"`, `"ssh"`</td>
<td>

By default this role will do some custom configuration (if applicable) for the
connection type used during the playbook run. If another connection plugin
should be configured instead, this variable can be used to overwrite it.

**Default:** `{{ ansible_connection }}`
</td></tr>
</table>


## Dependencies

None.

## Example Playbooks

```yaml
- hosts: all
  tasks:
    - ansible.builtin.import_role:
        name: gliech.common
```

## License

This project is licensed under the terms of the [GNU General Public License v3.0](LICENSE)

[1]: https://galaxy.ansible.com/ui/standalone/roles/gliech/common/
[2]: https://github.com/gliech/common-ansible-role/actions/workflows/release.yml/badge.svg
[3]: https://github.com/gliech/common-ansible-role/actions/workflows/release.yml
[4]: https://github.com/gliech/semantic-release-config-github-ansible-role
[5]: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html#parameter-local
