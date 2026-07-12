This is a multicast DNS repeater that has been modified to work on OpenBSD.
It might have broken Linux compatibility however, but it works for me.

On OpenBSD, the daemon uses unveil(2) to expose only its configured pid file
and pledge(2) to reduce its privileges in stages.  Once initialized, it can
only perform standard I/O and IPv4 network operations (plus removal of its pid
file when running in the background).

Original source was obtained from
https://bitbucket.org/geekman/mdns-repeater/src/28ecc2ab9a0e26c73148711c867d9d2b5dafff91

Original readme below:

mdns-repeater
==============
mdns-repeater is a Multicast DNS repeater for Linux. Multicast DNS uses the 
224.0.0.51 address, which is "administratively scoped" and does not 
leave the subnet.

This program re-broadcast mDNS packets from one interface to other interfaces.
It was written primarily to be run on my Linksys WRT54G which runs dd-wrt,
since my wireless network is on a different subnet from my wired network and 
I would like my zeroconf devices to work properly across the two subnets.

Since the mDNS protocol sends the AA records in the packet itself, the 
repeater does not need to forge the source address. Instead, the source 
address is of the interface that repeats the packet.


USAGE
-----
mdns-repeater only requires the interface names and it will do the rest.
For example, the dd-wrt standard installation defines br0 for the wireless 
interface and vlan1 as the WAN interface, I would use:

    mdns-repeater br0 vlan1

You can also specify the -f flag for debugging, which prints packets as they 
are received.

OPENBSD RC.D SERVICE
--------------------
An example rc.d(8) service is included in rc.d/mdns_repeater.  It assumes the
binary is installed as /usr/local/sbin/mdns-repeater.  Install and enable it,
replacing the example interface names with the interfaces to bridge:

    install -m 0555 rc.d/mdns_repeater /etc/rc.d/mdns_repeater
    rcctl set mdns_repeater flags em0 vlan10
    rcctl enable mdns_repeater
    rcctl start mdns_repeater

The daemon does not support configuration reloads; restart it after changing
its flags:

    rcctl restart mdns_repeater

You are free to modify the code to repeat whatever traffic you require, as
long as you abide by the software license.


LICENSE
--------
Copyright (C) 2011 Darell Tan

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301, USA.

