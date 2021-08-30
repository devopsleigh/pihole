# Pi-hole with DoH

## Description

This solution provides:

- Network-wide ad blocking and local DNS services using Pi-hole
- Encrypted DNS via DNS over HTTPS (DoH) using Cloudflared

Both Pi-hole and Cloudflared are defined within the same docker-compose file, and run within a Docker network.

## Installation

1. Create an external Docker network. Here, it's named `dnsnet`. Pick whatever name and address space suits your implementation.

    ```bash
    sudo docker network create --driver=bridge --subnet=172.20.53.0/29 --gateway=172.20.53.1 dnsnet
    ```

1. Create a file named `.env` and replace the placeholder values. Create paths that don't already exist. Note that if you're not running other services on the host or change the network type, the HTTP/S ports do not need to be non-standard as below.

    ```conf
    CONFIG=/path/to/persistent/volume/pihole/config
    DNSMASQ=/path/to/persistent/volume/pihole/dnsmasq
    HOSTIP=192.168.0.53
    HTTPPORT=2052
    HTTPSPORT=2053
    NETWORKNAME=dnsnet
    PIHOLEINTERNALIP=172.20.53.2
    DOHINTERNALIP=172.20.53.3
    WEBPASSWORD=somethingSecure
    EXTERNALDNS1=1.1.1.1
    EXTERNALDNS2=1.0.0.1
    ```

1. If you have split your network into subnets, create a file called `02-Custom.conf` in the dnsmasq path. This tells Pi-hole to use the DHCP server (subnet gateways) for internal name resolution. Use the below as a guide, remembering to change the IP addresses for the subnets and gateways:

    ```conf
    server=/10.168.192.in-addr.arpa/192.168.10.1
    server=/20.168.192.in-addr.arpa/192.168.20.1
    server=/30.168.192.in-addr.arpa/192.168.30.1
    ```

1. Run with `sudo docker-compose up -d`
1. Test using:

    - macOS/Linux: `dig github.com <host IP address>`
    - Windows: `nslookup github.com <host IP address>`

1. Set your network DNS address to the host IP address, and optionally, set a secondary DNS as the primary external DNS address in case the host is down; note that requests sent directly to the external DNS provider **will not be encrypted**.

## Usage

1. Navigate to `http://<host IP address>:<HTTP port>/admin`, for example, `http://192.168.0.53:2053/admin` to start configuring Pi-hole.

## Credit

Adapted from GitHub user, and Cloudflared container author, [CrazyMax](https://github.com/crazy-max/docker-cloudflared).
