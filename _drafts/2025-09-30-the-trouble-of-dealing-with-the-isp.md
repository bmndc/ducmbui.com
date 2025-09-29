---
layout: post
category: blog
---
# the trouble of dealing with the ISP

(Part 2!) I got nervous getting a public IPv4 address from my <abbr title="Internet Service Provider">ISP</abbr>, and keeping my domain pointing to the Pi because the IP address changes all over the place.

This year, I decided to purchase a domain to, maybe, have a nice little corner on the Internet. I chose this .com domain and paid a little over 11 U.S. bucks (around 270.000 VND at the time). At first, I set the domain up to point to my GitHub Pages (and later Sourcehut Pages) because I was scared to embrace the idea of getting a public IP address at the time.

Because of Internet of Things and every Internet-connected appliance having to be assigned a public IP address, IPv4 addresses, the ones with four blocks of 0 through 255, are running out (accounting the 500 million reserved addresses, which leaves us with about 3.7 billion available public-facing IP addresses). To combat this issue*, ISPs around the world have started to put devices behind what are called Carrier-Grade <abbr title="Network Address Translation">NAT</abbr>s. Think of it as a massive router: a group of devices is only assigned a public-facing IP address.

_\*Yes, I know people are embracing the IPv6 address standard, which holds a virtually infinite number of IP addresses to assign to devices._
