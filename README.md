# ansible-nftables
Ansible role installing and configuring nftables

[![ansible lint](https://github.com/aurxl/ansible-nftables/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/aurxl/ansible-nftables/actions/workflows/ansible-lint.yml)
[![Publish role on Ansible Galaxy](https://github.com/aurxl/ansible-nftables/actions/workflows/push_to_galaxy.yml/badge.svg)](https://github.com/aurxl/ansible-nftables/actions/workflows/push_to_galaxy.yml)

## Format
Default values are filled in. Table ip and chain input for example.
```yaml
nftables:
    enable: False
    routing: False
    vars: ""
    tables:
      ip:
        input:
          hook: ""
          priority: 0
          type: filter
          policy: drop
          pre_chain: []
          post_chain: []
          rules: ""
```

## Example confing:
```yaml

nftables:
  enable: true
  routing: false
  vars:
    if_lan: ens18
    ip_lan: 10.1.1.8
    net_lan: 10.1.1.8/24

  tables:
    ip:
      global:
        rules: |
          ct state vmap {established: accept, related: accept, invalid: drop}
          ip protocol icmp accept
      friendzone:
        rules: |  
          reject with icmp type admin-prohibited
      input:
        pre_chain:
          - global
        post_chain:
          - friendzone
        rules: |
          tcp dport 22 ip saddr $net_lan accept
      ingress:
        priority: 1
        hook: input
        policy: drop
        pre_chain:
          - gobal
          - input
        rules: |
          tcp dport {80, 443} accept
      output:
        hook: output
        policy: accept
```
