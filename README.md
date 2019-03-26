# elf-memfd_create

## Summary

This is examples and tools that use memfd_create to run normal ELF binaries on Linux without
touching the filesystem (except /proc)

This is intressting as you can run plain ELF binaries directly from memory without a tmpfs filesystem.

## elf-runner

This application run any static compiled application in memory on server by pipe them over SSH.

```sh
⬢  elf-memfd_create  ./elf-runner.py

▓█████  ██▓      █████▒██▀███   █    ██  ███▄    █  ███▄    █ ▓█████  ██▀███
▓█   ▀ ▓██▒    ▓██   ▒▓██ ▒ ██▒ ██  ▓██▒ ██ ▀█   █  ██ ▀█   █ ▓█   ▀ ▓██ ▒ ██▒
▒███   ▒██░    ▒████ ░▓██ ░▄█ ▒▓██  ▒██░▓██  ▀█ ██▒▓██  ▀█ ██▒▒███   ▓██ ░▄█ ▒
▒▓█  ▄ ▒██░    ░▓█▒  ░▒██▀▀█▄  ▓▓█  ░██░▓██▒  ▐▌██▒▓██▒  ▐▌██▒▒▓█  ▄ ▒██▀▀█▄
░▒████▒░██████▒░▒█░   ░██▓ ▒██▒▒▒█████▓ ▒██░   ▓██░▒██░   ▓██░░▒████▒░██▓ ▒██▒
░░ ▒░ ░░ ▒░▓  ░ ▒ ░   ░ ▒▓ ░▒▓░░▒▓▒ ▒ ▒ ░ ▒░   ▒ ▒ ░ ▒░   ▒ ▒ ░░ ▒░ ░░ ▒▓ ░▒▓░
 ░ ░  ░░ ░ ▒  ░ ░       ░▒ ░ ▒░░░▒░ ░ ░ ░ ░░   ░ ▒░░ ░░   ░ ▒░ ░ ░  ░  ░▒ ░ ▒░
   ░     ░ ░    ░ ░     ░░   ░  ░░░ ░ ░    ░   ░ ░    ░   ░ ░    ░     ░░   ░
   ░  ░    ░  ░          ░        ░              ░          ░    ░  ░   ░
[mikael.kall@kindredgroup.com]

Executes local elf binaries from memory on remote server by pipe them over SSH

Usage: ./elf-runner.py <USERNAME>@<HOST> <command>

EXAMPLE: elrunner.py 'username@127.0.0.1' /usr/local/static/linux/x86_64/nmap 192.168.1.1 -p 80
```

This example the server do not have nmap installed, but the tool automatically sends the nmap binary over SSH
and execute directly from memory without it land on disk. Execution will be obfuscated in process list, only the perl
process will be visible without any arguments so a sysadmin will not know what was executed.   

```sh
⬢  elf-memfd_create  ./elf-runner.py vagrant@127.0.0.1:2222 '/usr/local/static/linux/x86_64/nmap 127.0.0.1 -p 80'
vagrant@127.0.0.1's password:

Starting Nmap 6.49BETA1 ( http://nmap.org ) at 2019-03-26 13:13 GMT
Unable to find nmap-services!  Resorting to /etc/services
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000039s latency).
PORT   STATE  SERVICE
80/tcp closed http

Nmap done: 1 IP address (1 host up) scanned in 0.01 seconds
Cannot find nmap-payloads. UDP payloads are disabled.
```


## elf-shell

This is a interactive interface if you want to execute several commands against same target.
You need to type set command to set each parameter and then type run to run your command.

```sh
set RHOST 127.0.0.1
set RPORT 222
set USERNAME vagrant
set PASSWORD vagrant
run nmap 127.0.0.1 -p 80
```

You can also use the 'l' command in the interface to list all binaries you have in your static folder.

## Prerequisite

Note standard path for your static binaries is in '/usr/local/static/linux/x86_64/' you need
use this path or update the script with the path where your static compiled elf binaries are located.

Example on running the tool with all arguments set.

```sh

   ▄████████  ▄█          ▄████████    ▄████████    ▄█    █▄       ▄████████  ▄█        ▄█
  ███    ███ ███         ███    ███   ███    ███   ███    ███     ███    ███ ███       ███
  ███    █▀  ███         ███    █▀    ███    █▀    ███    ███     ███    █▀  ███       ███
 ▄███▄▄▄     ███        ▄███▄▄▄       ███         ▄███▄▄▄▄███▄▄  ▄███▄▄▄     ███       ███
▀▀███▀▀▀     ███       ▀▀███▀▀▀     ▀███████████ ▀▀███▀▀▀▀███▀  ▀▀███▀▀▀     ███       ███
  ███    █▄  ███         ███                 ███   ███    ███     ███    █▄  ███       ███
  ███    ███ ███▌    ▄   ███           ▄█    ███   ███    ███     ███    ███ ███▌    ▄ ███▌    ▄
  ██████████ █████▄▄██   ███         ▄████████▀    ███    █▀      ██████████ █████▄▄██ █████▄▄██
             ▀                                                               ▀         ▀
  [mikael.kall@kindredgroup.com] -- Runs file in memory by pipe command over ssh.

RHOST: 127.0.0.1
RPORT: 2222
USERNAME: vagrant
PASSWORD: True

elfshell> run nmap 127.0.0.1 -p 80

Starting Nmap 6.49BETA1 ( http://nmap.org ) at 2019-03-26 13:18 GMT
Unable to find nmap-services!  Resorting to /etc/services
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000039s latency).
PORT   STATE  SERVICE
80/tcp closed http

Nmap done: 1 IP address (1 host up) scanned in 0.01 seconds
Cannot find nmap-payloads. UDP payloads are disabled.
```








**This content was created by Kindred Group Security. Please share if you enjoyed!**