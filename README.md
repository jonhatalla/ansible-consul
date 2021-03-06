# Consul

[![Build Status](https://travis-ci.org/brianshumate/ansible-consul.svg?branch=master)](https://travis-ci.org/brianshumate/ansible-consul)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-brianshumate.consul-blue.svg)](https://galaxy.ansible.com/brianshumate/consul/)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Percentage of issues still open")

This Ansible role installs [Consul](https://consul.io/), including filesystem
structure and server or client configuration.

It can also bootstrap a development or evaluation cluster of 3 server
agents running in a Vagrant and VirtualBox based environment. See
[README_VAGRANT.md](https://github.com/brianshumate/ansible-consul/blob/master/examples/README_VAGRANT.md) and the associated [Vagrantfile](https://github.com/brianshumate/ansible-consul/blob/master/examples/Vagrantfile) for more details about the developer mode setup.

## Requirements

This role requires FreeBSD, or a Debian or RHEL based Linux distribution. It
might work with other software versions, but does work with the following
specific software and versions:

* Ansible: 2.2.2.0
* Vault: 0.7.0
* Debian: 8
* FreeBSD 11
* Ubuntu 16.04

Sorry, there is no planned support at the moment for Windows.

## Role Variables

The role defines variables in `defaults/main.yml` and in the hosts
inventory file (see below):

### `consul_version`

- Version to install
- Default value: *0.8.0*

### `consul_architecture_map`

- Dictionary for translating ansible_architecture to HashiCorp architecture
  naming convention
- Default value: dict

### `consul_architecture`

- System architecture as determined by `{{ consul_architecture_map[ansible_architecture] }}`
- Default value: *amd64*, *arm*, or *arm64* (determined at runtime)

### `consul_os`

- Node operating system name in lowercase representation
- Default value: `{{ ansible_os_family|lower }}`

### `consul_zip_url`

- Consul archive download URL
- Default value: `https://releases.hashicorp.com/consul/{{ consul_version }}/consul_{{ consul_version }}_{{ consul_os }}_{{ consul_architecture }}.zip`

### `consul_checksum_file_url`

- Package SHA256 summaries URL
- Default value: `https://releases.hashicorp.com/consul/{{ consul_version }}/{{ consul_version }}_SHA256SUMS`

### `consul_bin_path`

- Default value: `/usr/local/bin`
- Binary installation path

### `consul_config_path`

- Default value: `/etc/consul.d`
- Configuration file path

### `consul_data_path`

- Default value: `/var/consul`
- Data path

### `consul_log_path`

- Log path
- Default value: `/var/log/consul`

### `consul_user`

- OS user
- Default value: *consul*

### `consul_group`

- OS group
- Default value: *bin*

### `consul_group_name`

- Inventory group name
- Default value: *cluster_nodes*

### `consul_datacenter`

- Datacenter label
- Default value: *dc1*

### `consul_domain`

- Consul domain name
- Default value: *consul*

### `consul_log_level`

- Log level
- Default value: *INFO*

### `consul_syslog_enable`

- Log to syslog
- Default value: *true*

### `consul_iface`

- Consul network interface (can be overridden with `CONSUL_IFACE` environment variable)
- Default value: `eth1`

### `consul_bind_address`

- Bind address
- Default value: *127.0.0.1*

### `consul_dns_bind_address`

- DNS API bind address
- Default value: *127.0.0.1*

### `consul_http_bind_address`

- HTTP API bind address
- Default value: *0.0.0.0*

### `consul_https_bind_address`

- HTTPS API bind address
- Default value: *0.0.0.0*

### `consul_rpc_bind_address`

- RPC bind address
- Default value: *0.0.0.0*

### `consul_ports`

- Port Mappings
- Default value:
 - `rpc`: `8400`
 - `http`: `8500`
 - `https`: `-1`
 - `dns`: `8600`

### `consul_node_name`

- Node name (should not include dots)
- Default value: `{{ inventory_hostname_short }}`

### `consul_recursors`

- List of upstream DNS servers
  See [recursors](https://www.consul.io/docs/agent/options.html#recursors)
- Default value: Empty list

### `consul_bind_address`

- The interface address to bind to
- Default value: dynamic from hosts inventory

### `consul_dnsmasq_enable`

- Whether to install and configure DNS API forwarding on port 53 using DNSMasq
- Default value: *false*

### `consul_iptables_enable`

- Whether to enable iptables rules for DNS forwarding to Consul
- Default value: *false*

### `consul_acl_enable`

- Enable ACLs
- Default value: *false*

### `consul_acl_datacenter`

- ACL authoritative datacenter name
- Default value: *dc1*

### `consul_acl_default_policy`

- Default ACL policy
- Default value: *allow*

### `consul_acl_down_policy`

- Default ACL down policy
- Default value: *allow*

### `consul_acl_master_token`

- ACL master token
 - can be overridden with `CONSUL_ACL_MASTER_TOKEN` environment variable
- Default value: *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL*

### `consul_acl_master_token_display`

- Display generated ACL Master Token
- Default value: *false*

### `consul_acl_replication_token`

- ACL replication token
 - can be overridden with `CONSUL_ACL_REPLICATION_TOKEN` environment variable
- Default value: *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL*

### `consul_tls_enable`

- Enable TLS
- Default value: *false*

### `consul_src_def`

- Default source directory for TLS files
- Default value: `{{ role_path }}/files`

### `consul_src_files`

- User-specified source directory for TLS files
 - can be overridden with `CONSUL_SRC_FILES` environment variable
- Default value: `{{ role_path }}/files`

### `consul_tls_dir`

- Target directory for TLS files
 - can be overridden with `CONSUL_TLS_DIR` environment variable
- Default value: `/etc/consul/ssl`

### `consul_ca_crt`

- CA certificate filename
 - can be overridden with `CONSUL_CA_CRT` environment variable
- Default value: `ca.crt`

### `consul_server_crt`

- Server certificate
 - can be overridden with `CONSUL_SERVER_CRT` environment variable
- Default value: `server.crt`

### `consul_server_key`

- Server key
 - can be overridden with `CONSUL_SERVER_KEY` environment variable
- Default value: `server.key`

### `consul_verify_incoming`

- Verify incoming connections
 - can be overridden with `CONSUL_VERIFY_INCOMING` environment variable
- Default value: *false*

### `consul_verify_outgoing`

- Verify outgoing connections
 - can be overridden with `CONSUL_VERIFY_OUTGOING` environment variable
- Default value: *true*

### `consul_verify_server_hostname`

- Verify server hostname
 - can be overridden with `CONSUL_VERIFY_SERVER_HOSTNAME` environment variable
- Default value: *false*

#### Custom Configuration Section

As Consul loads the configuration from files and directories in lexical order,
typically merging on top of previously parsed configuration files, you may set
custom configurations via `consul_config_custom`, which will be expanded into a file named `config_z_custom.json` within your `consul_config_path` which will
be loaded after all other configuration by default.

An example usage for enabling `telemetry`:

```yaml
  vars:
    consul_config_custom:
      telemetry:
        dogstatsd_addr: "localhost:8125"
        dogstatsd_tags:
          - "security"
          - "compliance"
        disable_hostname: true
```

## Host Inventory Variables

This role also uses a host inventory variable to define the server's role
when forming a cluster. One server should be designated as the bootstrap
server that uses the `bootstrap_expect` configuration directive, and the other
servers will connect to this server. You can also specify *client* as the
role, and Consul will be configured as a client agent instead of a server.

### `consul_node_role`

- Consul server role, one of: *bootstrap*, *server*, or *client*
- Default value: NONE

Here is an example of how the hosts inventory could be defined for a simple
cluster of 3 servers:

```
consul1.local consul_node_role=bootstrap
consul2.local consul_node_role=server
consul3.local consul_node_role=server
```

## OS Distribution Variables

The `consul` binary works on most Linux platforms and is not distribution
specific. However, some distributions require installation of specific OS
packages with different package names.

### `consul_centos_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_centos_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_centos_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_centos_os_packages`

- List of OS packages to install
- Default value: list

### `consul_debian_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_debian_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_debian_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_debian_os_packages`

- List of OS packages to install
- Default value: list

### `consul_redhat_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_redhat_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_redhat_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_redhat_os_packages`

- List of OS packages to install
- Default value: list

### `consul_ubuntu_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_ubuntu_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_ubuntu_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_ubuntu_os_packages`

- List of OS packages to install
- Default value: list

## Dependencies

Ansible requires GNU tar and this role performs some local use of the
unarchive module, so ensure that your system has `gtar` installed and
in the PATH.

If you're on system with a different (i.e. BSD) `tar`, like macOS and you
see odd errors during unarchive tasks, you could be missing `gtar`.

## Example Playbook

Basic installation is possible using the included `site.yml` playbook:

```
ansible-playbook -i hosts site.yml
```

You can also pass variables in using the `--extra-vars` option to the
`ansible-playbook` command:

```
ansible-playbook -i hosts site.yml --extra-vars "consul_datacenter=maui"
```

Be aware that for clustering, the included `site.yml` does the following:

1. Executes consul role (installs Consul and bootstraps cluster)
2. Reconfigures bootstrap node to run without bootstrap-expect setting
3. Restarts bootstrap node

### ACL Support

Basic support for ACLs is included in the role. You can set the environment
variables `CONSUL_ACL_ENABLE` to *true*, and also set the
`CONSUL_ACL_DATACENTER` environment variable to its correct value for your
environment prior to executing your playbook; for example:

```
CONSUL_ACL_ENABLE=true CONSUL_ACL_DATACENTER=maui \
CONSUL_ACL_MASTER_TOKEN_DISPLAY=true ansible-playbook -i uat_hosts aloha.yml
```

If you want the automatically generated ACL Master Token value emitted to
standard out during the play, set the environment variable
`CONSUL_ACL_MASTER_TOKEN_DISPLAY` to *true* as in the above example.

If you want to use existing tokens, set the environment variables
`CONSUL_ACL_MASTER_TOKEN` and `CONSUL_ACL_REPLICATION_TOKEN` as well,
for example:

```
CONSUL_ACL_ENABLE=true CONSUL_ACL_DATACENTER=stjohn \
CONSUL_ACL_MASTER_TOKEN=0815C55B-3AD2-4C1B-BE9B-715CAAE3A4B2 \
CONSUL_ACL_REPLICATION_TOKEN=C609E56E-DD0B-4B99-A0AD-B079252354A0 \
CONSUL_ACL_MASTER_TOKEN_DISPLAY=true ansible-playbook -i uat_hosts sail.yml
```

There are a number of Ansible ACL variables you can override to further
refine your initial ACL setup. They are not all currently picked up from
environment variables, but do have some sensible defaults.

Check `defaults/main.yml` to see how some of he defaults (i.e. tokens)
are automatically generated.

### Dnsmasq DNS Forwarding Support

The role now includes support for [DNS forwarding](https://www.consul.io/docs/guides/forwarding.html) with [Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html).

Enable like this:

```
ansible-playbook -i hosts site.yml --extra-vars "consul_dnsmasq_enable=true"
```

Then, you can query any of the agents via DNS directly via port 53,
for example:

```
dig @consul1.local consul3.node.consul

; <<>> DiG 9.8.3-P1 <<>> @consul1.local consul3.node.consul
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 29196
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;consul3.node.consul.   IN  A

;; ANSWER SECTION:
consul3.node.consul.  0 IN  A 10.1.42.230

;; Query time: 42 msec
;; SERVER: 10.1.42.210#53(10.1.42.210)
;; WHEN: Sun Aug  7 18:06:32 2016
;;
```

### iptables DNS Forwarding Support

This role can also use iptables instead of Dnsmasq for forwarding DNS queries
to Consul. You can enable it like this:

```
ansible-playbook -i hosts site.yml --extra-vars "consul_iptables_enable=true"
```

> Note that iptables forwarding and Dnsmasq forwarding cannot be used
> simultaneously and the execution of the role will stop with error if such
> a configuration is specified.

### TLS Support

You can enable TLS encryption by dropping a CA certificate, server
certificate, and server key into the role's `files` directory.

By default these are named:

- `ca.crt` (can be overridden by {{ consul_ca_crt }})
- `server.crt` (can be overridden by {{ consul_server_crt }})
- `server.key` (can be overridden by {{ consul_server_key }})

Then either set the environment variable `CONSUL_TLS_ENABLE=true` or use the
Ansible variable `consul_tls_enable=true` when launching the role.

### Vagrant and VirtualBox

See `examples/README_VAGRANT.md` for details on quick Vagrant deployments
under VirtualBox for testing, etc.

## License

BSD

## Author Information

[Brian Shumate](http://brianshumate.com)

## Contributors

Special thanks to the folks listed in [CONTRIBUTORS.md](https://github.com/brianshumate/ansible-consul/blob/master/CONTRIBUTORS.md) for their
contributions to this project.

Contributions are welcome, provided that you can agree to the terms outlined
in [CONTRIBUTING.md](https://github.com/brianshumate/ansible-consul/blob/master/CONTRIBUTING.md)
