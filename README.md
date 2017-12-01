# check-easydb5
Nagios plugin to check whether an easydb5 instance is up.

## mandatory input

Needs a given URL, as first command line argument.

Recommended: use the basic easydb frontend URL in your host conig and add the API /api/v1/settings to it in nagios server config.

See below for a tested example.

## optional input

Warning timeout in seconds:

~~~~
-w 4
~~~~

Critical timeout in seconds:

~~~~
-c 8
~~~~

String to search for in the answer of the easydb.
Example command line option which sets the default:

~~~~
-s db-name
~~~~

## nagios configuration example

On your Nagios server add: (in e.g. /etc/nagios-plugins/config/local/easydb.cfg)

~~~~
define command {
    command_name    check_by_ssh_easydb5
    command_line    /usr/lib/nagios/plugins/check_by_ssh -t 10 -H $HOSTADDRESS$ -C "/usr/local/nagios/libexec/check-easydb5/check_easydb5 '$ARG1$$_HOSTEASYDB5_APIURL$' -w '$_HOSTEASYDB5_WARN$' -c '$_HOSTEASYDB5_CRIT$' -s '$_HOSTEASYDB5_APISTRING$'"
}
~~~~

Set the default values, in this example in /etc/icinga/objects/generic-host_icinga.cfg (icinga is a nagios fork):

~~~~
        _EASYDB5_WARN                   4
        _EASYDB5_CRIT                   8
        _EASYDB5_APIURL                 /api/v1/settings
        _EASYDB5_APISTRING              db-name
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

