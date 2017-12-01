# check-easydb5
This is a Nagios plug-in which checks whether an easydb5 instance is up and responding in a given time frame.

## mandatory input

Needs a given URL, as first command line argument.

Recommended: Use the basic easydb front-end URL in your host configuration and append the API call /api/v1/settings to the URL in the Nagios server configuration. See below for a tested example.

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

## Nagios server configuration example

On your Nagios server add: (in e.g. /etc/nagios-plugins/config/local/easydb.cfg)

~~~~
define command {
    command_name    check_by_ssh_easydb5
    command_line    /usr/lib/nagios/plugins/check_by_ssh -t 10 -H $HOSTADDRESS$ -C "/usr/local/nagios/libexec/check-easydb5/check_easydb5 '$ARG1$$_HOSTEASYDB5_APIURL$' -w '$_HOSTEASYDB5_WARN$' -c '$_HOSTEASYDB5_CRIT$' -s '$_HOSTEASYDB5_APISTRING$'"
}
~~~~

Set the default values, in this example in /etc/icinga/objects/generic-host_icinga.cfg (Icinga is a Nagios fork):

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
        check_command                   check_by_ssh_easydb5!https://uni-atlantis.de
        }
~~~~

Note: This is where the basic easydb front-end URL has to go (in this example and configuration style), separated with a ! in the check_command line, above.

There are other ways to set up the connection between Nagios client and server. But if you followed the style shown here, then also make sure that the nagios user of the Nagios server is able to log into nagios@client without interactive barriers (SSH-key instead of password and the host key has been accepted by confirming with "yes" during a prior connection).

## installation example on a client
On the server where docker for easydb5 is running, but not inside a docker container, do:

~~~~
mkdir -p /usr/local/nagios/libexec
cd /usr/local/nagios/libexec
git clone https://github.com/programmfabrik/check-easydb5
~~~~

It bears repetition to say that Nagios has to be able to connect automatically, either by SSH as in the example above, or by other means.

