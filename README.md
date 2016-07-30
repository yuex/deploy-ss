# deploy-ss

Deploy shadowsocks instance instantly via ansible.

`ssserver` is supervisored by supervisor. And `yum` is assumed. At the mean
time, the responsibility of configuring sshd and firewall etc. falls upon your
shoulder. delploy-ss assumes that you can login as root via port 22 and the
firewall is happy with the ports used by shadowsocks.

# Config

Before use this ansible playbook, you have to

1. create the `hosts` file to name your ss instance, ip or domain name.
2. create the `template/ss.conf.j2` to config your ss instance, port, password,
   authentication method, etc..

Examples are provided as `*.example` files. Have a look at it, modify it
according to your need, and move or copy it without its `.example` suffix.

# Deploy

ansible is required. Then, run

    ansible-playbook deploy.yml -k

If you have properly configured the ssh keys of your ss instance, then `-k` can
be left out.

# sssuper

deploy-ss also renders a cmd named `sssuper` under `/opt/ss/` to ease the
interaction with supervisor

    # start the supervisord
    /opt/ss/sssuper

    # enter the supervisorctl shell
    /opt/ss/sssuper sh

    # other args are all passed to supervisorctl
    /opt/ss/sssuper status
    /opt/ss/sssuper tail -f ss
    /opt/ss/sssuper stop ss

# Under the Hood

supervisor and shadowsocks will be installed to a virtualenv located at
`/opt/ss`.  Configuration files are put under this location with a `.conf`
suffix. Logs are put under `/var/log/ss`.

Thus, to start the `supervisorctl` shell, run

    /opt/ss/bin/supervisorctl -c /opt/ss/supervisor.conf

to rm deploy-ss completely, run

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
