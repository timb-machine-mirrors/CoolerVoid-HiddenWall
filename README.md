# HiddenWall
Linux kernel module generator for custom rules with netfilter. (block ports, Hidden mode, rootkit functions etc).
<img align="right" width="240" height="220" src="https://github.com/CoolerVoid/HiddenWall/blob/master/doc/wall.png">
The motivation: on bad situation, attacker can put your iptables/ufw to fall... but if you have HiddenWall, 
the attacker will not find the hidden kernel module that block external access, because have a hook to netfilter on 
kernel land(think like a second layer for firewall).

My beginning purpose at this project is protect my personal server, now is protect the machines of my friends.
When i talk "friends", i say peoples that don't know how to write low level code. Using the HiddenWall you can 
generate your custom kernel module for your firewall configuration. 

The low level programmer can write new templates for modules etc...


First step, understand before run
--

Verify if the kernel is 3.x or 4.x
```
uname -r
```

Clone the repository
```
git clone https://github.com/CoolerVoid/HiddenWall
```

Enter the folder
```
cd HiddenWall/module_generator
```

Edit your firewall rules in directory  rules/server.yaml, the python scripts use that file to generate a new firewall module.

```
$ cat rules/server.yaml
module_name: SandWall
public_ports: 80,443,53
whitelist: 
- machine: 
   ip: 192.168.100.181
   open_ports: 22,21
- machine:
   ip: 192.168.100.22
   open_ports: 22
```

If you want study the static code to generate, look the content at directory "templates".




Second step, generate your module
--

If you want generate a kernel module following your YAML file of rules, follow that command:

```
$ python3 WallGen.py --template template/wall.c -r rules/server.yaml
```
This generate a generic module with rules of server.yaml, if you want to use another template like "hiddenwall.c", the module run on hidden mode.
is not visible to "# lsmod" for example.



Third step, install your module
--

To test module:
```
# cd output; make clean; make
# insmod SandWall
```
The rule of this module is simple, drop all out to in packets, accept ports 80,443 and 53, machine 192*.181 can connect at ports 22 and 21...
if you use nmap at localhost/127.0.0.1 machine you can view the ports open... 

To exit module...

```
# rmmod SandWall
```


Random notes
--

Tested on ubuntu 16 and fedora 29 at kernels "4.x".


References
--

*Wikipedia Netfilter* 
https://en.wikipedia.org/wiki/Netfilter

*Linux Device Drivers* 
http://lwn.net/Kernel/LDD3/

*M0nad's Diamorphine* 
https://github.com/m0nad/Diamorphine/
