### Users and Groups in Linux

##### Users:

A user is anyone who uses a Linux system. Users can be people, accounts associated with running programs, or accounts for running system processes.

Each user is identified by a unique numerical identification number called a user ID (UID).

##### Groups:

A group is a collection of users.

Groups are used to organize users and to manage permissions and access to files and directories.
Each group also has a unique numerical identification number called a `group ID (GID)`.

##### Relationship Between Users and Groups

Users are members of groups.
Every user is a member of at least one group, known as their primary group.

Users can also belong to additional groups, known as secondary groups.
File and directory permissions in Linux can be set separately for the `owner (user), the owner’s group, and others`.

Files for User and Group Information

`/etc/passwd:`

This file contains user account information.
Each line in this file represents a user account and has various fields separated by colons, including the `username, UID, GID, user information (like the full name), home directory, and shell``.

`/etc/group:``

This file contains group account information.
Similar to `/etc/passwd`, each line represents one group.
The format includes the `group name, GID,` and a list of the members of the group.

##### Viewing User-Group Mappings

To see a user’s UID and groups, you can use the id command followed by the username. For example:

```bash
id username
```

This will display the user's `UID, their primary GID, and any secondary groups`` they belong to.

<br/>

## Extra tutorial to update user:

### Creating New user in ubuntu & setting up hadoop

1. Create a New Group for Hadoop:

```
sudo addgroup hadoop_
```

**Purpose:** This command creates a new group named hadoop\_. Groups are used to organize and administer user accounts in Linux.
Usage: Run this command to create a group that will contain users associated with Hadoop operations.

2. Create a New User and Add to the Hadoop Group:

```
sudo adduser --ingroup hadoop_ hduser_
```

**Purpose:** This command creates a new user hduser* and adds them to the `hadoop* group`.

`Usage:` Replace hduser\_ with the desired username. This user will be used for managing Hadoop services.

3. Add the New User to the sudo Group:

```
sudo adduser hduser_ sudo
```

**Purpose:** This command adds hduser\_ to the sudo group, granting them administrative privileges.
**Usage:** Essential for tasks that require elevated permissions, especially for system configurations and Hadoop management.

4. Switch to the New User Account:

```
su hduser_
```

**Purpose:** Switches the current session to the new user hduser*.
**Usage:** Used to perform operations as hduser* rather than the current logged-in user.

5. Generate SSH Keys:

```
ssh_keygen -t rsa -P ""
```

**Purpose:** Generates a new SSH key pair of type RSA.
**Usage:** -t rsa specifies the type of key to create (RSA), and -P "" creates it without a passphrase. SSH keys are used for secure communication between machines.

6. View the Generated SSH Public Key:

```
cat $HOME/.ssh/id_rsa.pub
```

**Purpose:** Displays the content of the public SSH key.
**Usage:** Run this command to view the generated public SSH key, which is used to set up secure communication with other systems.

7. Add SSH Public Key to Authorized Keys:

```bash
cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
```

**Purpose:** Appends the public key to the list of authorized keys.
**Usage:**This step is crucial for enabling SSH access to the hduser\_ account without needing a password.

8. Navigate to the Home Directory of the New User:

```bash
cd /home/hduser_
```

**Purpose:** Changes the current directory to the home directory of `hduser_`.

**Usage:** Used to perform operations or access files in the hduser\_'s home directory.

9. Test SSH Setup by Connecting to localhost:

```bash
ssh localhost
```

**Purpose:** Tests the SSH setup by attempting to connect to your own machine.

**Usage:** This step verifies that SSH is correctly set up for `hduser_`. It should log in without asking for a password, indicating that SSH key-based authentication is working.

#### Changing ownership and giving permission to a directory:

The command `sudo chown -R hduser_:hadoop_ hadoop` is used in a Linux environment to change the ownership of files and directories.


### Creating ssh key pairs 

```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa:
```

**ssh-keygen:** This is a tool for creating new authentication key pairs for SSH (Secure Shell). SSH keys are a way to identify trusted computers, without involving passwords.

**-t rsa:** This specifies the type of key to create, in this case, RSA, which is a widely used public-key algorithm.

**-P '':** This sets an empty passphrase for the key. Normally, a passphrase is used as an added layer of security. An empty passphrase means the key can be used without entering a passphrase.

**-f ~/.ssh/id_rsa:** This designates the file where the generated key pair will be saved. ~/.ssh/id_rsa is the standard location for the private key on the user's machine.
```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys:
```

**cat ~/.ssh/id_rsa.pub:** This displays the content of your public key `(id_rsa.pub)`.

**~/.ssh/authorized_keys**: This appends the public key to the authorized_keys file. This file stores all public keys that are allowed to access this account.

Essentially, this step adds your public key to the list of keys authorized to access your account on this machine. This is a necessary step for allowing passwordless login via SSH.

```bash
chmod 0600 ~/.ssh/authorized_keys:
```

**chmod 0600:** This command changes the file permissions of authorized_keys.
**0600** sets the permissions so that only the user (owner) can read and write the file, and no one else can read, write, or execute it.

This step is crucial for security. The authorized_keys file should be readable and writable only by the owner to prevent unauthorized modifications.

**Why are these commands needed?**

These commands are part of setting up SSH keys for secure, passwordless access to a server. SSH keys are more secure than password-based authentication due to their cryptographic strength.
Passwordless authentication simplifies automated processes by removing the need to manually enter a password.
It's a standard practice in server management and is used extensively for tasks like automated file transfers, remote server management, and running commands on remote machines.

