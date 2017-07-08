# Vagrant setup files

The Vagrantfile reads servers.yml and instanciates each listed server.   It also writes out an /etc/hosts style file to etc_hosts, we will use this in the Ansible bootstrap play.

An example of setting up your Vagrant env, I keep Vagrant files outside of the Ansible cloned repo:

```
laptop $ mkdir vagrant-creds-as-cattle-feed
laptop $ cd vagrant-creds-as-cattle-feed/
laptop $ curl -sO https://raw.githubusercontent.com/thisdougb/ansible-creds-as-cattle-feed/master/vagrant/Vagrantfile
laptop $ curl -sO https://raw.githubusercontent.com/thisdougb/ansible-creds-as-cattle-feed/master/vagrant/servers.yml
```
Our instances are defined in servers.yml:
```
laptop $ cat servers.yml 
---
domain: example.com
hosts:
  - name: node1
    ip: 172.28.128.15
  - name: node2
    ip: 172.28.128.16
  - name: node3
    ip: 172.28.128.17
```

Now we can bring up our Vagrant instances:
```
laptop $ vagrant up
Bringing machine 'node1' up with 'virtualbox' provider...
Bringing machine 'node2' up with 'virtualbox' provider...
Bringing machine 'node3' up with 'virtualbox' provider...
<snipped>

laptop $ vagrant status
Current machine states:

node1                     running (virtualbox)
node2                     running (virtualbox)
node3                     running (virtualbox)
```
The Vagrantfile script writes out the etc_hosts file, which is accessible on the target host through the shared mounted folder _/vagrant_.   Our Ansible _bootstrap_hosts.yml_ moves etc_hosts to _/etc/hosts_.
```
laptop $ cat etc_hosts 
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.28.128.15	node1	node1.example.com
172.28.128.16	node2	node2.example.com
172.28.128.17	node3	node3.example.com
```
The next step is typically to bootstrap the new instances with the _bootstrap_hosts.yml_ Ansible playbook in the root of the project repo.
