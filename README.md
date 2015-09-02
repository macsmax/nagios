# nagios
various nagios/centreon related scripts

## Plugins

### check_netscaler_lbvserver
This nagios plugin will query the nitro API for given vservers, it will go on warning if the total backends are <=50% and critical thereafter.
The script will create perfdata on it's output and has the option to push the data to statsd, this will enable graphing of data through graphite or statsd driven graphing tools.
Requires: JSON, Net::Statsd
