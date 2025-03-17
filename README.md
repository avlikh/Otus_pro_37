# OTUS PRO Homework 37 VLAN and LACP

## Домашняя работа 37: Строим бонды и вланы

### Домашнее задание:
Описание домашнего задания:

**1. Подготовка рабочего места**

**2. В Office1 в тестовой подсети появляется сервера с доп интерфейсами и адресами в internal сети testLAN:**    
* testClient1 - 10.10.10.254
* testClient2 - 10.10.10.254
* testServer1- 10.10.10.1
* testServer2- 10.10.10.1    
    
**Равести вланами:**
* testClient1 <-> testServer1
* testClient2 <-> testServer2

Между centralRouter и inetRouter "пробросить" 2 линка (общая inernal сеть) и объединить их в бонд, проверить работу c отключением интерфейсов.

---
### 1. Подготовка рабочего места:
Выполнение домашнего задания предполагает, что на компьютере установлен Vagrant+VirtualBox   
**[Как установить Vagrant на Debian 12](https://github.com/avlikh/Install_Vagrant_Debian12/blob/main/README.md)**   

Развернем Vagrant-стенд:
  - Создайте папку с проектом и зайдите в нее (например: /opt/otus/vlan-lacp):
```
mkdir -p /opt/otus/vlan-lacp ; cd /opt/otus/vlan-lacp
```
  - Клонируете проект с Github, набрав команду:
```
apt update -y && apt install git -y ; git clone https://github.com/avlikh/Otus_pro_37.git .
```
  - Запустите проект из папки, в которую склонировали проект (в нашем примере /opt/otus/vlan-lacp):
```
vagrant up
```
<details>
<summary> Результатом выполнения команды vagrant up станут созданные и настроенные 7 виртуальных машин: </summary>

```
root@deb4likh:/opt/otus/vlan-lacp# vagrant up
Bringing machine 'inetRouter' up with 'virtualbox' provider...
Bringing machine 'centralRouter' up with 'virtualbox' provider...
Bringing machine 'office1Router' up with 'virtualbox' provider...
Bringing machine 'testClient1' up with 'virtualbox' provider...
Bringing machine 'testServer1' up with 'virtualbox' provider...
Bringing machine 'testClient2' up with 'virtualbox' provider...
Bringing machine 'testServer2' up with 'virtualbox' provider...
==> inetRouter: Importing base box 'centos/stream9'...
==> inetRouter: Matching MAC address for NAT networking...
==> inetRouter: Checking if box 'centos/stream9' version '20250310.0' is up to date...
==> inetRouter: Setting the name of the VM: vlan-lacp_inetRouter_1742219564182_27246
==> inetRouter: Fixed port collision for 22 => 2222. Now on port 2200.
==> inetRouter: Clearing any previously set network interfaces...
==> inetRouter: Preparing network interfaces based on configuration...
    inetRouter: Adapter 1: nat
    inetRouter: Adapter 2: intnet
    inetRouter: Adapter 3: intnet
    inetRouter: Adapter 8: hostonly
==> inetRouter: Forwarding ports...
    inetRouter: 22 (guest) => 2200 (host) (adapter 1)
==> inetRouter: Running 'pre-boot' VM customizations...
==> inetRouter: Booting VM...
==> inetRouter: Waiting for machine to boot. This may take a few minutes...
    inetRouter: SSH address: 127.0.0.1:2200
    inetRouter: SSH username: vagrant
    inetRouter: SSH auth method: private key
    inetRouter:
    inetRouter: Vagrant insecure key detected. Vagrant will automatically replace
    inetRouter: this with a newly generated keypair for better security.
    inetRouter:
    inetRouter: Inserting generated public key within guest...
    inetRouter: Removing insecure key from the guest if it's present...
    inetRouter: Key inserted! Disconnecting and reconnecting using new SSH key...
==> inetRouter: Machine booted and ready!
==> inetRouter: Checking for guest additions in VM...
    inetRouter: No guest additions were detected on the base box for this VM! Guest
    inetRouter: additions are required for forwarded ports, shared folders, host only
    inetRouter: networking, and more. If SSH fails on this machine, please install
    inetRouter: the guest additions and repackage the box to continue.
    inetRouter:
    inetRouter: This is not an error message; everything may continue to work properly,
    inetRouter: in which case you may ignore this message.
==> inetRouter: Setting hostname...
==> inetRouter: Configuring and enabling network interfaces...
==> inetRouter: Rsyncing folder: /opt/otus/vlan-lacp/ => /vagrant
==> centralRouter: Importing base box 'centos/stream9'...
==> centralRouter: Matching MAC address for NAT networking...
==> centralRouter: Checking if box 'centos/stream9' version '20250310.0' is up to date...
==> centralRouter: Setting the name of the VM: vlan-lacp_centralRouter_1742219624713_5099
==> centralRouter: Fixed port collision for 22 => 2222. Now on port 2201.
==> centralRouter: Clearing any previously set network interfaces...
==> centralRouter: Preparing network interfaces based on configuration...
    centralRouter: Adapter 1: nat
    centralRouter: Adapter 2: intnet
    centralRouter: Adapter 3: intnet
    centralRouter: Adapter 6: intnet
    centralRouter: Adapter 8: hostonly
==> centralRouter: Forwarding ports...
    centralRouter: 22 (guest) => 2201 (host) (adapter 1)
==> centralRouter: Running 'pre-boot' VM customizations...
==> centralRouter: Booting VM...
==> centralRouter: Waiting for machine to boot. This may take a few minutes...
    centralRouter: SSH address: 127.0.0.1:2201
    centralRouter: SSH username: vagrant
    centralRouter: SSH auth method: private key
    centralRouter:
    centralRouter: Vagrant insecure key detected. Vagrant will automatically replace
    centralRouter: this with a newly generated keypair for better security.
    centralRouter:
    centralRouter: Inserting generated public key within guest...
    centralRouter: Removing insecure key from the guest if it's present...
    centralRouter: Key inserted! Disconnecting and reconnecting using new SSH key...
==> centralRouter: Machine booted and ready!
==> centralRouter: Checking for guest additions in VM...
    centralRouter: No guest additions were detected on the base box for this VM! Guest
    centralRouter: additions are required for forwarded ports, shared folders, host only
    centralRouter: networking, and more. If SSH fails on this machine, please install
    centralRouter: the guest additions and repackage the box to continue.
    centralRouter:
    centralRouter: This is not an error message; everything may continue to work properly,
    centralRouter: in which case you may ignore this message.
==> centralRouter: Setting hostname...
==> centralRouter: Configuring and enabling network interfaces...
==> centralRouter: Rsyncing folder: /opt/otus/vlan-lacp/ => /vagrant
==> office1Router: Importing base box 'centos/stream9'...
==> office1Router: Matching MAC address for NAT networking...
==> office1Router: Checking if box 'centos/stream9' version '20250310.0' is up to date...
==> office1Router: Setting the name of the VM: vlan-lacp_office1Router_1742219685583_20967
==> office1Router: Fixed port collision for 22 => 2222. Now on port 2202.
==> office1Router: Clearing any previously set network interfaces...
==> office1Router: Preparing network interfaces based on configuration...
    office1Router: Adapter 1: nat
    office1Router: Adapter 2: intnet
    office1Router: Adapter 3: intnet
    office1Router: Adapter 4: intnet
    office1Router: Adapter 5: intnet
    office1Router: Adapter 6: intnet
    office1Router: Adapter 8: hostonly
==> office1Router: Forwarding ports...
    office1Router: 22 (guest) => 2202 (host) (adapter 1)
==> office1Router: Running 'pre-boot' VM customizations...
==> office1Router: Booting VM...
==> office1Router: Waiting for machine to boot. This may take a few minutes...
    office1Router: SSH address: 127.0.0.1:2202
    office1Router: SSH username: vagrant
    office1Router: SSH auth method: private key
    office1Router:
    office1Router: Vagrant insecure key detected. Vagrant will automatically replace
    office1Router: this with a newly generated keypair for better security.
    office1Router:
    office1Router: Inserting generated public key within guest...
    office1Router: Removing insecure key from the guest if it's present...
    office1Router: Key inserted! Disconnecting and reconnecting using new SSH key...
==> office1Router: Machine booted and ready!
==> office1Router: Checking for guest additions in VM...
    office1Router: No guest additions were detected on the base box for this VM! Guest
    office1Router: additions are required for forwarded ports, shared folders, host only
    office1Router: networking, and more. If SSH fails on this machine, please install
    office1Router: the guest additions and repackage the box to continue.
    office1Router:
    office1Router: This is not an error message; everything may continue to work properly,
    office1Router: in which case you may ignore this message.
==> office1Router: Setting hostname...
==> office1Router: Configuring and enabling network interfaces...
==> office1Router: Rsyncing folder: /opt/otus/vlan-lacp/ => /vagrant
==> testClient1: Importing base box 'centos/stream9'...
==> testClient1: Matching MAC address for NAT networking...
==> testClient1: Checking if box 'centos/stream9' version '20250310.0' is up to date...
==> testClient1: Setting the name of the VM: vlan-lacp_testClient1_1742219747486_56127
==> testClient1: Fixed port collision for 22 => 2222. Now on port 2203.
==> testClient1: Clearing any previously set network interfaces...
==> testClient1: Preparing network interfaces based on configuration...
    testClient1: Adapter 1: nat
    testClient1: Adapter 2: intnet
    testClient1: Adapter 8: hostonly
==> testClient1: Forwarding ports...
    testClient1: 22 (guest) => 2203 (host) (adapter 1)
==> testClient1: Running 'pre-boot' VM customizations...
==> testClient1: Booting VM...
==> testClient1: Waiting for machine to boot. This may take a few minutes...
    testClient1: SSH address: 127.0.0.1:2203
    testClient1: SSH username: vagrant
    testClient1: SSH auth method: private key
    testClient1:
    testClient1: Vagrant insecure key detected. Vagrant will automatically replace
    testClient1: this with a newly generated keypair for better security.
    testClient1:
    testClient1: Inserting generated public key within guest...
    testClient1: Removing insecure key from the guest if it's present...
    testClient1: Key inserted! Disconnecting and reconnecting using new SSH key...
==> testClient1: Machine booted and ready!
==> testClient1: Checking for guest additions in VM...
    testClient1: No guest additions were detected on the base box for this VM! Guest
    testClient1: additions are required for forwarded ports, shared folders, host only
    testClient1: networking, and more. If SSH fails on this machine, please install
    testClient1: the guest additions and repackage the box to continue.
    testClient1:
    testClient1: This is not an error message; everything may continue to work properly,
    testClient1: in which case you may ignore this message.
==> testClient1: Setting hostname...
==> testClient1: Configuring and enabling network interfaces...
==> testClient1: Rsyncing folder: /opt/otus/vlan-lacp/ => /vagrant
==> testServer1: Importing base box 'centos/stream9'...
==> testServer1: Matching MAC address for NAT networking...
==> testServer1: Checking if box 'centos/stream9' version '20250310.0' is up to date...
==> testServer1: Setting the name of the VM: vlan-lacp_testServer1_1742219803946_79430
==> testServer1: Fixed port collision for 22 => 2222. Now on port 2204.
==> testServer1: Clearing any previously set network interfaces...
==> testServer1: Preparing network interfaces based on configuration...
    testServer1: Adapter 1: nat
    testServer1: Adapter 2: intnet
    testServer1: Adapter 8: hostonly
==> testServer1: Forwarding ports...
    testServer1: 22 (guest) => 2204 (host) (adapter 1)
==> testServer1: Running 'pre-boot' VM customizations...
==> testServer1: Booting VM...
==> testServer1: Waiting for machine to boot. This may take a few minutes...
    testServer1: SSH address: 127.0.0.1:2204
    testServer1: SSH username: vagrant
    testServer1: SSH auth method: private key
    testServer1:
    testServer1: Vagrant insecure key detected. Vagrant will automatically replace
    testServer1: this with a newly generated keypair for better security.
    testServer1:
    testServer1: Inserting generated public key within guest...
    testServer1: Removing insecure key from the guest if it's present...
    testServer1: Key inserted! Disconnecting and reconnecting using new SSH key...
==> testServer1: Machine booted and ready!
==> testServer1: Checking for guest additions in VM...
    testServer1: No guest additions were detected on the base box for this VM! Guest
    testServer1: additions are required for forwarded ports, shared folders, host only
    testServer1: networking, and more. If SSH fails on this machine, please install
    testServer1: the guest additions and repackage the box to continue.
    testServer1:
    testServer1: This is not an error message; everything may continue to work properly,
    testServer1: in which case you may ignore this message.
==> testServer1: Setting hostname...
==> testServer1: Configuring and enabling network interfaces...
==> testServer1: Rsyncing folder: /opt/otus/vlan-lacp/ => /vagrant
==> testClient2: Importing base box 'ubuntu/jammy64'...
==> testClient2: Matching MAC address for NAT networking...
==> testClient2: Checking if box 'ubuntu/jammy64' version '20241002.0.0' is up to date...
==> testClient2: Setting the name of the VM: vlan-lacp_testClient2_1742219865607_14232
==> testClient2: Fixed port collision for 22 => 2222. Now on port 2205.
==> testClient2: Clearing any previously set network interfaces...
==> testClient2: Preparing network interfaces based on configuration...
    testClient2: Adapter 1: nat
    testClient2: Adapter 2: intnet
    testClient2: Adapter 8: hostonly
==> testClient2: Forwarding ports...
    testClient2: 22 (guest) => 2205 (host) (adapter 1)
==> testClient2: Running 'pre-boot' VM customizations...
==> testClient2: Booting VM...
==> testClient2: Waiting for machine to boot. This may take a few minutes...
    testClient2: SSH address: 127.0.0.1:2205
    testClient2: SSH username: vagrant
    testClient2: SSH auth method: private key
    testClient2: Warning: Connection reset. Retrying...
    testClient2: Warning: Remote connection disconnect. Retrying...
    testClient2:
    testClient2: Vagrant insecure key detected. Vagrant will automatically replace
    testClient2: this with a newly generated keypair for better security.
    testClient2:
    testClient2: Inserting generated public key within guest...
    testClient2: Removing insecure key from the guest if it's present...
    testClient2: Key inserted! Disconnecting and reconnecting using new SSH key...
==> testClient2: Machine booted and ready!
==> testClient2: Checking for guest additions in VM...
    testClient2: The guest additions on this VM do not match the installed version of
    testClient2: VirtualBox! In most cases this is fine, but in rare cases it can
    testClient2: prevent things such as shared folders from working properly. If you see
    testClient2: shared folder errors, please make sure the guest additions within the
    testClient2: virtual machine match the version of VirtualBox you have installed on
    testClient2: your host and reload your VM.
    testClient2:
    testClient2: Guest Additions Version: 6.0.0 r127566
    testClient2: VirtualBox Version: 7.1
==> testClient2: Setting hostname...
==> testClient2: Configuring and enabling network interfaces...
==> testClient2: Mounting shared folders...
    testClient2: /opt/otus/vlan-lacp => /vagrant
==> testServer2: Importing base box 'ubuntu/jammy64'...
==> testServer2: Matching MAC address for NAT networking...
==> testServer2: Checking if box 'ubuntu/jammy64' version '20241002.0.0' is up to date...
==> testServer2: Setting the name of the VM: vlan-lacp_testServer2_1742219926046_33501
==> testServer2: Fixed port collision for 22 => 2222. Now on port 2206.
==> testServer2: Clearing any previously set network interfaces...
==> testServer2: Preparing network interfaces based on configuration...
    testServer2: Adapter 1: nat
    testServer2: Adapter 2: intnet
    testServer2: Adapter 8: hostonly
==> testServer2: Forwarding ports...
    testServer2: 22 (guest) => 2206 (host) (adapter 1)
==> testServer2: Running 'pre-boot' VM customizations...
==> testServer2: Booting VM...
==> testServer2: Waiting for machine to boot. This may take a few minutes...
    testServer2: SSH address: 127.0.0.1:2206
    testServer2: SSH username: vagrant
    testServer2: SSH auth method: private key
    testServer2: Warning: Connection reset. Retrying...
    testServer2: Warning: Remote connection disconnect. Retrying...
    testServer2:
    testServer2: Vagrant insecure key detected. Vagrant will automatically replace
    testServer2: this with a newly generated keypair for better security.
    testServer2:
    testServer2: Inserting generated public key within guest...
    testServer2: Removing insecure key from the guest if it's present...
    testServer2: Key inserted! Disconnecting and reconnecting using new SSH key...
==> testServer2: Machine booted and ready!
==> testServer2: Checking for guest additions in VM...
    testServer2: The guest additions on this VM do not match the installed version of
    testServer2: VirtualBox! In most cases this is fine, but in rare cases it can
    testServer2: prevent things such as shared folders from working properly. If you see
    testServer2: shared folder errors, please make sure the guest additions within the
    testServer2: virtual machine match the version of VirtualBox you have installed on
    testServer2: your host and reload your VM.
    testServer2:
    testServer2: Guest Additions Version: 6.0.0 r127566
    testServer2: VirtualBox Version: 7.1
==> testServer2: Setting hostname...
==> testServer2: Configuring and enabling network interfaces...
==> testServer2: Mounting shared folders...
    testServer2: /opt/otus/vlan-lacp => /vagrant
==> testServer2: Running provisioner: ansible...
    testServer2: Running ansible-playbook...

PLAY [Base set up] *************************************************************

TASK [Gathering Facts] *********************************************************
ok: [testServer1]
ok: [testClient1]
ok: [centralRouter]
ok: [inetRouter]
ok: [office1Router]
ok: [testClient2]
ok: [testServer2]

TASK [install software on CentOS] **********************************************
changed: [testClient1]
skipping: [testClient2]
skipping: [testServer2]
changed: [inetRouter]
changed: [office1Router]
changed: [testServer1]
changed: [centralRouter]

TASK [install software on Debian-based] ****************************************
skipping: [inetRouter]
skipping: [centralRouter]
skipping: [office1Router]
skipping: [testClient1]
skipping: [testServer1]
changed: [testClient2]
changed: [testServer2]

PLAY [set up vlan1] ************************************************************

TASK [Gathering Facts] *********************************************************
ok: [testServer1]
ok: [testClient1]

TASK [set up vlan1] ************************************************************
changed: [testServer1]
changed: [testClient1]

TASK [restart network for vlan1] ***********************************************
changed: [testClient1]
changed: [testServer1]

PLAY [set up vlan2] ************************************************************

TASK [Gathering Facts] *********************************************************
ok: [testClient2]
ok: [testServer2]

TASK [set up vlan2] ************************************************************
changed: [testClient2]
changed: [testServer2]

TASK [apply set up vlan2] ******************************************************
changed: [testClient2]
changed: [testServer2]

PLAY [set up bond0] ************************************************************

TASK [Gathering Facts] *********************************************************
ok: [centralRouter]
ok: [inetRouter]

TASK [set up ifcfg-bond0] ******************************************************
changed: [inetRouter]
changed: [centralRouter]

TASK [set up eth1,eth2] ********************************************************
changed: [centralRouter] => (item=templates/ifcfg-eth1)
changed: [inetRouter] => (item=templates/ifcfg-eth1)
changed: [centralRouter] => (item=templates/ifcfg-eth2)
changed: [inetRouter] => (item=templates/ifcfg-eth2)

TASK [restart hosts for bond0] *************************************************
changed: [centralRouter]
changed: [inetRouter]

PLAY RECAP *********************************************************************
centralRouter              : ok=6    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
inetRouter                 : ok=6    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
office1Router              : ok=2    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
testClient1                : ok=5    changed=3    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
testClient2                : ok=5    changed=3    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
testServer1                : ok=5    changed=3    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
testServer2                : ok=5    changed=3    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

```
</details>
    
    
* **inetRouter** 
* **centralRouter** 
* **office1Router**
* **testClient1**
* **testServer1**
* **testClient2**
* **testServer2**
---

## Выполнение задания:
### 2. Развернуть 7 виртуальных машин:    
    
В результате подготовки рабочего места (пункт 1) были созданы 7 виртуальные машины, к которым были применены необходимые настройки.    
Был выполнен playbook Ansible https://github.com/avlikh/Otus_pro_37/blob/main/provision.yaml.    
Созданные виртуальные машины соединены между сосбой, согласно схеме сети (см. ниже)

---
**Схема сети:**
     
![image](https://github.com/user-attachments/assets/84655336-28d8-411c-b7f5-fa5780355417)

**2.1 Проверим работу VLAN 1.** Для этого: 
* зайдем на **testClient1**: `vagrant ssh testClient1`
* посмотрим настройки сетевых интерфейсов: `ip -4 a`
* выполним пинг 10.10.10.254 и 10.10.10.1: `ping -c2 10.10.10.254 && ping -c2 10.10.10.1`
<details>
<summary> результат выполнения команды </summary>

```
root@deb4likh:/opt/otus/vlan-lacp# vagrant ssh testClient1
Last login: Mon Mar 17 15:10:33 2025 from 10.0.2.2
[vagrant@testClient1 ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 82146sec preferred_lft 82146sec
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.21/24 brd 192.168.56.255 scope global noprefixroute eth2
       valid_lft forever preferred_lft forever
5: eth1.1@eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 10.10.10.254/24 brd 10.10.10.255 scope global noprefixroute eth1.1
       valid_lft forever preferred_lft forever
[vagrant@testClient1 ~]$ ^C
[vagrant@testClient1 ~]$ ^C
[vagrant@testClient1 ~]$ ping -c2 10.10.10.254 && ping -c2 10.10.10.1
PING 10.10.10.254 (10.10.10.254) 56(84) bytes of data.
64 bytes from 10.10.10.254: icmp_seq=1 ttl=64 time=0.071 ms
64 bytes from 10.10.10.254: icmp_seq=2 ttl=64 time=0.136 ms

--- 10.10.10.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1018ms
rtt min/avg/max/mdev = 0.071/0.103/0.136/0.032 ms
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=2.44 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=64 time=1.68 ms

--- 10.10.10.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 1.678/2.059/2.440/0.381 ms
```
</details>
    
Итак, видим что VLAN 1 настроен и работает. 

---

**2.2 Проверим работу VLAN 2.** Для этого: 
* зайдем на **testClient2**: `vagrant ssh testClient2`
* посмотрим настройки сетевых интерфейсов: `ip -4 a`
* выполним пинг 10.10.10.254 и 10.10.10.1: `ping -c2 10.10.10.254 && ping -c2 10.10.10.1`
<details>
<summary> результат выполнения команды </summary>

```
root@deb4likh:/opt/otus/vlan-lacp# vagrant ssh testClient2
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-122-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon Mar 17 15:17:44 UTC 2025

  System load:             0.0
  Usage of /:              4.3% of 38.70GB
  Memory usage:            23%
  Swap usage:              0%
  Processes:               103
  Users logged in:         0
  IPv4 address for enp0s3: 10.0.2.15
  IPv6 address for enp0s3: fd00::fd:4dff:fe34:5576


Expanded Security Maintenance for Applications is not enabled.

99 updates can be applied immediately.
65 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

1 additional security update can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm

New release '24.04.2 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Mon Mar 17 15:16:08 2025 from 10.0.2.2
vagrant@testClient2:~$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 10.0.2.15/24 metric 100 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 81798sec preferred_lft 81798sec
4: enp0s19: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.56.31/24 brd 192.168.56.255 scope global enp0s19
       valid_lft forever preferred_lft forever
5: vlan2@enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 10.10.10.254/24 brd 10.10.10.255 scope global vlan2
       valid_lft forever preferred_lft forever
vagrant@testClient2:~$ ^C
vagrant@testClient2:~$ ^C
vagrant@testClient2:~$ ^C
vagrant@testClient2:~$ ping -c2 10.10.10.254 && ping -c2 10.10.10.1
PING 10.10.10.254 (10.10.10.254) 56(84) bytes of data.
64 bytes from 10.10.10.254: icmp_seq=1 ttl=64 time=0.060 ms
64 bytes from 10.10.10.254: icmp_seq=2 ttl=64 time=0.092 ms

--- 10.10.10.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1005ms
rtt min/avg/max/mdev = 0.060/0.076/0.092/0.016 ms
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=64 time=1.53 ms

--- 10.10.10.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 1.454/1.493/1.533/0.039 ms
```
</details>
    
Итак, видим что VLAN 2 настроен и работает.

---

**2.3 Проверим работу LACP (Link Aggregation Control Protocol).** Для этого:

* зайдем на **inetRouter**: `vagrant ssh inetRouter`
* посмотрим настройки сетевых интерфейсов: `ip -4 a && cat /proc/net/bonding/bond0`
* выполним бесконечный ping до centralRouter (192.168.255.2): `ping 192.168.255.2`
<details>
<summary> результат выполнения команды </summary>

```
root@deb4likh:/opt/otus/vlan-lacp# vagrant ssh inetRouter
Last login: Mon Mar 17 14:01:48 2025 from 192.168.56.1
[vagrant@inetRouter ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 81092sec preferred_lft 81092sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
[vagrant@inetRouter ~]$ ip -4 a && cat /proc/net/bonding/bond0
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 80440sec preferred_lft 80440sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
Ethernet Channel Bonding Driver: v5.14.0-570.el9.x86_64

Bonding Mode: fault-tolerance (active-backup) (fail_over_mac active)
Primary Slave: None
Currently Active Slave: eth1
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 08:00:27:99:06:43
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 08:00:27:37:79:6a
Slave queue ID: 0
[vagrant@inetRouter ~]$ ^C
[vagrant@inetRouter ~]$ ^C
[vagrant@inetRouter ~]$ ^C
[vagrant@inetRouter ~]$ ping 192.168.255.2
PING 192.168.255.2 (192.168.255.2) 56(84) bytes of data.
64 bytes from 192.168.255.2: icmp_seq=1 ttl=64 time=2.51 ms
64 bytes from 192.168.255.2: icmp_seq=2 ttl=64 time=1.70 ms
64 bytes from 192.168.255.2: icmp_seq=3 ttl=64 time=1.39 ms
64 bytes from 192.168.255.2: icmp_seq=4 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=5 ttl=64 time=1.41 ms
64 bytes from 192.168.255.2: icmp_seq=6 ttl=64 time=1.73 ms
64 bytes from 192.168.255.2: icmp_seq=7 ttl=64 time=1.64 ms
64 bytes from 192.168.255.2: icmp_seq=8 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=9 ttl=64 time=1.74 ms
64 bytes from 192.168.255.2: icmp_seq=10 ttl=64 time=1.51 ms
64 bytes from 192.168.255.2: icmp_seq=11 ttl=64 time=1.75 ms
64 bytes from 192.168.255.2: icmp_seq=12 ttl=64 time=1.28 ms
64 bytes from 192.168.255.2: icmp_seq=13 ttl=64 time=1.56 ms
64 bytes from 192.168.255.2: icmp_seq=14 ttl=64 time=1.48 ms
64 bytes from 192.168.255.2: icmp_seq=15 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=16 ttl=64 time=1.53 ms
64 bytes from 192.168.255.2: icmp_seq=17 ttl=64 time=1.59 ms
64 bytes from 192.168.255.2: icmp_seq=18 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=19 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=20 ttl=64 time=1.68 ms
64 bytes from 192.168.255.2: icmp_seq=21 ttl=64 time=3.11 ms
64 bytes from 192.168.255.2: icmp_seq=22 ttl=64 time=1.80 ms
64 bytes from 192.168.255.2: icmp_seq=23 ttl=64 time=1.56 ms
64 bytes from 192.168.255.2: icmp_seq=24 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=25 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=26 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=27 ttl=64 time=1.62 ms
64 bytes from 192.168.255.2: icmp_seq=28 ttl=64 time=1.52 ms
64 bytes from 192.168.255.2: icmp_seq=29 ttl=64 time=1.64 ms
64 bytes from 192.168.255.2: icmp_seq=30 ttl=64 time=1.77 ms
64 bytes from 192.168.255.2: icmp_seq=31 ttl=64 time=1.63 ms
64 bytes from 192.168.255.2: icmp_seq=32 ttl=64 time=1.47 ms
64 bytes from 192.168.255.2: icmp_seq=33 ttl=64 time=1.36 ms
64 bytes from 192.168.255.2: icmp_seq=34 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=35 ttl=64 time=1.57 ms
64 bytes from 192.168.255.2: icmp_seq=36 ttl=64 time=1.56 ms
64 bytes from 192.168.255.2: icmp_seq=37 ttl=64 time=1.57 ms
64 bytes from 192.168.255.2: icmp_seq=38 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=39 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=40 ttl=64 time=1.68 ms
64 bytes from 192.168.255.2: icmp_seq=41 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=42 ttl=64 time=1.72 ms
64 bytes from 192.168.255.2: icmp_seq=43 ttl=64 time=1.54 ms
64 bytes from 192.168.255.2: icmp_seq=44 ttl=64 time=1.75 ms
64 bytes from 192.168.255.2: icmp_seq=45 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=46 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=47 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=48 ttl=64 time=1.51 ms
64 bytes from 192.168.255.2: icmp_seq=49 ttl=64 time=1.50 ms
64 bytes from 192.168.255.2: icmp_seq=50 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=51 ttl=64 time=1.71 ms
64 bytes from 192.168.255.2: icmp_seq=52 ttl=64 time=1.52 ms
64 bytes from 192.168.255.2: icmp_seq=53 ttl=64 time=1.54 ms
64 bytes from 192.168.255.2: icmp_seq=55 ttl=64 time=2.84 ms
64 bytes from 192.168.255.2: icmp_seq=56 ttl=64 time=1.42 ms
64 bytes from 192.168.255.2: icmp_seq=57 ttl=64 time=1.58 ms
64 bytes from 192.168.255.2: icmp_seq=58 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=59 ttl=64 time=1.74 ms
64 bytes from 192.168.255.2: icmp_seq=60 ttl=64 time=1.61 ms
64 bytes from 192.168.255.2: icmp_seq=61 ttl=64 time=1.70 ms
64 bytes from 192.168.255.2: icmp_seq=62 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=63 ttl=64 time=1.68 ms
64 bytes from 192.168.255.2: icmp_seq=64 ttl=64 time=1.46 ms
64 bytes from 192.168.255.2: icmp_seq=65 ttl=64 time=0.625 ms
64 bytes from 192.168.255.2: icmp_seq=66 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=67 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=68 ttl=64 time=1.53 ms
64 bytes from 192.168.255.2: icmp_seq=69 ttl=64 time=1.77 ms
64 bytes from 192.168.255.2: icmp_seq=70 ttl=64 time=1.48 ms
64 bytes from 192.168.255.2: icmp_seq=71 ttl=64 time=1.54 ms
64 bytes from 192.168.255.2: icmp_seq=72 ttl=64 time=1.38 ms
64 bytes from 192.168.255.2: icmp_seq=73 ttl=64 time=1.63 ms
64 bytes from 192.168.255.2: icmp_seq=74 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=75 ttl=64 time=1.71 ms
64 bytes from 192.168.255.2: icmp_seq=76 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=77 ttl=64 time=1.62 ms
64 bytes from 192.168.255.2: icmp_seq=78 ttl=64 time=1.73 ms
64 bytes from 192.168.255.2: icmp_seq=79 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=80 ttl=64 time=1.53 ms
64 bytes from 192.168.255.2: icmp_seq=81 ttl=64 time=1.57 ms
64 bytes from 192.168.255.2: icmp_seq=82 ttl=64 time=1.64 ms
64 bytes from 192.168.255.2: icmp_seq=83 ttl=64 time=1.33 ms
64 bytes from 192.168.255.2: icmp_seq=84 ttl=64 time=1.59 ms
64 bytes from 192.168.255.2: icmp_seq=85 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=86 ttl=64 time=1.56 ms
^C
--- 192.168.255.2 ping statistics ---
86 packets transmitted, 85 received, 1.16279% packet loss, time 86220ms
rtt min/avg/max/mdev = 0.625/1.624/3.110/0.274 ms
```
</details>

**Далее, в другом окне терминала зайдем на хост inetRouter и проверим работу LACP.** Для Этого: 
* Посмотрим настройки сетевых интерфейсов: `ip -4 a && cat /proc/net/bonding/bond0`
* Отключим интерфейс eth1 на 10 секунд `ip link set down eth1`
* Посмотрим настройки сетевых интерфейсов: `ip -4 a && cat /proc/net/bonding/bond0`
* Включим его обратно `ip link set up eth1`
* Посмотрим настройки сетевых интерфейсов: `ip -4 a && cat /proc/net/bonding/bond0`
* Отключим интерфейс eth2 на 10 секунд: `ip link set down eth2`
* Посмотрим настройки сетевых интерфейсов: `ip -4 a && cat /proc/net/bonding/bond0`
* Включим его обратно: `ip link set up eth2`
* Посмотрим настройки сетевых интерфейсов: `ip -4 a && cat /proc/net/bonding/bond0`
* Примечание: Интерфейсы eth1 и eth2 объединены в bond что должно гарантировать, что при отключении одного из интерфейсов, сетевая связанность не нарушится.
<details>
<summary> результат выполнения команд </summary>

```
root@deb4likh:/opt/otus/vlan-lacp# vagrant ssh inetRouter
Last login: Mon Mar 17 15:22:52 2025 from 10.0.2.2
[vagrant@inetRouter ~]$ sudo -i
[root@inetRouter ~]# ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 81159sec preferred_lft 81159sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
[root@inetRouter ~]# exit
logout
[vagrant@inetRouter ~]$ exit
logout
root@deb4likh:/opt/otus/vlan-lacp# ^C
root@deb4likh:/opt/otus/vlan-lacp# ^C
root@deb4likh:/opt/otus/vlan-lacp# ^C
root@deb4likh:/opt/otus/vlan-lacp# ^C
root@deb4likh:/opt/otus/vlan-lacp# vagrant ssh inetRouter
Last login: Mon Mar 17 15:23:56 2025 from 10.0.2.2
[vagrant@inetRouter ~]$ sudo -i
[root@inetRouter ~]# ip -4 a && cat /proc/net/bonding/bond0
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 80146sec preferred_lft 80146sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
Ethernet Channel Bonding Driver: v5.14.0-570.el9.x86_64

Bonding Mode: fault-tolerance (active-backup) (fail_over_mac active)
Primary Slave: None
Currently Active Slave: eth1
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 08:00:27:99:06:43
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 08:00:27:37:79:6a
Slave queue ID: 0
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip link set down eth1
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip -4 a && cat /proc/net/bonding/bond0
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 80131sec preferred_lft 80131sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
Ethernet Channel Bonding Driver: v5.14.0-570.el9.x86_64

Bonding Mode: fault-tolerance (active-backup) (fail_over_mac active)
Primary Slave: None
Currently Active Slave: eth2
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth1
MII Status: down
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 1
Permanent HW addr: 08:00:27:99:06:43
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 08:00:27:37:79:6a
Slave queue ID: 0
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip link set up eth1
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip -4 a && cat /proc/net/bonding/bond0
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 80112sec preferred_lft 80112sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
Ethernet Channel Bonding Driver: v5.14.0-570.el9.x86_64

Bonding Mode: fault-tolerance (active-backup) (fail_over_mac active)
Primary Slave: None
Currently Active Slave: eth2
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 1
Permanent HW addr: 08:00:27:99:06:43
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 08:00:27:37:79:6a
Slave queue ID: 0
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip link set down eth2
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip -4 a && cat /proc/net/bonding/bond0
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 80097sec preferred_lft 80097sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
Ethernet Channel Bonding Driver: v5.14.0-570.el9.x86_64

Bonding Mode: fault-tolerance (active-backup) (fail_over_mac active)
Primary Slave: None
Currently Active Slave: eth1
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 1
Permanent HW addr: 08:00:27:99:06:43
Slave queue ID: 0

Slave Interface: eth2
MII Status: down
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 1
Permanent HW addr: 08:00:27:37:79:6a
Slave queue ID: 0
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip link set up eth2
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ^C
[root@inetRouter ~]# ip -4 a && cat /proc/net/bonding/bond0
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s3
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 80078sec preferred_lft 80078sec
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s19
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth3
       valid_lft forever preferred_lft forever
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.255.1/30 brd 192.168.255.3 scope global noprefixroute bond0
       valid_lft forever preferred_lft forever
Ethernet Channel Bonding Driver: v5.14.0-570.el9.x86_64

Bonding Mode: fault-tolerance (active-backup) (fail_over_mac active)
Primary Slave: None
Currently Active Slave: eth1
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 1
Permanent HW addr: 08:00:27:99:06:43
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 1
Permanent HW addr: 08:00:27:37:79:6a
Slave queue ID: 0
[root@inetRouter ~]#
```
</details>

Вернемся на хост **inetRouter** и посмотрим были ли потери пакетов до centralRouter (192.168.255.2):

<details>
<summary> Видим, что потери сетевой связанности не было </summary>

```
[vagrant@inetRouter ~]$ ping 192.168.255.2
PING 192.168.255.2 (192.168.255.2) 56(84) bytes of data.
64 bytes from 192.168.255.2: icmp_seq=1 ttl=64 time=2.51 ms
64 bytes from 192.168.255.2: icmp_seq=2 ttl=64 time=1.70 ms
64 bytes from 192.168.255.2: icmp_seq=3 ttl=64 time=1.39 ms
64 bytes from 192.168.255.2: icmp_seq=4 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=5 ttl=64 time=1.41 ms
64 bytes from 192.168.255.2: icmp_seq=6 ttl=64 time=1.73 ms
64 bytes from 192.168.255.2: icmp_seq=7 ttl=64 time=1.64 ms
64 bytes from 192.168.255.2: icmp_seq=8 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=9 ttl=64 time=1.74 ms
64 bytes from 192.168.255.2: icmp_seq=10 ttl=64 time=1.51 ms
64 bytes from 192.168.255.2: icmp_seq=11 ttl=64 time=1.75 ms
64 bytes from 192.168.255.2: icmp_seq=12 ttl=64 time=1.28 ms
64 bytes from 192.168.255.2: icmp_seq=13 ttl=64 time=1.56 ms
64 bytes from 192.168.255.2: icmp_seq=14 ttl=64 time=1.48 ms
64 bytes from 192.168.255.2: icmp_seq=15 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=16 ttl=64 time=1.53 ms
64 bytes from 192.168.255.2: icmp_seq=17 ttl=64 time=1.59 ms
64 bytes from 192.168.255.2: icmp_seq=18 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=19 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=20 ttl=64 time=1.68 ms
64 bytes from 192.168.255.2: icmp_seq=21 ttl=64 time=3.11 ms
64 bytes from 192.168.255.2: icmp_seq=22 ttl=64 time=1.80 ms
64 bytes from 192.168.255.2: icmp_seq=23 ttl=64 time=1.56 ms
64 bytes from 192.168.255.2: icmp_seq=24 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=25 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=26 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=27 ttl=64 time=1.62 ms
64 bytes from 192.168.255.2: icmp_seq=28 ttl=64 time=1.52 ms
64 bytes from 192.168.255.2: icmp_seq=29 ttl=64 time=1.64 ms
64 bytes from 192.168.255.2: icmp_seq=30 ttl=64 time=1.77 ms
64 bytes from 192.168.255.2: icmp_seq=31 ttl=64 time=1.63 ms
64 bytes from 192.168.255.2: icmp_seq=32 ttl=64 time=1.47 ms
64 bytes from 192.168.255.2: icmp_seq=33 ttl=64 time=1.36 ms
64 bytes from 192.168.255.2: icmp_seq=34 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=35 ttl=64 time=1.57 ms
64 bytes from 192.168.255.2: icmp_seq=36 ttl=64 time=1.56 ms
64 bytes from 192.168.255.2: icmp_seq=37 ttl=64 time=1.57 ms
64 bytes from 192.168.255.2: icmp_seq=38 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=39 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=40 ttl=64 time=1.68 ms
64 bytes from 192.168.255.2: icmp_seq=41 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=42 ttl=64 time=1.72 ms
64 bytes from 192.168.255.2: icmp_seq=43 ttl=64 time=1.54 ms
64 bytes from 192.168.255.2: icmp_seq=44 ttl=64 time=1.75 ms
64 bytes from 192.168.255.2: icmp_seq=45 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=46 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=47 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=48 ttl=64 time=1.51 ms
64 bytes from 192.168.255.2: icmp_seq=49 ttl=64 time=1.50 ms
64 bytes from 192.168.255.2: icmp_seq=50 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=51 ttl=64 time=1.71 ms
64 bytes from 192.168.255.2: icmp_seq=52 ttl=64 time=1.52 ms
64 bytes from 192.168.255.2: icmp_seq=53 ttl=64 time=1.54 ms
64 bytes from 192.168.255.2: icmp_seq=55 ttl=64 time=2.84 ms
64 bytes from 192.168.255.2: icmp_seq=56 ttl=64 time=1.42 ms
64 bytes from 192.168.255.2: icmp_seq=57 ttl=64 time=1.58 ms
64 bytes from 192.168.255.2: icmp_seq=58 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=59 ttl=64 time=1.74 ms
64 bytes from 192.168.255.2: icmp_seq=60 ttl=64 time=1.61 ms
64 bytes from 192.168.255.2: icmp_seq=61 ttl=64 time=1.70 ms
64 bytes from 192.168.255.2: icmp_seq=62 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=63 ttl=64 time=1.68 ms
64 bytes from 192.168.255.2: icmp_seq=64 ttl=64 time=1.46 ms
64 bytes from 192.168.255.2: icmp_seq=65 ttl=64 time=0.625 ms
64 bytes from 192.168.255.2: icmp_seq=66 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=67 ttl=64 time=1.60 ms
64 bytes from 192.168.255.2: icmp_seq=68 ttl=64 time=1.53 ms
64 bytes from 192.168.255.2: icmp_seq=69 ttl=64 time=1.77 ms
64 bytes from 192.168.255.2: icmp_seq=70 ttl=64 time=1.48 ms
64 bytes from 192.168.255.2: icmp_seq=71 ttl=64 time=1.54 ms
64 bytes from 192.168.255.2: icmp_seq=72 ttl=64 time=1.38 ms
64 bytes from 192.168.255.2: icmp_seq=73 ttl=64 time=1.63 ms
64 bytes from 192.168.255.2: icmp_seq=74 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=75 ttl=64 time=1.71 ms
64 bytes from 192.168.255.2: icmp_seq=76 ttl=64 time=1.65 ms
64 bytes from 192.168.255.2: icmp_seq=77 ttl=64 time=1.62 ms
64 bytes from 192.168.255.2: icmp_seq=78 ttl=64 time=1.73 ms
64 bytes from 192.168.255.2: icmp_seq=79 ttl=64 time=1.66 ms
64 bytes from 192.168.255.2: icmp_seq=80 ttl=64 time=1.53 ms
64 bytes from 192.168.255.2: icmp_seq=81 ttl=64 time=1.57 ms
64 bytes from 192.168.255.2: icmp_seq=82 ttl=64 time=1.64 ms
64 bytes from 192.168.255.2: icmp_seq=83 ttl=64 time=1.33 ms
64 bytes from 192.168.255.2: icmp_seq=84 ttl=64 time=1.59 ms
64 bytes from 192.168.255.2: icmp_seq=85 ttl=64 time=1.55 ms
64 bytes from 192.168.255.2: icmp_seq=86 ttl=64 time=1.56 ms
^C
--- 192.168.255.2 ping statistics ---
86 packets transmitted, 85 received, 1.16279% packet loss, time 86220ms
rtt min/avg/max/mdev = 0.625/1.624/3.110/0.274 ms
```
</details>
    
**Итак, видим что LACP работает и пакеты не теряются при отключении одного из интерфейсов сети, объединенной в BOND.**


