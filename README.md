# Shell Configuration Deployer (SCD)
A program used to deploy shell configuration to remote hosts. The program can
be used to automatically deploy your shell configuration to remote hosts before
sshing in to them. This way you can always use your desired shell configuration 
no matter where you are. See `bin/sshops` for an example of how it can be used.

SCD keeps track of which servers have correct shell configuration by keeping
track of the time of deployment as well as a list of programs and files that 
have been installed. Any files that have since changed or been added will be 
redeployed to the server. It can not handle removal of files or programs.

## Usage
Use `scd on the command line to deploy your configuration to the desired host:

`scd -u vagrant -p vagrant -P 2222 127.0.0.1`

Use `~/.scd/config` to specify what programs should be installed on the remote
server and what files should be deployed. Example:
```json
{
    "user": "vagrant",
    "shell": "zsh",
    "ignored_files": [
        ".git",
        ".DS_Store"
    ],
    "files": [
        ".oh-my-zsh",
        ".zshrc",
        ".gitconfig"
    ],
    "programs": [
        "unzip",
        "tree"
    ]
}
```

This configuration will deploy the folder `.oh-my-zsh` and the files `.zshrc` 
and `.gitconfig` placed in ~ on to the remote host and install `unzip` and 
`tree`. It will ignore `.git` folders and `.DS_Store` files and sign on to the
server using the user `vagrant`. It will also install `zsh` and use it as the 
default login shell for the user.

##### NOTE:

Not all Unix systems come with `unzip` already installed. `unzip` is needed by 
SCD to deploy the configuration files. By adding `unzip` to the programs list
you can make sure that it is installed before deploying the files.

## Installation

* Make sure you have python3 installed.
* Clone the repository

`git clone git@github.com:timlindeberg/ShellConfigDeployer.git <SHELL_CONFIG_DEPLOYER>`

* Install scd:

```
cd <SHELL_CONFIG_DEPLOYER>
python3 setup.py install
```

* You can now use `scd` on the command line. When running it for the first
time a default configuration will be created. 

* Edit the configuration file (`~/.scd/config) with your settings

## Configuration options

##### "shell"
Specifies which shell to use on the remote server. If specified, the selected 
shell will be installed together with the other specified programs and the 
default login shell will be changed to this shell. 

##### "programs"
Specifies a list of programs to install on the remote host.

##### "files"
Specifies a list of files and directories to deploy to the remote host. The
files are relative to the home folder and will be deployed to the home folder
of the selected user on the remote host. Example:

`"files": [ ".oh-my-zsh" ]`

Will deploy the folder `~/.oh-my-zsh` to `/home/<USER>/.oh-my-zsh` on the
remote host.

##### "ignored_files"
Specifies a list of files and directories that will be ignored when looking
for files to deploy.

##### "user"
Selects which user to authenticate against the host with. Can also be specified
with the flag `--user` (`-u`) if you use different user names for different 
hosts. The user needs sudo rights in order to install programs.

##### "host"
Selects which host to deploy the configuration to. Normally this would be given
as a command line argument but if you usually connect to the same host you can 
specify it in the config file.

##### "port"
Selects which port to connect through. Can also be specified using the flags 
`--port` (`-P`). Defaults to 22.

## Flags

##### --help
Prints a help message and exits.

##### --port (-P) PORT
Specifies which port to connect to, defaults to 22.

##### --file (-f) PATH
Specifies a path to a file containing the password to use when authenticating 
against the host. This is preferable to passing a password directly using
the `-p` flag since otherwise the password can be seen by other processes.

##### --password (-p) PASSWORD
Specify the password to use for authentication. It is better to use the `-f` flag
to specify password since otherwise the password can be seen by other processes.
Only use this for testing or if you don't care about security.

##### --verbose (-v)
Prints additional information.

##### --user (-u) USER
Specify which user to authenticate with.

##### --clear-status HOST
Clears the status information for a given host.

##### --force
Forces a full redeployment of the configuration, ignoring the status of the 
host.

##### --host-status
Prints the status of all hosts and exits.

##### --print-config
Prints the current configuration and exits.
