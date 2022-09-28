Ansible Role: Docker Stack Deploy
=================================

An Ansible role that deploys a Stack to a Docker Swarm Cluster.

Requirements
------------

- a Docker Swarm cluster

    In order to get docker installed on a remote host, take a look at the [mirceanton.docker](https://github.com/mirceanton/ansible_role-docker) role.

    To get a basic single-node Swarm cluster going, it is as simple as adding the following task to your playbook:

    ``` yaml
    - name: Initialize The Docker Swarm
      ansible.builtin.command:
        cmd: docker swarm init
    ```

- The following python packages are required on the remote machine:
  - setuptools
  - requests
  - docker
  - jsondiff
  - pyyaml

Role Variables
--------------

- `docker_stack_deploy_name`:  
  **Value Type**: string  
  **Default value**: `example`  
  **Description**:  
    The name of the stack to deploy.  
    The argument that would have been passed to the docker stack deploy command.

- `docker_stack_deploy_state`:  
  **Value Type**: string  
  **Default value**: `present`  
  **Description**:  
    The state of the stack to deploy.  
    Generally, this should be set to `present`.  
    Set it to `absent` to do a docker stack rm.

- `docker_stack_deploy_root_dir_path`:  
  **Value Type**: string  
  **Default value**: `/opt/example`  
  **Description**:  
    The path of the root data directory of the application.  
    > NOTE: The role will expect the docker-compose.yml file to be here

- `docker_stack_deploy_root_dir_state`:  
  **Value Type**: string  
  **Default value**: `directory`  
  **Description**:  
    The state of the root directory.  
    Generally, this should be set to `directory`.  
    Set it to `absent` to delete it.

- `docker_stack_deploy_root_dir_owner` / `docker_stack_deploy_root_dir_group`:  
  **Value Type**: string  
  **Default value**: `root`  
  **Description**:  
    The user and group owners of the root dir  
    Equivalent of the arguments passed to `chown`

- `docker_stack_deploy_root_dir_mode`:  
  **Value Type**: string  
  **Default value**: `0777`  
  **Description**:  
    The permissions of the root data directory of the application.  
    Equivalent to the arguments passed to `chmod`

- `docker_stack_deploy_additional_dirs`:  
  **Value Type**: `list(object)`  
  **Default value**: `[]`  
  **Description**:  
    Any additional directories that might need to be created.  
    Object structure:
  - path:
    - Required: true
    - Value Type: `string`
    - Default Value: N/A
    - Description: The path is relative to the docker_stack_deploy_root_dir_path
  - mode:
    - Required: false
    - Value Type: `string`
    - Default Value: "0777
    - Description: The permissions of the directory as passed to `chmod`
  - owner:
    - Required: false
    - Value Type: `string`
    - Default Value: "0777
    - Description: The user owner of the directory as passed to `chown`
  - group:
    - Required: false
    - Value Type: `string`
    - Default Value: "0777
    - Description: The group owner of the directory as passed to `chown`

- `docker_stack_deploy_copy_files` / `docker_stack_deploy_template_files`:  
  **Value Type**: `list(object)`  
  **Default value**: `[]`  
  **Description**:  
    List of additional files that might need to be copied/templated over to the remote host.  
    Object structure:
  - src:
    - Required: true
    - Value Type: `string`
    - Default Value: N/A
    - Description: The source path on the local machine.
  - dest:
    - Required: true
    - Value Type: `string`
    - Default Value: N/A
    - Description: The destination path on the remote machine, relative to docker_stack_deploy_root_dir_path.
  - mode:
    - Required: false
    - Value Type: `string`
    - Default Value: "0777
    - Description: The permissions of the file as passed to `chmod`
  - owner:
    - Required: false
    - Value Type: `string`
    - Default Value: "0777
    - Description: The user owner of the file as passed to `chown`
  - group:
    - Required: false
    - Value Type: `string`
    - Default Value: "0777
    - Description: The group owner of the file as passed to `chown`

Dependencies
------------

This role requires the `community.docker` collection to be installed.

Example Playbook
----------------

For the associated files and variables needed, take a look at the following files:

- `files/test-file.html`
- `templates/test-file.yml.j2`
- `vars/main.yml`

The `converge` playbook in the `molecule/default` directory uses this role to deploy a basic nginx stack to a single node swarm cluster.

``` yaml
---
- name: Deploy application
  hosts: all
  gather_facts: true

  tasks:
    - name: "Include mirceanton.docker_stack_deploy"
      include_role:
        name: "mirceanton.docker_stack_deploy"

```

License
-------

MIT

Author Information
------------------

A role developed by [Mircea-Pavel ANTON](https://www.mirceanton.com).
