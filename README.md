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

1. Create a file named `.env` and replace the placeholder values. Note that if you're not running other services on the host or change the network type, the HTTP/S ports do not need to be non-standard as below.

    ```plaintext
    CONFIG=/path/to/persistent/volume/pihole/config
    DNSMASQ=/path/to/persistent/volume/pihole/dnsmasq
    HOSTIP=192.168.0.53
    HTTPPORT=2052
    HTTPSPORT=2053
    NETWORKNAME=dnsnet
    PIHOLEINTERNALIP=172.20.53.2
    DOHINTERNALIP=172.20.53.3
    WEBPASSWORD=somethingSecure
    EXTERNALDNS1=1.1.1.2
    EXTERNALDNS2=1.0.0.2
    ```

1. Run with `docker-compose up -d`
1. Test using:

    - macOS/Linux: `dig github.com <host IP address>`
    - Windows: `nslookup github.com <host IP address>`

1. Set your network DNS address to the host IP address, and optionally, set a secondary DNS as the primary external DNS address in case the host is down; note that requests sent directly to the external DNS provider **will not be encrypted**.

## Usage

1. Navigate to `http://<host IP address>:<HTTP port>/admin`, for example, `http://192.168.0.53:2053/admin` to start configuring Pi-hole.

## Credit

Adapted from GitHub user, and Cloudflared container author, [CrazyMax](https://github.com/crazy-max/docker-cloudflared).
