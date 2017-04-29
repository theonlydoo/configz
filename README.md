configz
=======

Yet another ansible's playbook repository

roles
======

* Common
 * provides **common** configuration
 * https://github.com/nojhan/liquidprompt <3
* Update
 * allow install all update on  hosts (tag normal)
 * allow update specific packages from list (tags packages)
   * use host_vars, group_vars or default vars to update packages list
* Wallabag
 * provides **Wallabag** configuration
 * Imported with <3 from https://github.com/al3x/sovereign/
 * **Not yet READY**
* Prosody
 * Provides XMPP (Jabber) server
 * Imported with <3 from https://github.com/al3x/sovereign/
 * **Not yet READY**
* IRCBouncer
 * Provides a ZNC Config
 * Imported with <3 from https://github.com/al3x/sovereign/
* Mail
 * provides a complete **mail** server for a given domain name and the vdomain capability for other domains.
 * **Note** : This role starts in order : common, mariadb, and mail. If you don't want one of them, please comment out.
 * **Note2** : If you already have a SQL server, **it wont erase the original config**, but it needs a ``~/.my.cnf``.
 * **TODO** :
     * Razor/Pyzor
     * Roundcube
     * Simplify template copy
     * Postgrey
* MariaDB
 * provides a lambda **MariaDB** server peered on ``127.0.0.1:3306`` with ``root`` MySQL password on ``~/.my.cnf``
* ownCloud
 * provides a simple instance of **ownCloud**, with ``NGINX, PHP5-FPM, and MariaDB``
* xymon-client and xymon-server
 * https://www.xymon.com/
 * Provide installation of xymon server and xymon client monitoring system
 * Available for Debian (6 to 8) and Centos (6 to 7). **WARN** : xymon-server only for Debian (Centos dependencies are really hard to automate)
 * Configure apache for xymon-server
 * Configure xymon client and add the client in xymon server configuration to allow fetch data
 * **Note** : Using xymon-client tag/role needs a working xymon-server (whenever the server was installed with the playbook or not)
 * Cloud be (theoretically, to be tested) used to update xymon server binaries to last stable release
* ovzdb
 * http://projets.developpeur-neurasthenique.fr/projects/openvz-diff-backups
 * Install openvz-diff-backup to an openvz host to backup container
 * enable update of openvz-diff-backup thanks to 0.9.4 version
 * enable backup AND upload feature via cron
 * enable purge feature via cron
 * enable customization of configuration file
 * use standard installation method (conf in /etc, link binary to /usr/local/bin)
 * provide bonus hook to create files when problems occurs (additionnally to send emails), allowing monitoring with standard tool (ie xymon and else)
 * Possibility to use beta version in file directory: hard coded for the moment, name the file openvz-diff-backups_v0.9.8-beta.tar.gz and use -e beta=true on command line
* rudder-node
 * https://www.rudder-project.org
 * allow to configure a debian/ubuntu rudder node to report to a rudder server
 * you need a working rudder-server (https://www.rudder-project.org/doc-4.1/_install_rudder_server.html)
 * use rudder_server variable to configure your rudderserver IP (rudder advice to use IP addresses instead of DNS)

example host file
=====

```yaml

---
admin_ssh_keys: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDZXK3ufonx+zNQ1x6cSWuUWckB/xf9sKZ+mRgY5SPXzqrxSkqNSmr9JQ6xzvhxKEVcFWsi50op1WWtRo3HG3p3+EHKXeCyzt5QnczDlVOoQbB8kgI0byKcvXux1inL4/Q4DbVLUbDFnynD/C5aAyYMYePahMxR+AQr60DD+7Ty6pcEVih1wwHIlxWziY1EF6sEzQwz/PiTxWIZkKHl/WPGagS9Pp/5nQfdZy0AS/JqbzNyMEg51+XedADuqseV4GXDzrzDYLJXJFv1PFVJxRWLrjChKrUMqyszUySkZMr5YSPXlsV0bi+0xivYEsXvIkLORV96JTZosYbV+0aFKDPv root@debian

default_packages_debian: htop

description: machine test

# NTP
ntp_servers:
  - 0.pool.ntp.org
  - 1.pool.ntp.org
  - 2.pool.ntp.org
disable_ipv6: true

# Update
deb_packages_to_update:
  - apache2

centos_packages_to_update:
  - httpd

# Mail
domain: test.net

# MariaDB
mariadb_version: 10.0
mysql_root_password: changeme
mysql_host: localhost

# ircbouncer
znc_version: 1.4
irc_nick: (required)
irc_ident: (required)
irc_realname: (required)
irc_quitmsg: (required)
irc_password_hash: (required) # http://wiki.znc.in/Configuration#Pass
irc_password_salt: (required) # http://wiki.znc.in/Configuration#Pass
irc_timezone: "Europe/Paris" #Example: "Europe/Paris"
network_address: irc.my.network.net
network_port: 6697
network_channel: 1337Chan

# xmpp
prosody_admin: "admin@test.net"
prosody_virtual_domain: "test.net"
prosody_accounts: admin@test.net

#Wallabag
wallabag_version: 1.8.1
wallabag_domain: "read.{{ domain }}"
wallabag_salt: (required)
wallabag_db_username: wallabag
wallabag_db_password: (required)
wallabag_db_database: wallabag


#xymon
xymon_server: yyy.yyy.yyy.yyy # server IP address (mandatory)
xymon_htname: admin # server user for webinterface use
xymon_htpasswd: mysecurepasswd # server password for webinterface use
## xymon per client configuration (ie usually done in host_var)##
monitoring_file: dns ## Where to store the host in hosts.d xymon server directory (optionnal)
monitoring_section: dns ## Name of the page to use in xymon server webpage tree view (optionnal)
monitoring_ip: xxx.xxx.xxx.xxx ## IP address of the client to add in server (mandatory)
xymon_checks: "#" ## Checks to use for this client. Default '#' do a simple ping check

#ovzdb
## You can duplicate backup locally and remotely
## by using openvz host as backup_server and
## remote server as upload_server
## I advice to customize cron hour to have
## backup, then purge, then upload
backup_server: xxx.xxx.xxx.xxx
backup_dir: "/var/lib/vz/backups/OpenVZ/"
backup_minute: 10
backup_hour: 02
purge_minute: 10
purge_hour: 03
upload_server: yyy.yyy.yyy.yyy
upload_dir: "/var/lib/vz/backups/OpenVZ/"
upload_minute: 10
upload_hour: 05
admin_email: "your_email@example.com"

# rudder-node
rudder_server: 192.168.0.100
# vim: set textwidth=0 ft=yaml:

```
