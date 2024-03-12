```bash
sudo apt update
sudo apt upgrade
sudo apt install haproxy -y
systemctl status haproxy.service
sudo vim /etc/haproxy/haproxy.cfg
systemctl restart haproxy.service
```

```bash
global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon

        # Default SSL material locations
        ca-base /etc/ssl/certs
        crt-base /etc/ssl/private

        # See: https://ssl-config.mozilla.org/#server=haproxy&server-version=2.0.3&config=intermediate
        ssl-default-bind-ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
        ssl-default-bind-ciphersuites TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256
        ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
        errorfile 400 /etc/haproxy/errors/400.http
        errorfile 403 /etc/haproxy/errors/403.http
        errorfile 408 /etc/haproxy/errors/408.http
        errorfile 500 /etc/haproxy/errors/500.http
        errorfile 502 /etc/haproxy/errors/502.http
        errorfile 503 /etc/haproxy/errors/503.http
        errorfile 504 /etc/haproxy/errors/504.http


listen kubernetes-apiserver-https
        bind *:6443
        mode tcp
        option log-health-checks
        timeout client 3h
        timeout server 3h
        server master1 193.23.55.48:6443 check check-ssl verify none inter 10000
        server master2 193.23.55.158:6443 check check-ssl verify none inter 10000
        server master3 193.23.55.158:6443 check check-ssl verify none inter 10000
        balance roundrobin

frontend proxy-http
        mode tcp
        option tcplog
        bind *:80
        use_backend proxy-http

backend proxy-http
        mode tcp
        balance roundrobin
        server worker1 45.156.21.193:80 check
        server worker2 45.156.21.187:80 check
        server worker3 45.156.21.224:80 check

frontend proxy-https
        mode tcp
        option tcplog
        bind *:443
        use_backend proxy-https

backend proxy-https
        mode tcp
        balance roundrobin
        server worker1 45.156.21.193:443 check
        server worker2 45.156.21.187:443 check
        server worker3 45.156.21.224:443 check
```

