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

For example, on your Nagios server add the configuration:

~~~~
define command {
    command_name    check_easydb5
    command_line    /usr/lib/nagios/plugins/check_by_ssh -t 10 -H $HOSTADDRESS$ -C "/usr/local/nagios/libexec/check_easydb5 '$ARG1$' -w 2 -c 8"
}
~~~~

Also add a service definition:

~~~~
define service {
        use                             generic-service
        host_name                       foo
        service_description             easydb5 instance
        check_command                   check_easydb5
        }
~~~~

