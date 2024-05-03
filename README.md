# Pi-hole

## Description

This solution provides network-wide ad blocking and local DNS services using Pi-hole.

## Installation

1. If running on Ubuntu, stop systemd-resolved from occupying port 53

    ```bash
    sudo sed -r -i.orig 's/#?DNSStubListener=yes/DNSStubListener=no/g' /etc/systemd/resolved.conf
    sudo sh -c 'rm /etc/resolv.conf && ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf'
    systemctl restart systemd-resolved
    ```

2. Create a Docker network. Here, it's named `pihole` and I'm using "bridge" mode.

    ```bash
    sudo docker network create --driver=bridge pihole
    ```

3. Copy or rename files ending in `.example` and replace the placeholder values. Create paths that don't already exist. Note that if you're not running other services on the host or change the network type, the HTTP/S ports do not need to be non-standard as per the example.

4. If you have split your network into subnets, create a file called `02-Custom.conf` in the dnsmasq path. This tells Pi-hole to use the DHCP server (subnet gateways) for internal name resolution. Use the below as a guide, remembering to change the IP addresses for the subnets and gateways:

    ```conf
    server=/1.168.192.in-addr.arpa/192.168.1.1
    server=/2.168.192.in-addr.arpa/192.168.2.1
    server=/3.168.192.in-addr.arpa/192.168.3.1
    ```

5. Run with `sudo docker compose up -d`

6. Test using:

    - Linux/macOS: `dig github.com <host IP address>`
    - Windows: `nslookup github.com <host IP address>`

7. Navigate to `http://<host IP address>:<HTTP port>/admin`, for example, `http://192.168.0.53:2052/admin` to start configuring Pi-hole.

## Adlists

Description | URL
-- | --
Advertising | `https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts`
Suspicious | `https://v.firebog.net/hosts/static/w3kbl.txt`
Tracking and telemetry | `https://v.firebog.net/hosts/Easyprivacy.txt`
Porn | `https://raw.githubusercontent.com/chadmayfield/my-pihole-blocklists/master/lists/pi_blocklist_porn_all.list`
