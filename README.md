# deploy-ss

Deploy new ss instance via ansible. `ssserver` is supervisored by supervisor.
And `yum` is assumed.

NOTE: you have to config the ss instance first, like sshd and config firewalld
etc. `delploy-ss` assumes that you login as `root` via port 22 and the firewall
has already been configured for `shadowsocks`.

# Config

Before use this ansible playbook, you have to

1. create the `hosts` file to config the ip or domain name of your ss instance
2. create the `template/ss.conf.j2` to config your ss instance

Examples are provided as `*.example` files. You can have a look at it, modify
it according to your need, and then move or copy it without its `*.example`
suffix.

# Deploy

`ansible` is required. Then, execute

    ansible-playbook deploy.yml -k

If you have already upload your pubkey to the ss instance, then `-k` should be
leave out.

# sssuper

`deploy-ss` also renders a cmd named `sssuper` to `/opt/ss/` to ease the
interaction with `supervisor`

    # start the supervisord
    /opt/sssuper

    # enter the supervisorctl shell
    /opt/sssuper sh

    # other args are all passed to supervisorctl
    /opt/sssuper status
    /opt/sssuper tail -f ss
    /opt/sssuper stop ss

# Under the Hood

`supervisor` and `shadowsocks` will be installed to a virtualenv located at
`/opt/ss`.  Configuration files will be put under this location with a `.conf`
suffix.  Logs will be put under `/var/log/ss`.

Thus, to start the shell of `supervisorctl`, run

    /opt/ss/bin/supervisorctl -c /opt/ss/supervisor.conf

to rm `deploy-ss` completely, run

    rm -rf /opt/ss /var/log/ss

# FAQ

## firewall

Centos 7 used firewalld by default. It can be configured by following cmds to
enable specific ports

    firewall-cmd --zone=public --add-port=26892/tcp --permanent
    firewall-cmd --reload

## inet_http_server

By default, `supervisor.conf` uses `unix_http_server`. If you want to enable
the inet HTTP server, you need to modify `template/supervisor.conf.j2`. It can
enable you to use `supervisorctl` remotely. But only user/pass authentication
is provided. This is not safe to be used over the Internet.

Keep it simple but no naive.
