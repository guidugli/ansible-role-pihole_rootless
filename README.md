Role Name
=========

This ansible role install pihole as rootless container using podman. This increases the security as it makes pihole to run as unprivileged ID.
This role performs the following actions:
  - Create user to host the container
  - Enable lingering for the user
  - Download container image
  - Create container
  - Setup systemd to automatically start the container
  - Enable podman auto update to automatically update the container

Requirements
------------

No additional requirements.

Role Variables
--------------

**Available variables are listed below, along with default values (see defaults/main.yml):**

    pihole_username: pihole

Username of the user to be created for pihole

    pihole_homedir: "/srv/{{ pihole_username }}"

Path to the pihole homedir (will host all pihole files)

    pihole_systemd_dir: "{{ pihole_homedir }}/.config/systemd/user"

Path to store systemd files for pihole user

    pihole_server_ip: "{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}"

Pihole server IP (mandatory)

    pihole_container_name: pihole-server

Name of the container

    pihole_dns_port: 1153

Non-privileged DNS Port

    pihole_web_port: 1180

Non-privileged Web Port

    pihole_timezone: "America/Sao_Paulo"

Select the timezone of the container

    #pihole_web_password: "{{ vault_web_password }}"

This variable is mandatory. Password to be set for web page. It is recommended to set a variable in a vault and specify it here.

    pihole_reset_password: false

If set to true, it will stop the container to change the web password.
If set to false, it will set the secret if container if stopped or ignore setting it if running

    firewall_type: iptables

Firewall to be configured (only port redirection will be set). Supported are iptables, firewalld and nftables.

    pihole_domain_name: local

Domain name sent by the DHCP server.

    pihole_dnssec: 'false'

Enable DNSSEC support

    pihole_dns_bogus_priv: 'true'

Never forward reverse lookups for private ranges

    pihole_dns_fqdn_required: 'true'

Never forward non-FQDNs

    pihole_enable_dhcp: 'false'

Enable DHCP server. Static DHCP leases can be configured with a custom /etc/dnsmasq.d/04-pihole-static-dhcp.conf

    #pihole_dhcp_start: 192.168.10.100

Start of the range of IP addresses to hand out by the DHCP server (mandatory if DHCP server is enabled).

    #pihole_dhcp_end: 192.168.10.150

End of the range of IP addresses to hand out by the DHCP server (mandatory if DHCP server is enabled).

    #pihole_dhcp_router: 192.168.10.1

Router (gateway) IP address sent by the DHCP server (mandatory if DHCP server is enabled).

    pihole_dhcp_leasetime: 24

DHCP lease time in hours.

    pihole_dhcp_ipv6: 'false'

Enable DHCP server IPv6 support (SLAAC + RA).

    pihole_dhcp_rapid_commit: 'false'

Enable DHCPv4 rapid commit (fast address assignment).

    pihole_virtual_host: "{{ ansible_hostname }}"

What your web server 'virtual host' is, accessing admin through this Hostname/IP allows you to make changes to the whitelist / blacklists in addition to the default 'http://pi.hole/admin/' address

    pihole_ipv6: 'true'

For unraid compatibility, strips out all the IPv6 configuration from DNS/Web services when false.

    pihole_temperature_unit: c

Set preferred temperature unit to c: Celsius, k: Kelvin, or f Fahrenheit units.

    pihole_webuiboxedlayout: boxed

Use boxed layout (helpful when working on large screens)

    pihole_query_logging: 'true'

Enable query logging or not.

    pihole_web_theme: default-light

User interface theme to use. Valid values are default-dark, default-darker, default-light, default-auto, high-contrast, high-contrast-dark, lcars.

    #pihole_cors_hosts: domain.com

List of domains/subdomains on which CORS is allowed. Wildcards are not supported. Eg: pihole_cors_hosts: domain.com,home.domain.com,www.domain.com.

    pihole_custom_cache_size: 10000

Set the cache size for dnsmasq. Useful for increasing the default cache size or to set it to 0. Note that when DNSSEC is "true", then this setting is ignored.

    #pihole_server_dnsip: 192.168.1.1

If the server is using DHCP, this setting will configure DHCP client to add the specified IP as the first nameserver. It is recommended to use this option because slirp4netns is not working locally, and even if it works, the server would loose dns resolution during maintenance of PiHole service.

    #pihole_dnsmasq_user: pihole

Allows changing the user that FTLDNS runs as. Default: pihole, some systems such as Synology NAS may require you to change this to root.

    #pihole_uid: 999

Overrides image's default pihole user id to match a host user id.
IMPORTANT: id must not already be in use inside the container!

    #pihole_gid: 999

Overrides image's default pihole group id to match a host group id
IMPORTANT: id must not already be in use inside the container!

    #pihole_web_uid: 33

Overrides image's default www-data group id to match a host group id
IMPORTANT: id must not already be in use inside the container!
(Make sure it is different to PIHOLE_GID if you are using that, also)

    #pihole_web_gid: 33

Overrides image's default www-data group id to match a host group id
IMPORTANT: id must not already be in use inside the container!
(Make sure it is different to PIHOLE_GID if you are using that, also)

    #pihole_weblogs_stdout: 0

0 logs to defined files, 1 redirect access and error logs to stdout


Dependencies
------------

This role uses guidugli.user role and containers.podman collection.

Example Playbook
----------------

    - name: Playbook to setup Pihole Components
      hosts: phtest
      gather_facts: yes
      vars:
        pihole_server_ip: 192.168.1.123
        firewall_type: nftables
        pihole_web_theme: default-dark
        pihole_dnssec: 'true'
        pihole_web_password: 'Test123'
        pihole_reset_password: false
      tasks:
    
        - name: Run pihole role
          import_role:
            name: guidugli.pihole_rootless

License
-------

MIT / BSD

Author Information
------------------

This role was created in 2023 by Carlos Guidugli.
