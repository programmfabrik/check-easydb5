# check-easydb5
Nagios plugin to check whether an easydb5 instance is up.

## mandatory input

Needs a given easydb5 Frontend URL, as first command line argument.

## optional input

Warning timeout in seconds. Default is 2.

~~~~
-w 123
~~~~


Critical timeout in seconds. Default is 8.

~~~~
-c 123
~~~~

## nagios configuration

For example, on your Nagios server add the configuration: (in e.g. /etc/nagios-plugins/config/local/easydb.cfg)

~~~~
define command {
    command_name    check_by_ssh_easydb5
    command_line    /usr/lib/nagios/plugins/check_by_ssh -t 10 -H $HOSTADDRESS$ -C "/usr/local/nagios/libexec/check-easydb5/check_easydb5 '$ARG1$$_HOSTEASYDB5_APISTRING$' -w '$_HOSTEASYDB5_WARN$' -c '$_HOSTEASYDB5_CRIT$'"
}
~~~~

Set the default values, in this example in /etc/icinga/objects/generic-host_icinga.cfg (icinga is a nagios fork):

~~~~
        _EASYDB5_WARN                   2
        _EASYDB5_CRIT                   8
        _EASYDB5_APISTRING              /api/v1/settings
~~~~

Also add a service definition:

~~~~
define service {
        use                             generic-service
        host_name                       foo
        service_description             easydb5 instance
        check_command                   check_by_ssh_easydb5
        }
~~~~

