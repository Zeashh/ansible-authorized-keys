# Introduction

  

This simple Ansible playbook can be used to copy authorized SSH keys to remote Linux servers **for a single user**. It cleans up keys that are no longer in use and can use one of several sources for authorized keys:

- Local basic `authorized_keys` file, one key after another **(default)**

- Local YAML file, a list of keys that allows for a more organized approach with support for per-key comments

- Basic `authorized_keys` file stored in an HTTPS Git repository (downloads repository to local machine and deletes it after execution)

- YAML file (formatted the same way as the local one) stored in an HTTPS Git repository

  

It is made in a way that it can be easily integrated with Ansible Tower/AWX.

  

# Use

  

Simply run the playbook by running:

  

``` bash
ansible-playbook -i inventory.ini copy-authorized-keys.yml
```

  

There shouldn't be any issues regarding compatibility, as the playbook uses standard Ansible modules.

  

Example YAML file if in use:

  

``` yaml
authorized_keys:
  - ssh-rsa kdfjhslkhfjlsdkhgjslkghjs user@host
  - ...
```

# Ansible variables

  

When using the terminal utility, variables can be specified in the playbook, or using the `-e` flag. This is where AWX/Tower makes things a little more organized.

  

- General variables:

  

| Name                       | Description                                                                                                                             | Type   | Default                                                  | Required |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ------ | -------------------------------------------------------- | -------- |
| `use_yaml`                 | Whether to read authorized keys from a YAML formatted file or not                                                                       | bool   | False                                                    | No       |
| `use_git`                  | Whether to download the source file from a Git repository or not                                                                        | bool   | False                                                    | No       |
| `authorized_keys_src_dir`  | Path on local machine where the `authorized_keys` file is stored                                                                        | string | Playbook directory or `repo_dest` if defined             | No       |
| `authorized_keys_dest_dir` | Destination path on the remote server for the file containing authorized keys                           | string | `$HOME/.ssh/authorized_keys`                    | No       |
| `file_name`                | Name or full path (when using Git and storing the file in a subdirectory) of the file which contains the source list of authorized keys | String | `authorized_keys` or `authorized_keys.yml` if using YAML | No       | 
  

- Git variables:

| Name               | Description                                                                                                         | Type   | Default     | Required                              |
| ------------------ | ------------------------------------------------------------------------------------------------------------------- | ------ | ----------- | ------------------------------------- |
| `git_url`          | URL to the git repository that contains the authorized keys file **(don't specify the protocol, HTTPS is assumed)** | String |             | Yes (if `use_git` is `True`)          |
| `git_authenticate` | Whether to authenticate to the Git server or not                                                                    | Bool   | False       | No (if repository is public)          |
| `git_username`     | Username to use when authenticating to Git                                                                          | String |             | Yes (if `git_authenticate` is `True`) |
| `git_password`     | Password to use when authenticating to Git                                                                          | String |             | Yes (if `git_authenticate` is `True`) |
| `repo_dest`        | Local directory where Git repository will be cloned                                                                 | String | `/tmp/repo` | No                                    |