# OpenVPN server with IPv6 support

This Ansible role will install OpenVPN with support for IPv6 inside the tunnel.

Thanks to Niko at [techgrube.de](https://www.techgrube.de/ueber-mich) for his [blog entry][2] on this matter.


## Quick start

For this example, we assume that

* your server runs Debian
* the public IPv4 of your server is `10.20.30.40`
* the routable IPv6 prefix of your server is `2000:1234:5678:abcd::/64`


```
$ mkdir openvpn-server
$ cd openvpn-server
$ mkdir roles
$ git -C roles clone https://github.com/sniner/ansible-openvpn-server-role.git openvpn-server
$ echo "10.20.30.40" > hosts
$ cat > play.yml <<EOT
- hosts: 10.20.30.40
  vars:
    openvpn_ipv4:
      subnet: "172.16.16.0"
      length: "24"
    openvpn_ipv6:
      prefix: "2000:1234:5678:abcd"
      addon: "cafe"
      length: "80"
  roles:
    - openvpn-server
EOT
```

Edit the files to your liking. After that run the playbook:

```
$ ansible-playbook -i hosts -u root play.yml
```

When it is through, your OpenVPN server is up and running :-)


## More details

* `openvpn_ipv4.subnet`: this has to be a **private** subnet. Clients will receive an IP out of this subnet, which will be NATed to the public IPv4 of your server.
* `openvpn_ipv4.length`: the length of the network prefix of the subnet.
* `openvpn_ipv6.prefix`: has to be the **public** (i.e. routable) IPv6 /64 prefix of your server.
* `openvpn_ipv6.addon`: used to divide the IPv6 address range into two parts. See [OpenVPN wiki][1] on "Splitting a single routable IPv6 netblock".
* `openvpn_ipv6.length`: length of the prefix part, i.e. 64 + length of addon.

Have a look at [vars](./vars/main.yml) for more, especially the PKI part.


## Building client configurations

Login to your server and head to `/etc/openvpn/client`:

```
$ cd /etc/openvpn/client
$ ./build-client NAME
```

This will build the key files for NAME if necessary and emit a file `NAME.ovpn`. Feed it to your OpenVPN client.


## Further informations

To check if your IPv6 tunnel is working as expected, open this link: [test-ipv6.com][3].


## Copyright

Author: Stefan Schönberger <mail@sniner.net>

License: MIT

[1]: https://community.openvpn.net/openvpn/wiki/IPv6
[2]: https://www.techgrube.de/tutorials/openvpn-server-mit-ipv4-und-ipv6
[3]: http://test-ipv6.com/
