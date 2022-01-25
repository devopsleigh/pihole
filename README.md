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

2. Copy or rename `.env.example` to `.env` and replace the placeholder values. Create paths that don't already exist. Note that if you're not running other services on the host or change the network type, the HTTP/S ports do not need to be non-standard as per the example.

3. If you have split your network into subnets, create a file called `02-Custom.conf` in the dnsmasq path. This tells Pi-hole to use the DHCP server (subnet gateways) for internal name resolution. Use the below as a guide, remembering to change the IP addresses for the subnets and gateways:

    ```conf
    server=/1.168.192.in-addr.arpa/192.168.1.1
    server=/2.168.192.in-addr.arpa/192.168.2.1
    server=/3.168.192.in-addr.arpa/192.168.3.1
    ```

4. Run with `sudo docker-compose up -d`
5. Test using:

    - macOS/Linux: `dig github.com <host IP address>`
    - Windows: `nslookup github.com <host IP address>`

6. Set your network DNS address to the host IP address, and optionally, set a secondary DNS as the primary external DNS address in case the host is down; note that requests sent directly to the external DNS provider **will not be encrypted**.

## Usage

1. Navigate to `http://<host IP address>:<HTTP port>/admin`, for example, `http://192.168.0.53:2053/admin` to start configuring Pi-hole.

