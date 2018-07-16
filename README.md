[![Build Status](https://travis-ci.org/while-true-do/ansible-role-certbot.svg?branch=master)](https://travis-ci.org/while-true-do/ansible-role-certbot)

# Ansible Role: certbot
| A role to install certbot and create SSL certificates via letsencrypt.org

- installs certbot
- creates certificates for given domains
- will stop/start daemons

This role does _not_ cover opening firewall ports for the handshake. Currently only  `create_method = standalone` and `renew_method = cron` are covered, but the role is prepared for implementation of other methods in the future.

## Disclaimer

Due to some major issues, currently `create_challenge = tls-sni` is not supported from letsencrypt.org.

- [link 1](https://community.letsencrypt.org/t/2018-01-09-issue-with-tls-sni-01-and-shared-hosting-infrastructure/49996)
- [link 2](https://community.letsencrypt.org/t/2018-01-11-update-regarding-acme-tls-sni-and-shared-hosting-infrastructure/50188)
- [link 3](https://news.ycombinator.com/item?id=16112237)

## Motivation

[Certbot](https://certbot.eff.org/) is a tool to issue SSL/TLS certificates from [letsencrypt.org](https://letsencrypt.org/). Having a SSL/TLS certificate is a one of the major things to consider for any Webserver. With letsencrypt you will get one free.

## Installation

Install from [Ansible Galaxy](https://galaxy.ansible.com/while_true_do/certbot)

```
ansible-galaxy install while_true_do.certbot
```

Install from [Github](https://github.com/while-true-do/ansible-role-certbot)

```
git clone https://github.com/while-true-do/ansible-role-certbot.git while_true_do.certbot
```

## Requirements

Used Modules:

-  [package_module](http://docs.ansible.com/ansible/latest/package_module.html)
-  [include_tasks_module](https://docs.ansible.com/ansible/2.4/include_tasks_module.html)
-  [stat_module](http://docs.ansible.com/ansible/latest/stat_module.html)
-  [service_module](http://docs.ansible.com/ansible/latest/service_module.html)
-  [cron_module](http://docs.ansible.com/ansible/latest/cron_module.html)
-  [command_module](http://docs.ansible.com/ansible/latest/command_module.html)

## Dependencies

[while_true_do.repo_epel](https://galaxy.ansible.com/while_true_do/repo_epel/) is needed to get the certbot packages and dependencies.

```
ansible-galaxy install -r requirements.yml
```

## Role Variables

Below you can find all variables used. You need to set some of the variables manually.

Mandatory Variables:

You must set a valid contact.
```
wtd_certbot_create_contact: "your@email.address.here"
```
You must configure daemons, which may use the ports already.
```
wtd_certbot_create_stop_daemons: ["httpd"]
```
You should configure domains, which should be created. Default is the inventory Hostname.
```
wtd_certbot_create_domains: ["foo.domain.com","bar.domain.com"]
```

Default Variables:

```yaml
---
# defaults/main.yml for certbot
#
# You can set the state to ["present"|"absent"|"latest"]
wtd_certbot_state: "present"
wtd_certbot_packages: ["certbot"]

# Here you can define, if/how a certificate is created
wtd_certbot_create: True
wtd_certbot_create_method: "standalone"
wtd_certbot_create_domains: ["{{ inventory_hostname }}"]
# You have to provide a valid mail address
wtd_certbot_create_contact: ""

# Currently only tls-sni and http are supported.
# You have to assure that the firewall port is openend.
# http = 80/tcp, tls-sni = 443/tcp.
wtd_certbot_create_challenges: "http"

# Standalone Method needs a port to create a cert.
# Therefore all other daemons, which may use this
# port must be stopped during creation.
wtd_certbot_create_stop_daemons: []

# Here you have to define, if/how the renewal will happen
# According to letsencrypt, 2 times a day is a good idea.
wtd_certbot_renew: True
wtd_certbot_renew_method: "cron"
wtd_certbot_renew_hour: "0,12"
wtd_certbot_renew_minute: 0
```

## Example Playbook

Simple Example:

```yaml
- hosts: servers 
  roles:
    - { role: while_true_do.certbot, wtd_certbot_create_contact: me@cool.org }
```

## Testing

All tests are located in [test directory](./tests/).

Basic testing:

```
bash ./tests/test-spelling.sh
bash ./tests/test-ansible.sh
```

## Contribute / Bugs

Thank you so much for considering to contribute. Every contribution helps us.
We are really happy, when somebody is joining the hard work. Please have a look 
at the links first.

-   [Contribution Guidelines](./docs/CONTRIBUTING.md)
-   [Create an issue or Request](https://github.com/while-true-do/ansible-role-certbot/issues)
-   [See who was contributing already](https://github.com/while-true-do/ansible-role-certbot/graphs/contributors)

## License

This work is licensed under a [BSD License](https://opensource.org/licenses/BSD-3-Clause).

## Author Information

Blog: [blog.while-true-do.org](https://blog.while-true-do.org)

Mail: [hello@while-true-do.org](mailto:hello@while-true-do.org)
