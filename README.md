# vagrant_base_elasticsearch
Vagrant basebox with Oracle Linux 7 (latest) and the most common Elasticsearch (6.x) components pre-installed.

This basebox only *installs* the base components but does *not* start them at boot.

In boxes and setups you derive from this basebox you can use the following Ansible code to autostart the required services at boot time (choose applicable services):

```
- name: Start and enable services at boot time
  service:
     name: "{{ item }}"
     state: started
     enabled: True
  with_items:
     - elasticsearch
     - logstash
     - kibana
     - filebeat
     - heartbeat-elastic
     - metricbeat
     - packetbeat     
  when:
     - ansible_os_family == "RedHat"
     - ansible_distribution_major_version == "7"
```     

# Ansible in guest VM

Because this Vagrant system is developed to work on both Windows and Mac
we will need Ansible to be available in the guest VM (Vagrant method: ansible_local).

Ansible will be installed on the guest VM automatically.


# VirtualBox file transfer mechanism

To be able to use the VirtualBox default transfer mechanism for the
Ansible playbooks we need the VirtualBox utilities installed on the guest VM.

When using the default VirtualBox transfer mechanism to transfer the Ansible playbook files
to the Vagrant guest machine *without* the VirtualBox utilities the following error occurs:

```
Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available. This filesystem is
made available via the VirtualBox Guest Additions and kernel module.
Please verify that these guest additions are properly installed in the
guest. This is not a bug in Vagrant and is usually caused by a faulty
Vagrant box. For context, the command attempted was:

mount -t vboxsf -o uid=1000,gid=1000 vagrant /vagrant

The error output from the command was:

/sbin/mount.vboxsf: mounting failed with the error: No such device
```

Basing the boxes directly on the base images provided by Oracle gives us the VirtualBox utilities installed by default.

PS: There have been problems with the Vagrant plugin "vagrant-vbguest".
If you experience issues with the VirtualBox guest additions not being updated properly
and therefore e.g. ansible_local can not run because of a missing /vagrant shared folder please consider removing the Vagrant plugin "vagrant-vbguest" as follows:

```
# vagrant plugin uninstall vagrant-vbguest
Uninstalling the 'vagrant-vbguest' plugin...
Successfully uninstalled micromachine-2.0.0
Successfully uninstalled vagrant-vbguest-0.15.1
```

# Regarding port numbers that Elasticsearch uses

```
Component	                          Port
Elasticsearch API	                  9200
Kibana webinterface	                5061
```


# Note for MobaXterm users!

There is a 'bug' in MobaXterm that does not allow for the default 'vagrant ssh' commands.

Please perform the following command:

vagrant ssh-config

This will produce the following output:

```
Host es_basebox
  HostName 127.0.0.1
  User vagrant
  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /cygdrive/d/Users/<USERNAME>/Documents/MobaXterm/home/Vagrant/vagrant_base_elasticsearch/.vagrant/machines/es_basebox/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL


```

Please create a seperate SSH session with abovementioned details.

In the tab "Advanced SSH settings" select "Use private key" and select the location of the
"IdentityFile" as mentioned above.
