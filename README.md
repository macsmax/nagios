# nagios
various nagios/centreon related scripts

## Plugins

### check_netscaler_lbvserver
This nagios plugin will query the Citrix Netscaler nitro API for given vservers.
It will return a warning exit code and output if the total backends are <=50% and critical thereafter.
The script will create perfdata on it's output and has the option to push the data to statsd, this will enable graphing of data through graphite or statsd driven graphing tools.
Multiple vservers can be specified as comma separated value. This will aggregate metrics.
Requires: JSON, Net::Statsd (if you choose to use the statsd option)

Help:
`
$ ./check_netscaler_lbvserver -h
usage: ./check_netscaler_lbvserver -u <username> -p <password> -H <netscaler host> -V <lbvserver> [-s, -d, -S <statsd server:port>]
        -u netscaler username
	-p netscaler password
	-H netscaler address
	-s connect to the netscaler via https (http default)
	-V lbvserver to check. This can be a comma separated list of vservers. If more than one is checked the main metrics will be aggregated.
	-t type of metric to check for warning and critical threshold [TBC]
	-w warning threshold
	-c critical threshold
	-S statsd server [host:port]
	-r statsd extra metric hierarchy eg. add "internal" to push metrics to stats.gauges.netscaler.internal.<lbvserver> rather than stats.gauges.netscaler.lbvserver

	-d debug
        -h Print This help message


Available metrics for single vserver:

curclntconnections - current client connections
deferredreqrate - rate of deferred requests
responsesrate - response rate
requestsrate - requests rate
pktsrecvdrate - received packets rate
requestbytesrate - requests bytes rate
establishedconn - number of established connections
cursrvrconnections - current server connections
totalpktssent - total packets sent
totalpktsrecvd - total packets received
tothits - total hits
hitsrate - hits rate
totalrequests - total requests
totalresponses - total responses
pktssentrate - packets sent rate
deferredreq - number of deferred requests
totalresponsebytes - total response bytes
totalrequestbytes - total requests bytes
vsvrsurgecount - virtual server surge count
responsebytesrate - response byte rate
totspillovers - spillovers count
surgecount - surge count
svcsurgecount - service surge count
invalidrequestresponsedropped - invalid requests droped count
inactsvcs - inactive services

Aggregated metrics for multiple vservers:

totalrequests - total requests
totalresponses - total responses
totalresponsebytes - total response bytes
totalrequestbytes - total requests bytes
establishedconn - number of established connections
`

Example:
`
$ ./check_netscaler_lbvserver -H my-netscaler.com -V  www-kafka
LBVserver OK - LB vserver www-kafka: Status:UP  | g[www-kafka_establishedconn]=0 g[www-kafka_requestsrate]=0 g[www-kafka_responsesrate]=0 g[www-kafka_responsebytesrate]=0B g[www-kafka_requestbytesrate]=0B g[www-kafka_surgecount]=0 g[www-kafka_curclntconnections]=0 g[www-kafka_cursrvrconnections]=0

$ echo $?
0

$ ./check_netscaler_lbvserver -H my-netscaler.com -V  www-test
LBVserver WARNING - LB vserver www-test: Status:UP - WARNING -> servicecount:[50/100]%  IP/Port:10.10.10.1:9981 - Health:[50%] - members:[ 10.10.20.1:9981(DOWN) 10.10.20.2:9981(UP) ] <- WARNING  | g[www-test_establishedconn]=8435 g[www-test_requestsrate]=847 g[www-test_responsesrate]=847 g[www-test_responsebytesrate]=681806B g[www-test_requestbytesrate]=287780B g[www-test_surgecount]=0 g[www-test_curclntconnections]=8435 g[www-test_cursrvrconnections]=3

$ echo $?
1
`
