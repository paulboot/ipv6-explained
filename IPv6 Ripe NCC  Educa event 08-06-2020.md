## IPv6 Ripe NCC :: Educa event 08/06/2020

#### Contents

* [IPv6 numberplan design](#IPv6 numberplan design)



### Ripe Allocation policy
* Minimum allocation size is /32 and up to a /29 without additional justification
* More if justified by customer numbers and network extension
* Additional bits based on hierarchical and geographical structure, planned longevity and security levels
* Every assignment must be registered in the RIPE Database
* Minimum Pl (providor independant) assignment size is /48 (for non members)

### Atlas probe network

#### Probes

* https://labs.ripe.net/Members/alun_davies/ripe-atlas-software-probes
* Hardware
  * https://www.turris.cz/en/
  * https://secure.nic.cz/files/Turris-web/MOX/MOX_Brochure.pdf

#### Measurements

* DNS k-root stable around 17%, not the 30% as HTTP? (DNS server managers are lagging)

### IPv6 numberplan design

* Link local (do not manage, do not put in DNS!!)
* Global unicast
* Unique Local Addressess (ULA): unique, should not overlap when organisation merge
  * 7 bit: FC00::/7 prefix to identify Local IPv6 unicast address
  * 40 bit: Global Unique ID, chosen randomly
  * 16 bit: Subnet ID
  * 64 bit: Interface ID
  * Not very popular, because of the extra (double) IP adress managment. Perhaps easier to use a seperate half of a Global Unique PI adresses for this.
* Every org should be assigned a /48
* Smallest BGP routed is /48 (also for PI addresses)
* How split up: /48 ??
  * 128-48-64 = 16 bits for subnets   (2^16)
  * 16 bits
    * per floor / per rack / per location
    * or copy the VLAN number in the /16 part and all use /64
    * or location bits the type/security bits
    * or type/security bits and then location bits (easy for firewalls)
  * IPv6 end-system configuration
    * stateless autoconfiguration (totally random and dynamic)
      * Guest/BYOD including Android
    * DHCPv6 (still not supported by Android???)
      * For enterprises/workstations etc...
  * ::1 for default route address (VRRP router1 :11, router2 :12)
  * IPv4 10.1.1.10  --> 2001:db8:edca:10:1:1::10/64 ?????
  * use service port number: ::53 for DNS, ::80 for HTTP
  * matching IPv4: • 192.0.2.1 → 2001:db8:edca:8001:192:0:2:1
    * (but 2001:db8:edca:8001::192.0.2.1 is something different!)
  * make a clear distinction between public routed prefixes and the rest

#### Examples

* 2001:db8:edca:8001::1

* 2001:db8:edca:8001::11

* 2001:db8:edca:8001::12

* 2001:db8:edca:8001::80

* 2001:db8:edca:8001::2005   -> DHCPv6

* 2001:db8:edca:8001:203::113:127  -> manual from 203.0.113.127

* 2001:db8:edca:8001:5054:18ff:fedb:d4a4  -> MAC

* 2001:db8:edca:8001:c139:b4c1:6850:12e5  -> privacy

#### DNS server addresses are important

* DNS addresses are the only addresses you can't look up in the DNS!
  * (at least, those should be the only ones)
  * so need to be easy to type/remember and avoid renumbering them
  * So give each their own /64 (so they can be moved independently) with low subnet #, such as:
    * DNS server 1: 2001:db8:edca:**1::53**/64
    * DNS server 2: 2001:db8:edca:**2::53**/64
    * DNS server 3: 2001:db8:edca:**3::53**/64
  * make clear separation between public and non public DNS servers

#### References IPv6 plans

* Much of this based on the Surfnet IPv6 address planning guide:
  * https://www.ripe.net/support/training/material/IPv6-for-LIRs-TrainingCourse/Preparing-an-IPv6-Addressing-Plan.pdf/at_download/file 
  * Also available in Dutch: • https://wiki.surfnet.nl/download/attachments/11211103/rapport_201309_IPv6_numplan_NL.pdf 

### IPv6-only in the data center (SIIT-DC)

Source: Tore Anderson, 

* Front-end must be dual stack
* Back-end stick to a single protocol! (dual stack is too much of a hassle)
  * IPv6 less renumbering for server segments (IPv4 /24 can fill up!)
  * IPv6 Facebook measures 15% better performance
  * New datacenters do IPv6 only if you can proxy?? (no see SIIT-DC)
  * If you have no growing pains in IPv4 then bother at the backend
* Front-end to back-end translation
  * Stateless 1:1 IPv4 ↔ IPv6 NAT function
    * IPv4 clients mapped to an IPv6 prefix: 0.0.0.0/0 ↔ 64:ff9b::0.0.0.0/96
    * IPv6 frontends mapped to an IPv4 address: 203.0.113.1 ↔ 2001:db8::1
      * Static table of arbitrary 1:1 IPv4 ↔ IPv6 mappings – Layer-3 NAT (not NAPT!), does not touch TCP/UDP payload
      * May be deployed anywhere in the network backbone – Or in a different autonomous system altogether (think IPv4aaS)
      * Cisco ASR has an implementation
      * Jool [SIIT-DC](https://www.jool.mx/en/siit-dc.html) also supports NAT64
      * Use DNS64+NAT64 for IPv6 servers with any outgoing traffic?
        * http://www.litech.org/tayga/
        * IPv6 only testing, use public NAT64/DNS64 (nat 64.net)
          * Set your nameservers to 2a00:1098:2c::1 / 2a00:1098:2b::1
            2a01:4f9:c010:3f02::1 for public NAT64/DNS64

![2020-06-08 11_33_16-ipv6dc-siit-dc-1](images\2020-06-08 11_33_16-ipv6dc-siit-dc-1.png)



### IPv6 only RPI only data center

*Source:* Pete Stevens.  Mythic Beasts 

* Our Pi platform is experimental with no SLA
* Hosted the backed for the Cambridge Beer Festival including all the customer ratings.
* PiWheels – a build service for Python packages on ARM all natively built on Raspberry Pi
* Many Pi user groups run their websites + meeting pages
* Biggest problem is people firewalling IPv4 – the only thing
  available over IPv4 is the filesystem(something about net booting)
* Problems:
  * Do not use SIIT-DC because IPv4 addresses are too expensive so they need to overload IPv4 addresses and use Proxies.
  * Can not proxy POP3??, IMAP?? and SSH.
  * SSH use different  TCP port and forward.
  * SSL only proxy for 'smart' browsers. (not Windows XP, they do not care) 

### IPv6 deployment in Seznam.cz/T-Mobile

*Source:* Radek Zajic blog:tech.showmax.com

![2020-06-08 13_10_25-RIPE NCC __Educa - Happy-Eyeballs-01](images\2020-06-08 13_10_25-RIPE NCC __Educa - Happy-Eyeballs-01.png)



* Happy Eyeballs a lot of different Head start settings
* It hides a lot of 'blunders' if we mess up IPv6
* IPv6 monitoring is badly done (not a priority??)
* Happy Eyeballs v1 and v2
  * HEv1 has no advantages on NAT64 networks.
  * HEv2, in NAT64 network tries to fix litterals 1.1.1.1
  * HEv2 only on recent Apple OS

### IPv6 migration strategies in the Enterprise

*Source:* Benedikt Stockebrand (Stepladder IT, IPv6 working group)

https://www.stepladder-it.com/bivblog/

* Deutsche Bahn project
* Fundamental strategies
  * Eliminate/Minimize/Isolate/Put a price on IPv4/Expire IPv4
* Router-only networks
  * Disable of separate IPv4 in VLANs
* Server networks
  * VLAN trunks to servers and separate IPv4/IPv6
* Client networks
  * Here dual-stacking realy hurts (many apps)
* Applications and services
  * Upgrading is a nightmare, isolate, terminal services and use IPv4

#### Prepare the basics

* Prepare people
* First do DNS 
* Second do Monitoring IPv6
* Firewalls/security infra
* Migrate an application

### IPv6 ipv6 IETF policy maker

*Source:* Jordi Palet (The IPv6 Company)

* 464XLAT (NAT64 not sufficient)
* RFC8585/RFC8683 (Why NAT64 is not sufficient)

#### 464XLAT explained (is not a NAT64 replacement)

*Remeber/Teasor:*

* Customers in access networks and applications that are IPv4 only can not use
* Has CG-NAT problems remain for instance IPSEC clients
* The CLAT (customer-side translator) can be in a phone or OS??
* Save on paying on IPv4 (IPv4 are shared)
* If you replace CEs then add 464XLAT (CEs need replacement WiFi upgrades etc...)
* 464XLAT will be roled out for an ISP with 25.000.000 subscribers

OS clients support for CLAT:

* Android supports IPv6-only and CLAT by default
* IOS requires the operator's Apple liaison support
* Apple will enable the right APN, IPv6 and CLAT or HEv2
  * Windows 10 has specific CLAT support
  * You can "hack" a few iOS devices with your own profile to override the standard config (useful for a test-bed)

*Explained:*

464XLAT provides a simple and scalable technique for an IPv4 client with a private address to connect to an IPv4 host over an IPv6 network. 464XLAT only suports IPv4 in the client-server model, so it does not support IPv4 peer-to-peer communication or inbound IPv4 connections.

XLAT464 provides the advantages of not having to maintain an IPv4 network for this IPv4 traffic and not having to assign additional public IPv4 addresses.

A customer-side translator (CLAT), which is not a Juniper Networks product, translates the IPv4 packet to IPv6 by embedding the IPv4 source and destination addresses in IPv6 /96 prefixes, and sends the packet over an IPv6 network to the PLAT. The PLAT translates the packet to IPv4, and sends the packet to the IPv4 host over an IPv4 network (see [Figure 1](https://www.juniper.net/documentation/en_US/junos/topics/concept/nat-464xlat.html#fig-464xlat-wireline-addresses)).

Figure 1: 464XLAT Wireline Flow

![464XLAT Wireline Flow](https://www.juniper.net/documentation/images/g043569.png)

The CLAT uses a unique source IPv6 prefix for each end user, and translates the IPv4 source address by embedding it in the IPv6 /96prefix. In [Figure 1](https://www.juniper.net/documentation/en_US/junos/topics/concept/nat-464xlat.html#fig-464xlat-wireline-addresses), the CLAT source IPv6 prefix is 2001:db8:aaaa::/96, and the IPv4 source address 192.168.1.2 is translated to 2001:db8:aaaa::192.168.1.2. The CLAT translates the IPv4 destination address by embedding it in the IPv6 /96 prefix of the PLAT (MX Series router). In [Figure 1](https://www.juniper.net/documentation/en_US/junos/topics/concept/nat-464xlat.html#fig-464xlat-wireline-addresses), the PLAT destination IPv6 prefix is 2001:db8:bbbb::/96, so the CLAT translates the IPv4 destination address 198.51.100.1 to 2001:db8:bbbb::198.51.100.

The CLAT can reside on the end user mobile device in an IPv6-only mobile network, allowing mobile network providers to roll out IPv6 for their users and support IPv4-only applications on mobile devices (see [Figure 2](https://www.juniper.net/documentation/en_US/junos/topics/concept/nat-464xlat.html#fig-464xlat-wireless-addressses)).

Figure 2: 464XLAT Wireless Flow

![464XLAT Wireless Flow](https://www.juniper.net/documentation/images/g043572.png)

To configure the PLAT on the MX Series router, you create a NAT rule that uses the PLAT IPv6 prefix for the destination address and destination prefix and uses the NAT translation type stateful-nat464. For the source address and CLAT prefix in the NAT rule, identify the IPv6 prefix for the CLAT. The NAT rule must specify a NAT pool that the PLAT uses for converting the private IPv4 source address to a public IPv4 address.

## Benefits of 464XLAT

* No need to maintain an IPv4 transit network
* No need to assign additional public IPv4 addresses

### RELATED DOCUMENTATION

* [Configuring 464XLAT Provider-Side Translater for IPv4 Connectivity Across IPv6-Only Network](https://www.juniper.net/documentation/en_US/junos/topics/task/configuration/464xlat-plat-configuring.html)

### Microsoft IT Journey to IPv6

*Source:* Veronika McKillop https://www.youtube.com/watch?v=iFvaqpW4vLA