---
title: "Taiga"
date: 2016-10-24T11:59:11-03:00
---

## Instalación

```bash>
<code bash>
$ vagrant up --provider=virtualbox
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'xenial64' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Box file was not detected as metadata. Adding it directly...
==> default: Adding box 'xenial64' (v0) for provider: virtualbox
    default: Downloading: https://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-vagrant.box
==> default: Successfully added box 'xenial64' (v0) for 'virtualbox'!
==> default: Importing base box 'xenial64'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: taiga-vagrant_default_1477319354976_86666
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 8000 (guest) => 8000 (host) (adapter 1)
    default: 9001 (guest) => 9001 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: ubuntu
    default: SSH auth method: password
    default: 
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default: 
    default: Guest Additions Version: 5.0.24
    default: VirtualBox Version: 5.1
==> default: Mounting shared folders...
    default: /vagrant => /home/scayuqueo/taiga/taiga-vagrant
    default: /home/vagrant/data => /home/scayuqueo/taiga/taiga-vagrant/data
==> default: Running provisioner: shell...
    default: Running: inline script
==> default: Hit:1 http://archive.ubuntu.com/ubuntu xenial InRelease
==> default: Hit:2 http://security.ubuntu.com/ubuntu xenial-security InRelease
==> default: Get:3 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [95.7 kB]
==> default: Get:4 http://security.ubuntu.com/ubuntu xenial-security/main Sources [43.9 kB]
==> default: Get:5 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [92.2 kB]
==> default: Get:6 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [12.3 kB]
==> default: Get:7 http://security.ubuntu.com/ubuntu xenial-security/multiverse Sources [728 B]
==> default: Get:8 http://archive.ubuntu.com/ubuntu xenial/main Sources [868 kB]
==> default: Get:9 http://archive.ubuntu.com/ubuntu xenial/restricted Sources [4,808 B]
==> default: Get:10 http://archive.ubuntu.com/ubuntu xenial/universe Sources [7,728 kB]
==> default: Get:11 http://archive.ubuntu.com/ubuntu xenial/multiverse Sources [179 kB]
==> default: Get:12 http://archive.ubuntu.com/ubuntu xenial-updates/main Sources [198 kB]
==> default: Get:13 http://archive.ubuntu.com/ubuntu xenial-updates/universe Sources [103 kB]
==> default: Get:14 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse Sources [3,220 B]
==> default: Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [410 kB]
==> default: Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [345 kB]
==> default: Get:17 http://archive.ubuntu.com/ubuntu xenial-backports/main Sources [1,464 B]
==> default: Get:18 http://archive.ubuntu.com/ubuntu xenial-backports/universe Sources [800 B]
==> default: Get:19 http://archive.ubuntu.com/ubuntu xenial-backports/main amd64 Packages [1,540 B]
==> default: Get:20 http://archive.ubuntu.com/ubuntu xenial-backports/main Translation-en [1,484 B]
==> default: Get:21 http://archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [1,000 B]
==> default: Get:22 http://archive.ubuntu.com/ubuntu xenial-backports/universe Translation-en [584 B]
==> default: Fetched 10.1 MB in 22s (449 kB/s)
==> default: Reading package lists...
==> default: Reading package lists...
==> default: Building dependency tree...
==> default: 
==> default: Reading state information...
==> default: git is already the newest version (1:2.7.4-0ubuntu1).
==> default: bash is already the newest version (4.3-14ubuntu1.1).
==> default: 0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
==> default: Cloning into 'taiga-scripts'...
```

Acceso

<http://localhost:8000>

Admin

<http://localhost:8000/admin/>

Usuario y contraseña por default - admin : 123123
