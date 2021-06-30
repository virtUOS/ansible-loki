# Ansible Role for Loki and Promtail

Install latest [loki](https://github.com/grafana/loki) and/or [promtail](https://grafana.com/docs/loki/latest/clients/promtail/) versions with [ansible](https://docs.ansible.com/).

## Role Variables

Have a look at the [defaults](defaults/main.yml) to see what variables you can set.
You should set `has_loki` and `has_promtail` to `true` on instances that should have loki and/or promtail installed, respectively.

For loki you can specify your log retention period by setting the variable `loki_retention_deletes` to `true` and
`loki_retention_period` to the desired retention period.

For promtail, as an example config, you can turn on two jobs as an example config by setting either one of `promtail_job_journal` or `promtail_job_nginx` to `true`.

In most cases, however, you would probably want to provide your own config-templates for loki and promtail.
You can achieve this by changing the path to the template files in `loki_config_template` and `promtail_config_template`.

## Example Playbook

If you have a centralised `loki` instance that collects logs from other servers via `promtail`, your `hosts.yml` might look something like this:

```yaml
all:
  children:
    # install promtail on these machines
    servers:
      hosts:
        server[01:10].yourdomain.com:
          has_promtail: true
          loki_host: loki.yourdomain.com
    # install loki here
    loki:
      hosts:
        loki.yourdomain.com:
          has_loki: true
```

Then your playbook might look like this:

```yaml
- hosts:
    - servers
    - loki
  become: true
  roles:
    - loki
```

Of course you can also install promtail and loki on the same machine.

## Development

For development and testing you can use [molecule](https://molecule.readthedocs.io/en/latest/).
With podman as driver you can install it like this – preferably in a virtual environment (if you use docker, substitute `podman` with `docker`):

```bash
pip install "molecule[ansible,podman]"
```

Then you can *create* the test instances, apply the ansible config (*converge*) and *destroy* the test instances with these commands:

```bash
molecule create
molecule converge
molecule destroy
```

If you want to inspect a running test instance use `molecule login --host <instance_name>`, where you replace `<instance_name>` with the desired value.

## License

[MIT](LICENSE)

## Author Information

[virtUOS](https://www.virtuos.uni-osnabrueck.de)
