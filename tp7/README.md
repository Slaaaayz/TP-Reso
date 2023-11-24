# TP7 : Do u secure
## 1. Fingerprint
### B. Manip

**🌞 Effectuez une connexion SSH en vérifiant le fingerprint**

```bash
PS C:\Users\melb3> ssh slayz@10.7.1.11
The authenticity of host '10.7.1.11 (10.7.1.11)' can't be established.
ED25519 key fingerprint is SHA256:f49nzRoDxA/WtCf14jVptxcuRrgZrUCAcVTmbZ3VMz4.
This host key is known by the following other names/addresses:
    C:\Users\melb3/.ssh/known_hosts:16: 10.3.1.12
    C:\Users\melb3/.ssh/known_hosts:19: 10.3.1.11
    C:\Users\melb3/.ssh/known_hosts:30: 10.6.1.101
    C:\Users\melb3/.ssh/known_hosts:31: 10.6.1.13
    C:\Users\melb3/.ssh/known_hosts:32: 10.6.1.102
    C:\Users\melb3/.ssh/known_hosts:33: 10.6.1.254
    C:\Users\melb3/.ssh/known_hosts:34: 10.6.1.103
    C:\Users\melb3/.ssh/known_hosts:35: 10.7.1.254
Are you sure you want to continue connecting (yes/no/[fingerprint])?    
```

```bash
[slayz@john ~]$ sudo ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key
[sudo] password for slayz:
256 SHA256:f49nzRoDxA/WtCf14jVptxcuRrgZrUCAcVTmbZ3VMz4 /etc/ssh/ssh_host_ed25519_key.pub (ED25519)
```
## 2. Conf serveur SSH
**🌞 Modifier la configuration du serveur SSH**

```bash
[slayz@router ~]$ sudo ss -alntpu
Netid  State   Recv-Q  Send-Q   Local Address:Port   Peer Address:Port  Process
tcp    LISTEN  0       128            0.0.0.0:22          0.0.0.0:*      users:(("sshd",pid=682,fd=3))
```

**🌞 Modifier la configuration du serveur SSH**
```bash
[slayz@router ~]$ sudo cat /etc/ssh/sshd_config
#       $OpenBSD: sshd_config,v 1.104 2021/07/02 05:11:21 dtucker Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

# To modify the system-wide sshd configuration, create a  *.conf  file under
#  /etc/ssh/sshd_config.d/  which will be automatically included below
Include /etc/ssh/sshd_config.d/*.conf

# If you want to change the port on a SELinux system, you have to tell
# SELinux about this change.
# semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
#
Port 22222
#AddressFamily any
ListenAddress 10.7.1.254
#ListenAddress ::

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
#LogLevel INFO

# Authentication:

#LoginGraceTime 2m
#PermitRootLogin prohibit-password
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10

#PubkeyAuthentication yes

# The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2
# but this is overridden so installations will only check .ssh/authorized_keys
AuthorizedKeysFile      .ssh/authorized_keys

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no

# Change to no to disable s/key passwords
#KbdInteractiveAuthentication yes

# Kerberos options
#KerberosAuthentication no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes
#KerberosGetAFSToken no
#KerberosUseKuserok yes

# GSSAPI options
#GSSAPIAuthentication no
#GSSAPICleanupCredentials yes
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no
#GSSAPIEnablek5users no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the KbdInteractiveAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via KbdInteractiveAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and KbdInteractiveAuthentication to 'no'.
# WARNING: 'UsePAM no' is not supported in RHEL and may cause several
# problems.
#UsePAM no

#AllowAgentForwarding yes
#AllowTcpForwarding yes
#GatewayPorts no
#X11Forwarding no
#X11DisplayOffset 10
#X11UseLocalhost yes
#PermitTTY yes
#PrintMotd yes
#PrintLastLog yes
#TCPKeepAlive yes
#PermitUserEnvironment no
#Compression delayed
#ClientAliveInterval 0
#ClientAliveCountMax 3
#UseDNS no
#PidFile /var/run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none

# override default of no subsystems
Subsystem       sftp    /usr/libexec/openssh/sftp-server

# Example of overriding settings on a per-user basis
#Match User anoncvs
#       X11Forwarding no
#       AllowTcpForwarding no
#       PermitTTY no
#       ForceCommand cvs server
```

**🌞 Prouvez que le changement a pris effet**
```bash
[slayz@router ~]$ sudo ss -alntpu
Netid  State   Recv-Q  Send-Q   Local Address:Port    Peer Address:Port Process
tcp    LISTEN  0       128         10.7.1.254:22222        0.0.0.0:*     users:(("sshd",pid=2052,fd=3))
```

**🌞 Effectuer une connexion SSH sur le nouveau port**
```bash
[slayz@router ~]$ sudo firewall-cmd --add-port=22222/tcp --permanent
success
[slayz@router ~]$ sudo firewall-cmd --reload
success
[slayz@router ~]$ exit
logout
Connection to 10.7.1.254 closed.
PS C:\Users\melb3> ssh slayz@router.tp7.b1 -p 22222
The authenticity of host '[router.tp7.b1]:22222 ([10.7.1.254]:22222)' can't be established.
ED25519 key fingerprint is SHA256:f49nzRoDxA/WtCf14jVptxcuRrgZrUCAcVTmbZ3VMz4.
This host key is known by the following other names/addresses:
    C:\Users\melb3/.ssh/known_hosts:16: 10.3.1.12
    C:\Users\melb3/.ssh/known_hosts:19: 10.3.1.11
    C:\Users\melb3/.ssh/known_hosts:30: 10.6.1.101
    C:\Users\melb3/.ssh/known_hosts:31: 10.6.1.13
    C:\Users\melb3/.ssh/known_hosts:32: 10.6.1.102
    C:\Users\melb3/.ssh/known_hosts:33: 10.6.1.254
    C:\Users\melb3/.ssh/known_hosts:34: 10.6.1.103
    C:\Users\melb3/.ssh/known_hosts:35: 10.7.1.254
    (1 additional names omitted)
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[router.tp7.b1]:22222' (ED25519) to the list of known hosts.
slayz@router.tp7.b1's password:
Last login: Thu Nov 23 14:27:29 2023 from 10.7.1.1
[slayz@router ~]$
```
## 3. Connexion par clé 

