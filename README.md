# Prometheus monitoring demo site

[![Build Status](https://circleci.com/gh/prometheus/demo-site.svg?style=svg)](https://circleci.com/gh/prometheus/demo-site)
[![License](https://img.shields.io/badge/license-Apache%20License-brightgreen.svg)](https://opensource.org/licenses/Apache-2.0)
[![IRC](https://img.shields.io/badge/chat-on%20freenode-blue.svg)](http://webchat.freenode.net/?channels=prometheus)

## [demo.do.prometheus.io](https://demo.do.prometheus.io)

This repository provides a demo site for [prometheus](https://github.com/prometheus/prometheus), [alertmanager](https://github.com/prometheus/alertmanager), prometheus exporters, and [grafana](https://github.com/grafana/grafana).
The site is provisioned with Ansible running every day and on all commits to the master branch. Everything is fully automated with a Travis CI pipeline. If you want to check the `ansible-playbook` output, go to the [last build](https://travis-ci.org/prometheus/demo-site).

Have a look at configuration files in the [group_vars/](group_vars/) directory.

## Applications

All applications should be running on their default ports.

| App name          | Address (HTTP)                                       | Address (HTTPS)                                           |
|-------------------|------------------------------------------------------|-----------------------------------------------------------|
| node_exporter     | [demo.do.prometheus.io:9100][node_exporter_http]     | [node.demo.do.prometheus.io][node_exporter_https]         |
| prometheus        | [demo.do.prometheus.io:9090][prometheus_http]        | [prometheus.demo.do.prometheus.io][prometheus_https]      |
| alertmanager      | [demo.do.prometheus.io:9093][alertmanager_http]      | [alertmanager.demo.do.prometheus.io][alertmanager_https]  |
| grafana           | [demo.do.prometheus.io:3000][grafana_http]           | [grafana.demo.do.prometheus.io][grafana_https]            |

## Important notice

Before running, Go (golang) is required to be installed on the deployer machine (necessary to install random_exporter).

Most services can be accessed in two ways (links in [Applications](#applications) section). As an example, prometheus can be accessed via:
  - **http**://demo.do.prometheus.io:9090 - default way
  - **https**://prometheus.demo.do.prometheus.io - workaround which in the background communicates with Prometheus via insecure, "default" channel mentioned above

This workaround was needed to solve issue [cloudalchemy/demo-site#13](https://github.com/cloudalchemy/demo-site/issues/13).

## Run yourself

You can easily run this setup yourself without much knowledge of how any part of this works. You just need to follow these steps:

#### 1. Change Ansible inventory

First of all, you need to configure your inventory, ours is located in the [`hosts`](hosts) file. Here you set up your target hosts by changing the value of the `ansible_host` variable. Also, here you can exclude parts of this demo site. For example, if you don't need our website, you can just remove this part:

```ini
[web]
demo
```

Similarly, you can exclude Grafana, Prometheus, or other components as needed.

#### 2. Change passwords

For security measures, we encrypted some of our passwords, but it's easy to use your own! You can do it by replacing the file located at [`group_vars/grafana/vault`](group_vars/grafana/vault) with the following content:

```yaml
vault_grafana_password: <<INSERT_YOUR_GRAFANA_PASSWORD>>
```

#### 3. Download the 'random' exporter binary

You will have to manually run the `go` command to download & copy the [`random`](https://github.com/prometheus/client_golang/tree/master/examples/random) exporter binary to the [`playbooks/files`](playbooks/files) directory.

- The binary will be downloaded at the `GOPATH` location. The value of `GOPATH` can be found by running the `go env | grep GOPATH` command on your system.

```bash
go get -u github.com/prometheus/client_golang/examples/random
cp <GOPATH>/bin/random /path/to/demo-site/playbooks/files/
```

#### 4. Run as usual Ansible playbook

```bash
# Download roles
ansible-galaxy install -r roles/requirements.yml

# Run playbook
ansible-playbook site.yml
# or when using vault encrypted variables
ansible-playbook --vault-id @prompt site.yml
```

## Deployment

This demo site is deployed using [Cloud Alchemy](https://github.com/cloudalchemy) Ansible roles.

[![DigitalOcean](https://snapshooter.io/powered_by_digital_ocean.png)](https://digitalocean.com)



[node_exporter_http]: http://demo.do.prometheus.io:9100
[node_exporter_https]: https://node.demo.do.prometheus.io

[prometheus_http]: http://demo.do.prometheus.io:9090
[prometheus_https]: https://prometheus.demo.do.prometheus.io

[alertmanager_http]: http://demo.do.prometheus.io:9093
[alertmanager_https]: https://alertmanager.demo.do.prometheus.io

[grafana_http]: http://demo.do.prometheus.io:3000
[grafana_https]: https://grafana.demo.do.prometheus.io
