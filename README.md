This directory contains configuration files for the cloak.
They are copied in the proper place during cloak image creation.
Check `build.sh` for the target paths.


__monitrc__ : monit configuration file.

__nginx.conf__ : nginx configuration file.

__syslog-ng.conf__ : syslog-ng configuration file for cloak host. This sends every
log messages to cloaklog and save them on local host as well.

__logrotate.syslog-ng__ : logrotate configuration for cloak.

__70-persistent-*.rules__ : pre-created rules for the udev service so that we get
consistent device names or to prevent certain devices to be initialized.
Needed so that we get consistent image between boots.

__iptables.conf__ : firewall rules.

__pg_hba.conf, postgresql.conf__ : configurations for the postgresql service.

## Auditor Guidelines

### nginx.conf

The operation of nginx is determined by its configuration file at nginx.conf.  From the SELinux rules, the auditor may determine that the only sensitive file that nginx has access to is the SSL private key.  In particular, SELinux blocks nginx from accessing user data files.  In addition, nginx.conf itself prevents access to user data files (with the "catch all" rule).

In the cloak, nginx operates as a gateway to a variety of processes that handle HTTP requests.  nginx.conf determines which URLs are handled by which processes.  nginx.conf also requires that clients authenticate themselves.  The auditor may test that clients cannot connect without proper authentication credentials.

nginx.conf does not allow for logging of POST data (which is how user data is conveyed).  Specifically, the access_log command specifies the default "combined" log format, which does not include post data (see for example https://www.digitalocean.com/community/tutorials/how-to-configure-logging-and-log-rotation-in-nginx-on-an-ubuntu-vps).


### postgresql.conf

The operation of postgresql is determined by its configuration file at postgresql.conf.  The auditor can verify that user data is stored at the directory `/mnt/crypt/postgresql`, which is on the crypto-disk partition.  Postgresql listens for client applications at TCP port 5432.


