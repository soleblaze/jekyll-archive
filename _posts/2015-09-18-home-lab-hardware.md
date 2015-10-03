---
layout: post
date: "2015-09-18"
title: "Homelab"
---

I've been playing with Openstack off and on since Grizzly. I decided it's time
to go back and redo my lab and install Kilo on it. I'm using a mix of hardware
from my Grizzly install and other pieces I have lying around.

![Hardware](/images/20150918-cluster.jpg)

## Server Setup


-   Acer 1820t i3 laptop with 6GB running as the imaging and configuration
management server.

-   Acer 1820t i7 laptop with 6GB and a 100mbit ethernet USB dongle running as
the network node.

-   Thinkpad X230 i7 laptop with 16GB running as the controller node

-   White Box'd i3 Ivy Bridge with 32GB running as a compute node

-   White Box'd i7 Ivy Bridge with 32GB running as a compute node

-   White Box'd i7 Sandy Bridge with 32GB running as a compute node

-   4 BeagleBone Blacks running as VPN end points

All the boxes have SSDs. The compute nodes also have WD Black 1TB drives for
instance storing. The controller node is also running cinder and exports a
500GB SSD that way. The main bottleneck is the 100mbit ethernet usb dongle on
the network node. All network traffic goes through this machine.

## Network Setup

Everything connects to a 1820-24T network switch. I split my network into
different VLANs to better segregate traffic. This includes backend management,
the api network, and multiple networks to segregate projects within Openstack.
Each compute node uses multiple VLANs off a single NIC. The network node is
using a usb nic to route each of the VLANs associated with a project.

The cluster is also segmented from my home network through the use of VLANs and
VPN endpoints. I have to log into a vpn to perform any maintenance to the
system. I also have to log into a separate vpn server to access most of the lab
systems I have running.

## Initial Setup

Initially I worked on getting this setup deployed via puppet. However, I ran
into bugs with the official puppet openstack scripts. I also had a hard time
figuring out how to get puppet to configure neutron for VLANs. Instead, I ended
up writing my own bash scripts to install the cluster. You can download them
from my [openstack github repo](https://github.com/soleblaze/openstack).

## Drawbacks to my setup

The main issues with my setup is the lack of shared storage. While I don't have
enough nodes to get true HA, it would be nice to be able to perform live
migrations of instances. The 100mbit USB network adapter is also a pain to deal
with if I have to upload data to an instance from my laptop.

## Future plans

While this setup works, it's not ideal. I'm looking at rearchitecting my setup
to use chef to deploy and manage Openstack. I'm also working on increasing my
network speed and decreasing the reliance on the slow networking node by
updating neutron to use [Distributed Virtual
Routing](http://docs.openstack.org/networking-guide/scenario_dvr_ovs.htm).