**🌞 Générer une paire de clés**
```bash
PS C:\Users\melb3> ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\melb3/.ssh/id_rsa):
C:\Users\melb3/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\melb3/.ssh/id_rsa
Your public key has been saved in C:\Users\melb3/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:N9QaZLSxQ+KCUqH1bZiwMEzy1nOaS7NwIYxpbp3M/IY melb3@Maxime
The key's randomart image is:
+---[RSA 4096]----+
|.o+ =.  ..*      |
| *.B = = = =     |
|o.B * * + * .    |
|o.=o.* o . +     |
| o.**   S +      |
|.  +o+   . .     |
|   Eoo           |
|    .            |
|                 |
+----[SHA256]-----+
```

```bash
melb3@Maxime MINGW64 ~
$ ssh-copy-id slayz@10.7.1.11
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/melb3/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
slayz@10.7.1.11's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slayz@10.7.1.11'"
and check to make sure that only the key(s) you wanted were added.


```

```bash
PS C:\Users\melb3> ssh slayz@10.7.1.11
Last login: Thu Nov 23 15:13:45 2023 from 10.7.1.1
[slayz@john ~]$
```

## C. Changement de fingerprint
**🌞 Supprimer les clés sur la machine router.tp7.b1**

```bash
[slayz@router ssh]$ sudo rm /etc/ssh/ssh_host_*
[sudo] password for slayz:
[slayz@router ssh]$ ls
moduli  ssh_config  ssh_config.d  sshd_config  sshd_config.d
```
**🌞 Regénérez les clés sur la machine router.tp7.b1**
```bash
[slayz@router ssh]$ sudo ssh-keygen -A
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519
[slayz@router ssh]$ ls
moduli         ssh_host_dsa_key        ssh_host_ed25519_key.pub
ssh_config     ssh_host_dsa_key.pub    ssh_host_rsa_key
ssh_config.d   ssh_host_ecdsa_key      ssh_host_rsa_key.pub
sshd_config    ssh_host_ecdsa_key.pub
sshd_config.d  ssh_host_ed25519_key
```
```bash
PS C:\Users\melb3> ssh slayz@router.tp7.b1 -p 22222
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:DKjIPsCN8SWPK2oNxP5FEL+Mv2l3R+++kovu0XN/Abs.
Please contact your system administrator.
Add correct host key in C:\\Users\\melb3/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in C:\\Users\\melb3/.ssh/known_hosts:37
Host key for [router.tp7.b1]:22222 has changed and you have requested strict checking.
Host key verification failed.
PS C:\Users\melb3> ssh slayz@router.tp7.b1 -p 22222
The authenticity of host '[router.tp7.b1]:22222 ([10.7.1.254]:22222)' can't be established.
ED25519 key fingerprint is SHA256:DKjIPsCN8SWPK2oNxP5FEL+Mv2l3R+++kovu0XN/Abs.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

```bash
[slayz@web ~]$ sudo ss -alntpu
Netid  State   Recv-Q  Send-Q   Local Address:Port   Peer Address:Port  Process
tcp    LISTEN  0       511            0.0.0.0:80          0.0.0.0:*      users:(("nginx",pid=1709,fd=6),("nginx",pid=1708,fd=6))
```

```bash
[slayz@web ~]$ openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout server.key -out server.crt
....+...+.+.........+..+.........+.+...+......+...........+.+..+.+...............+.....+.+.....+......+..........+...+.........+...+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*........................+...+.....+.+......+.....+....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.......+.+......+.....+.+...+...........+.+..+...+.......+...............+..+....+.........+..+.+..+..........+......+..+.......+........+...+....+...+...+......+........+...+...+.......+.........+......+...+...........+.+...+..+............+...+............+......+.+....................+.+....................+...+....+..+.+........+.....................+...+.+.....+................+...+...+........+............+.......+...+..+..................+.......+........+....+...........+...+.+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
.+.+...+........+...+......+...+.......+.....+.+........+...............+....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*......+........+.......+...+......+...+..+...+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+....+..+.......+..+...+......+...............+......+...+.......+.........+......+...+..+.............+..................+..+.......+...........+....+........+..........+.....+......+.......+.....+...+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:
State or Province Name (full name) []:
Locality Name (eg, city) [Default City]:
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:
Email Address []:
[slayz@web ~]$
[slayz@web ~]$
[slayz@web ~]$ sudo mv server.key /etc/pki/tls/private/web.tp7.b1.key
[slayz@web ~]$ sudo mv server.crt /etc/pki/tls/certs/web.tp7.b1.crt
[slayz@web ~]$ sudo chown nginx:nginx /etc/pki/tls/private/web.tp7.b1.key
[slayz@web ~]$ sudo chown nginx:nginx /etc/pki/tls/certs/web.tp7.b1.crt
[slayz@web ~]$ sudo chmod 0400 /etc/pki/tls/private/web.tp7.b1.key
[slayz@web ~]$ sudo chmod 0444 /etc/pki/tls/certs/web.tp7.b1.crt
```