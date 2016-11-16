!SLIDE smbullets
# Git Server Installation

The following exercises will guide you to install
your own minimal Git server with SSH read/write access.

This is required for the next chapters with
collaboration and workflows.

~~~SECTION:handouts~~~

****

~~~ENDSECTION~~~


!SLIDE smbullets
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Generate public SSH key

* Objective:
 * Generate public SSH key
* Steps:
 * Use `ssh-keygen -f $HOME/.ssh/training` to generate an SSH key
 * Configure SSH to use this key for `localhost` in `~/.ssh/config`

~~~SECTION:handouts~~~

****

This key will be used to authorize against localhost's SSH server
in this training session.


~~~ENDSECTION~~~

!SLIDE supplemental exercises
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Generate public SSH key

## Objective: Generate public SSH key
****

* Generate public SSH key

## Steps:

****

* Use `ssh-keygen -f $HOME/.ssh/training` to generate an SSH key
* Configure SSH to use this key for `localhost` in `~/.ssh/config`


!SLIDE supplemental solutions
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Proposed Solution
****

## Generate public SSH key

****

### Generate key

    @@@ Sh
    $ ssh-keygen -f $HOME/.ssh/training

### Configure SSH

    @@@ Sh
    $ vim $HOME/.ssh/config

    Host localhost
        IdentityFile ~/.ssh/training


In case you are using the default `id_rsa` ssh key file, you don't
need this configuration.

!SLIDE smbullets
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Add git user and add SSH key

* Objective:
 * Add git user and add SSH key
* Steps:
 * Use `adduser git`
 * Create `.ssh` directory in his home directory
 * Copy your generated SSH key into `/home/git/.ssh/authorized_keys`
* Bonus:
 * Restrict the git user to `git-shell`

~~~SECTION:handouts~~~

****

This key will be used to authorize against localhost's SSH server
in this training session.


~~~ENDSECTION~~~

!SLIDE supplemental exercises
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Add git user and add SSH key

## Objective: Add git user and add SSH key
****

* Add git user and add SSH key

## Steps:

****

* Use `adduser git`
* Create `.ssh` directory in his home directory
* Copy your generated SSH key into `/home/git/.ssh/authorized_keys`

## Bonus:

****

* Restrict the git user to `git-shell`

!SLIDE supplemental solutions
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Proposed Solution
****

## Add git user and add SSH key

****

### Add git user

    @@@ Sh
    $ sudo adduser git

Change to the newly created user and create the `.ssh` directory:

    $ sudo su - git
    $ cd
    $ mkdir .ssh
    $ touch .ssh/config
    $ chmod 640 .ssh/config
    $ chmod 750 .ssh
    $ exit

### Add your SSH key to the git user

    @@@ Sh
    $ whoami (ensure that you are not `git` but your local `training` user)

    $ sudo cp $HOME/.ssh/training.pub /home/git/.ssh/authorized_keys

Note: This overrides the `authorized_keys` file. Instead you may also copy
the file to /tmp, then become `git` user and update the `authorized_keys` file.
Or you use shell copy paste, depends on your likings.


### Test the connection

Ensure that you are working as your local training user, not `git`.

    @@@ Sh
    $ ssh git@localhost

Exit the test and continue working as your local training user.

    @@@ Sh
    $ exit

### Restrict the git user shell

Ensure that you are working as your local training user.

    @@@ Sh
    $ sudo vim /etc/passwd
   
    git:x:1000:1000::/home/git:/usr/bin/git-shell

Note: Run `which git-shell` to determine the correct path.

### Test the connection again

Ensure that you are working as your local training user, not `git`.

    @@@ Sh
    $ ssh git@localhost

    fatal: Interactive git shell is not enabled.
    Connection to localhost closed.


!SLIDE smbullets
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Create a new repository on the Git Server

* Objective:
 * Create a new repository on the Git Server
* Steps:
 * Create a new repository called `training.git` in `/opt/git`
 * Initialize a bare repository (`git init --bare`)

A bare repository does not have a working directory. This acts
as storage facility on the server, while development happens
in cloned repositories.

~~~SECTION:handouts~~~

****

~~~ENDSECTION~~~

!SLIDE supplemental exercises
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Create a new repository on the Git Server

## Objective: Create a new repository on the Git Server
****

* Create a new repository on the Git Server

## Steps:

****

* Create a new repository called `training.git` in `/opt/git`
* Initialize a bare repository (`git init --bare`)

!SLIDE supplemental solutions
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Proposed Solution
****

## Create a new repository on the Git Server

****

### Create the directory

Become `root` user and create the directory.

    @@@ Sh
    $ sudo -i
    $ mkdir -p /opt/git && cd /opt/git
    $ mkdir training.git

### Initialize the bare Git repository

Continue as `root`user and initialize the bare Git repository.

    @@@ Sh
    $ cd training.git
    $ git init --bare

### Change ownership for git user

Ensure that the `git` user gets ownership of the Git repository storage.

    @@@ Sh
    $ chown -R git:git /opt/git


!SLIDE smbullets
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Add the repository as remote origin

* Objective:
 * Use your local repository and add the `training.git` remote origin
* Steps
 * Navigate into your local repository
 * Use `git remote add origin git@localhost:/opt/git/training.git` to add the origin
 * Push your local history
 * Use `--set-upstream` to enable the local branch following the remote repository
* Bonus
 * Configure the default push method to `simple`


~~~SECTION:handouts~~~

****


~~~ENDSECTION~~~

!SLIDE supplemental exercises
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Add the repository as remote origin

## Objective: Add the repository as remote origin
****

* Add the repository as remote origin

## Steps:

****

* Navigate into your local repository
* Use `git remote add origin git@localhost:/opt/git/training.git` to add the origin
* Push your local history
* Use `--set-upstream` to enable the local branch following the remote repository

## Bonus:

****

* Configure the default push method to `simple`

!SLIDE supplemental solutions
# Lab ~~~SECTION:MAJOR~~~.~~~SECTION:MINOR~~~: Proposed Solution
****

## Add the repository as remote origin

****

### Add the remote origin

    @@@ Sh
    $ cd $HOME/training.git
    $ git remote add origin git@localhost:/opt/git/training.git
    $ git fetch

### Push the history

    @@@ Sh
    $ git push

This will not work since the local branch does not follow the remote branch.
Use `--set-upstream` as proposed by the cli output.

    @@@ Sh
    $ git push --set-upstream origin master

### Set default push method

Git versions prior 2.0 did not define the default push method. The default behaviour
was to use the same local branch name for the remote branch name.

The new default method should be `simple` which ensures that the local branches
will only be pushed to remote branches which `git pull` is following.

Our setup did not clone the repository (which includes a virtual git pull). Therefore
the local master branch does not follow a remote branch.

In order to fix that, add the default push method to your global configuration.

    @@@ Sh
    git config --global push.default simple

