---
layout: post
title: "Secure IOT with VLAN on pfSense"
date:	2019-12-04
categories:
    - posts
tags:
    - informational
---

There are plenty of articles and videos explaining how this is setup, however the issue I’ve come across is setting up VLAN tagging and trunking etc. So this is my attempt explain and outlining my steps with pfSense, Managed switch and Unifi Network.

First setting up the VLAN ID on pfSense is fairly straight forward. Go to `Interfaces > Assignments > VLANs > Add`. Here and in this situation I am using the parent interface LAN, choose a VLAN ID other then 1 and give a description. Save

![]({{ site.url }}/attachments/VLAN-ID-1024x381.png)

Next you will want to create the VLAN interface. Go to `Interfaces > Assignments`. Here you will want to add another interface which will include the VLAN ID on the interface you specified. Save

![]({{ site.url }}/attachments/VLAN-ID-1-1024x381.png)

Next you will want to Edit the interface that you just created by clicking on the interface name. What you will want to edit is the changing the IPv4 Configuration Type to Static IPv4. Next the IPv4 Configuration needs to be updated with a private LAN address of your choice.

![]({{ site.url }}/attachments/Interface-Edit-1024x1010.png)

Next you will want to configure DHCP for the IOT interface. Go to `Services > DHCP Server > IOT Interface`. Enable the interface, provide a DHCP range and add the DNS Server which will usually be .1 of the created private IP. Save

![]({{ site.url }}/attachments/DHCP-Server-973x1024.png)

Creating a Alias is a simple way to add your secured IPs as a pool.

![]({{ site.url }}/attachments/SecuredLAN-Aliases-1024x484.png)

Lastly you will want to create a Rule for the VLAN. You dont want this VLAN to communicate with your secured LAN however you should be able to talk to the VLAN from your secured LAN. The rule action should be Pass to allow networking. Protocol should be set to Any. Source should be any however destination should a single host or alias not your SecureLAN.

![]({{ site.url }}/attachments/IOT-FWRule-1002x1024.png)

Now you will want to create a Trunking port on your switch that carry the VLAN ID. You will also want to tag all the ports on the switch that you want the VLAN ID carried to.

As an example, I am using a Zyxel GS1900-24 Switch. First you will want to create the VLAN ID.

![]({{ site.url }}/attachments/SwitchVLANCreate-1024x703.png)

Next create the Trunking port. This is usually the port coming from your router(pfSense) carrying the VLAN ID.

![]({{ site.url }}/attachments/SwitchTrunking-923x1024.png)

Last you will want to Tag the VLAN ports in which you want the ID carried to.

![]({{ site.url }}/attachments/SwitchVLANTagging-768x978.png)

In just this situation, if you connect a device to one of the configured ports, the device should pickup DHCP for the VLAN created.

Next steps are likely what you want to use as most IOT devices are configured using a wireless SSID. In this situation I am using a Unfi Access Point. First create the Network in the controller using the VLAN ID created in pfSense. In this case ID – 50.

![]({{ site.url }}/attachments/Unifi-Networks-1024x556.png)

Since the controller is plugged into one of the ports on the switch and the port is tagged to carry the VLAN ID, the VLAN IP should be carried as well. Next you will want to create the Wireless Network.

![]({{ site.url }}/attachments/Wireless-NetworkUnifi-911x1024.png)

A side note, I’ve edit the User Group because nothing utilizing this wireless network needs full network speeds.

![]({{ site.url }}/attachments/UserGroups-1024x752.png)

This should then allow you to configured your IOT devices to connect to this SSID which has limited network speeds on its own VLAN interface.

Hopefully this helps someone and if there are any questions, please feel free to ask.
