---
title: Vagrant - virtual box
date: 2017-09-18 12:06:26
toc: true 
tags:
- Vagrant
- Virtual Box
categories: Virtualization
---
## Vagrant + virtual box vm with host-based network
- The vm can access internet
- The host can access the services running on the vm (e.g. httpd) 

Follow below steps to configure a [host-only network](https://www.virtualbox.org/manual/ch06.html#network_hostonly) vm and provision by vagrant:
1. Create a host-only network on Oracle VM VirtualBox Manager
   File -> Preferences -> Network -> Host-only Networks
     {% img "/images/2017-09-18 12_20_58-Virtual Box Manager - Host-only network 1.png"  %} 
     {% img "/images/2017-09-18 12_20_58-Virtual Box Manager - Host-only network 2.png"  %} 

   By default, it will auto assign all required values for IPv4 Address, network mask. If DHCP server option is enabled, it will auto assign the required values.
2. VirtualBox creates a new virtual interface on the host:
{% codeblock line_number:false lang:bash %}
root@localhost:~$ ifconfig vboxnet1
vboxnet1  Link encap:Ethernet  HWaddr 0a:00:27:00:00:01
          inet addr:172.28.128.1  Bcast:172.28.128.255  Mask:255.255.255.0
          inet6 addr: fe80::800:27ff:fe00:1/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:96 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:21440 (21.4 KB)
{% endcodeblock %} 
3. Last (but not least ^_^), edit your [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/):
{% codeblock line_number:false lang:ruby %}
  config.vm.provider "virtualbox" do |vb|
    config.vm.network "private_network", :type => 'dhcp', :name => 'vboxnet1', :adapter => 2
  end
{% endcodeblock %} 

<!-- more -->

