# Generating a new key

```
ssh-keygen -t ed25519
```

# Specifying a file

```
ssh-keygen -f <filename> -t ed25519
```

# Removing a host from known host
Example:

```
ssh-keygen -f "/home/gmj/.ssh/known_hosts" -R "[172.17.0.2]:830"
```

# SSH login without password

Automatic login from **Host A / User a** to **Host B / User b**

```
a@A:~> ssh-keygen -t ed25519
```

Now use ssh to create a directory `~/.ssh` as user b on B. (The directory may already exist, which is fine):

```
a@A:~> ssh b@B mkdir -p .ssh
```

Finally append a's new public key to `b@B:.ssh/authorized_keys` and enter b's password one last time:

```
a@A:~> cat .ssh/id_rsa.pub | ssh b@B 'cat >> .ssh/authorized_keys'
```

# SSHFS

```
sshfs -o allow_other,default_permissions <user>@<host>:<remote_dir> <local_dir>
```

```
sshfs -o allow_other,default_permissions,IdentityFile=<absolute_path_to_key> <user>@<host>:<remote_dir> <local_dir>
```

## FSTAB

```
sshfs#USER@HOST:REMOTE_PATH LOCAL_PATH fuse defaults,allow_other 0 0
```
