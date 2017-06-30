---
currentMenu: getting-started-configure-webserver
---

# Configure your webserver

You have your certificate, the next step is to configure your webserver to use it.

## Mozilla SSL Configuration Generator

A wonderful tool to generate an SSL configuration suited to your needs is the
[Mozilla SSL Configuration Generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/).

We recommand you to use it as it will generate for you a secure and complete configuration and
you will be able to tweak the configuration for your audience.

The following configuration examples are based on this tool.

## Apache 2.2.32

```
<VirtualHost *:443>
    ...
    SSLEngine on
    SSLCertificateFile      /home/youruser/.acmephp/master/certs/yourdomain.org/cert.pem
    SSLCertificateKeyFile   /home/youruser/.acmephp/master/private/yourdomain.org/private.pem
    SSLCertificateChainFile /home/youruser/.acmephp/master/certs/yourdomain.org/fullchain.pem
    ...
</VirtualHost>
```

## Apache 2.4.18

```
<VirtualHost *:443>
    ...
    SSLEngine on
    SSLCertificateFile      /home/youruser/.acmephp/master/certs/yourdomain.org/fullchain.pem
    SSLCertificateKeyFile   /home/youruser/.acmephp/master/private/yourdomain.org/private.pem
    ...
</VirtualHost>
```

## nginx 1.9.5

```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    ssl_certificate /home/youruser/.acmephp/master/certs/yourdomain.org/fullchain.pem;
    ssl_certificate_key /home/youruser/.acmephp/master/private/yourdomain.org/private.pem;

    ....
}
```

## lighttpd 1.4.37

```
$SERVER["socket"] == ":443" {
    ...
    ssl.pemfile = "/home/youruser/.acmephp/master/certs/yourdomain.org/combined.pem"
    ...
}
```

## haproxy 1.5.14

```
frontend ft_test
    ...
    bind    :443 ssl crt /home/youruser/.acmephp/master/certs/yourdomain.org/combined.pem
    ...
```

---------------------------------------------------------------------

Next: [Configure automatic renewal](/documentation/getting-started/4-automatic-renewal.html)
