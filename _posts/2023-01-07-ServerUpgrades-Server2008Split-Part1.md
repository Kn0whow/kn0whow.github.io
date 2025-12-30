---
title: "Windows Server 2008 Upgrade - Part 1: Moving AD, DNS, and DHCP from Server 2008 to Server 2022"
categories: 
  - Windows Server
tags:
  - Windows Server 2008
  - Windows Server 2022
  - Windows Server
  - Server Upgrades
  - Active Directory
  - DHCP
  - DNS
toc: true
toc_label: "Contents"
---

2023 is the year of server upgrades for me. I have around 14 servers that all need OS upgrades, mainly from 2012 R2 to 2022, but one of the most difficult ones is the 2008 Windows Server that this post is about. Yes you heard correctly we stil have 2008 Servers in production.

## Purpose of the project

The 2008 server has the following roles: Active Directory (AD), DNS, DHCP, and the main company File Server, which of course is neither good or ideal. So the purpose of this project is to split the server, which is a physical server, into two new 2022 virtual servers. One server will run AD, DNS, and DHCP, and the other will be the new file server. So the project will be split into two parts:

* Part 1: Move the AD, DNS, and DHCP roles onto a new 2022 server.
* Part 2: Move the file server onto a new 2022 server, separate from the new Domain Controller (DC) server.

This server has been in production for many many years, since before I started at the company, so now I have finally got the chance to sort this mess of a server out!

## Plan of Action

For the first part of the project I made the following plan:

* Create new 2022 DC (DC22).
* Install DHCP Role on the new DC.
* Export DHCP from 2008 DC (DC08) and import onto 2022 DC.
* Deauthorise 2008 DHCP server and Authorise the new 2022 DHCP server.
* Install AD on 2022 using 2008 as the template, and promote as DC.
* Transfer all DC FSMO roles from 2008 to 2022.
* Decommission 2008 DC and upgrade Domain Schema and Forest to Windows2016 version.
* Take IP address from 2008 server and assign to 2022 server to ensure static IP configs see the new 2022 server for DNS and DHCP.
* Continue to use 2008 server as file serve until part 2 of the project is complete.

## Implementing the plan

### Creating the new DC server

I created the new DC server (DC22) in Hyper-V using Windows Server 2022 Standard OS. The VM has 2 vCPUs, 4GB of RAM and a 75GB C: Drive. These specs seem to be ideal for a domain controller. I would have used 1 vCPU but our Anti-Virus software was using too much of the CPU.

### Moving DHCP from DC08 to DC22

To migrate DHCP onto the new server I followed the following guide on the [Spiceworks](https://community.spiceworks.com/how_to/160139-migrate-dhcp-from-windows-server-2008-to-windows-server-2019) website [1].

### Moving AD and DNS role from DC08 to DC22

To migrate the AD and DNS roles onto the new 2022 server I used the following guide on the [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/itops-talk-blog/step-by-step-guide-active-directory-migration-from-windows/ba-p/2888117) website [2].

### Changing IP from old DC08 to new DC22

After migrating DCHP and the DC roles over to the new server, I then moved the IP address of the old DC08 server onto the new DC22 server. My reasoning for doing this is because we have many static IP configs that point DNS to the DC08 IP address so it made sense to continue to use the same IP address to avoid mass config changes and potential DNS issues. 

The IP address of the old DC08 server was then changed to a new one.

## Testing

To test how successful the changes were, we did the following:

* Reboot IP phones and ensure they pick up DHCP addresses.
* Test logging into PC's and Laptops over ethernet and wifi to ensure they can find domain logon servers.
* Test DNS by pinging hostnames.
* Test shared folders are still accessible on DC08 as it will still be used as a file server until Part 2 is complete.

All tests were successful!

## Issues that arose during work

Fortunately there weren't any major issues. One of the only issues was the need to reconfigure a Datto backup agent to point to the new IP address of the old DC08 server to ensure files on there are still backed up.

## Conclusion

Overall I think the work took around 3 hours to complete when taking into account all the server intallations and reboots and the testing at the end. But at the end of it all, we now have a brand new 2022 server running AD, DHCP and DNS with no apparent issues. The next part will cover moving files over from the DC08 to the new file server FS22.

Thank you for reading!

## References

1. [https://community.spiceworks.com/how_to/160139-migrate-dhcp-from-windows-server-2008-to-windows-server-2019](https://community.spiceworks.com/how_to/160139-migrate-dhcp-from-windows-server-2008-to-windows-server-2019)
2. [https://techcommunity.microsoft.com/t5/itops-talk-blog/step-by-step-guide-active-directory-migration-from-windows/ba-p/2888117](https://techcommunity.microsoft.com/t5/itops-talk-blog/step-by-step-guide-active-directory-migration-from-windows/ba-p/2888117)
