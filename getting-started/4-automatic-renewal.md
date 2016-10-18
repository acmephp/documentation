---
currentMenu: getting-started-automatic-renewal
---

# Setup automatic renewal with monitoring

An awesome feature coming with Let's Encrypt automation is the possibility of automatically renew your certificates.
Acme PHP was built from the beginning with the idea to be easy to put in a CRON.

## Create the CRON

The easiest part is to create the CRON.

Acme PHP is smart enough to know when to renew a certificate: you don't need to think carefully when to schedule your
CRON task, simply run it every day and Acme PHP will check if the certificate needs renewal.

**Note:** a vast majority of webservers requires a restart or a reload after a certificate update, therefore you
should run your CRON as root. You can also create two separate CRON if you don't want to execute Acme PHP as root.

Here is an example:

```
0 0 * * * php /home/youruser/acmephp.phar request yourdomain.org >/dev/null 2>&1 && service nginx reload
```

You may also want to save the output to a log file (note that monitoring is much more powerful though):

```
0 0 * * * php /home/youruser/acmephp.phar request yourdomain.org > /var/log/acmephp.log && service nginx reload
```

## Configure monitoring

A very powerful feature of Acme PHP is monitoring: the CLI client will warn you when a renewal failed. Different
alerts are available, such as e-mail or Slack.

To enable monitoring, you need to edit the Acme PHP configuration file (`~/.acmephp/acmephp.conf`):

``` yaml
###################################################################
# Monitoring
#
# This section let you configure a simple monitoring mechanism that
# will warn you if an error occurs during a CRON job.
#

monitoring: ~   # Monitoring is disabled by default

# You can enabled it by configuring at least one alert handler.
# You can change the default handler level to decide when to be alerted
# (only when an error occurs or every time the CRON is started).
#monitoring:
#    email:   # Only SMTP(S) support for now
#        to: galopintitouan@gmail.com
#        host: smtp.example.com
#        # port: 25
#        # username: ~
#        # password: ~
#        # encryption: ~
#        # subject: An error occured during Acme PHP CRON renewal
#        # level: error     # By default, only on error for email handler
#
#    slack:
#        token: your_token
#        channel: #general
#        # username: Acme PHP
#        # level: info      # By default, on every CRON for slack handler
```

This configuration is quite straight-forward: simply remove the `monitoring: ~` line, uncomment the monitoring part
and edit it to your needs.

Note that there are two levels for monitoring alert handlers:
- *info* level means that the handler will be triggered at every renewal, even if it succeeded ;
- *error* level means that the handler will be triggered only if an error occurs during renewal ;
