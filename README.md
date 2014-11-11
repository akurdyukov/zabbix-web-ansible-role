This is an [Ansible](http://www.ansible.com/home) role for installing
[Zabbix web](https://www.zabbix.com/documentation/2.0/manual/web_interface)
using [nginx](http://wiki.nginx.org/Main) as a reverse proxy and
[PHP-FPM](http://php-fpm.org/) for
[FastCGI](http://en.wikipedia.org/wiki/FastCGI).

You may also be interested in:

* an Ansible role for
  [Zabbix agent](https://github.com/dhruvbansal/zabbix-agent-ansible-role)
* an Ansible role for the
  [Zabbix server](https://github.com/dhruvbansal/zabbix-server-ansible-role)
* an Ansible role for
  [nginx](https://github.com/dhruvbansal/nginx-ansible-role)
* an Ansible role for
  [php-fpm](https://github.com/dhruvbansal/php-fpm-ansible-role)

# What it Does

## Assumptions

This role explicitly assumes you have already installed nginx and
PHP-FPM.

It also (more implicitly) assumes you have correctly installed Zabbix
server and (at least one) agent.  Without these pieces there will be
little data in Zabbix web to see.  (Though, honestly, it is often the
case that only by seeing partially correct data through Zabbix web can
Zabbix server & agents be properly configured...).

## Software

Installs the `zabbix-frontend-php` package.  This unfortunately forces
the installation of
[Apache 2](http://en.wikipedia.org/wiki/Apache_HTTP_Server) but this
service is turned off immediately.

## Configuration & Logging

Creates the files:

* `/etc/zabbix/zabbix.conf.php` -- configuration file for Zabbix web PHP application
* `/etc/php5/fpm/pool.conf/zabbix-web.conf` -- configuration file for pool of PHP-FPM resources
* `/etc/logstash/conf.d/zabbix-web.conf` -- input file for logstash
* `/var/log/zabbix-web` -- log directory
* `/etc/nginx/sites-available/zabbix-web.conf`-- nginx configuration file

## Services

Adds a virtual host for nginx running over SSL on port 443 reverse
proxying a newly created pool of PHP-FPM resources.

# Usage

Use the role in a playbook like this:

```yaml
- hosts: all
  roles:
    - zabbix-server
```

Set the `zabbix_server_use_logstash` to `false` to skip the logstash
configuration.

## Variables

The following variables are exposed for configuration

* `mysql_root_password` -- the password for the MySQL root user
* `zabbix_database_host` -- host for the database
* `zabbix_database_port` -- port for the database
* `zabbix_database_username` -- username to connect to the database as
* `zabbix_database_password` -- password to connect to the database with
* `zabbix_database_name` -- name of the database
* `zabbix_server_use_logstash` -- set to `false` to skip logstash configuration

You will also want to set the following PHP-FPM variables (Zabbix web
is aggressive...):

```yaml
php_fpm_time_zone: UTC
php_fpm_memory_limit: 128M
php_fpm_post_max_size: 16M
php_fpm_upload_max_file_size: 2M
php_fpm_max_execution_time: 300
php_fpm_max_input_time: 300

```
