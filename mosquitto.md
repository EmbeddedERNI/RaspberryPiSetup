mosquitto — an MQTT broker
# Synopsis

mosquitto [-c config file] [ -d | --daemon ] [-p port number] [-v]
# Description

mosquitto is a broker for the MQTT protocol version 3.1.
# Options

-c, --config-file

    Load configuration from a file. If not given, the default values as described in mosquitto.conf(5) are used.
-d, --daemon

    Run mosquitto in the background as a daemon. All other behaviour remains the same.
-p, --port

    Listen on the port specified instead of the default 1883. This acts in addition to the port setting in the config file. May be specified multiple times to open multiple sockets listening on different ports. This socket will be bound to all network interfaces.
-v, --verbose

    Use verbose logging. This is equivalent to setting log_type to all in the configuration file. This overrides and logging options given in the configuration file.

# Configuration

The broker can be configured using a configuration file as described in mosquitto.conf(5) and this is the main point of information for mosquitto. The files required for SSL/TLS support are described in mosquitto-tls(7).
# Broker Status

Clients can find information about the broker by subscribing to topics in the $SYS hierarchy as follows. Topics marked as static are only sent once per client on subscription. All other topics are updated every sys_interval seconds. If sys_interval is 0, then updates are not sent.

Note that if you are using a command line client to interact with the $SYS topics and your shell interprets $ as an environment variable, you need to place the topic in single quotes '$SYS/...' or to escape the dollar symbol: \$SYS/... otherwise the $SYS will be treated as an environment variable.

$SYS/broker/bytes/received

    The total number of bytes received since the broker started.
$SYS/broker/bytes/sent

    The total number of bytes sent since the broker started.
$SYS/broker/clients/connected, $SYS/broker/clients/active (deprecated)

    The number of currently connected clients.
$SYS/broker/clients/expired

    The number of disconnected persistent clients that have been expired and removed through the persistent_client_expiration option.
$SYS/broker/clients/disconnected, $SYS/broker/clients/inactive (deprecated)

    The total number of persistent clients (with clean session disabled) that are registered at the broker but are currently disconnected.
$SYS/broker/clients/maximum

    The maximum number of clients that have been connected to the broker at the same time.
$SYS/broker/clients/total

    The total number of active and inactive clients currently connected and registered on the broker.
$SYS/broker/connection/#

    When bridges are configured to/from the broker, common practice is to provide a status topic that indicates the state of the connection. This is provided within $SYS/broker/connection/ by default. If the value of the topic is 1 the connection is active, if 0 then it is not active. See the Bridges section below for more information on bridges.
$SYS/broker/heap/current size

    The current size of the heap memory in use by mosquitto. Note that this topic may be unavailable depending on compile time options.
$SYS/broker/heap/maximum size

    The largest amount of heap memory used by mosquitto. Note that this topic may be unavailable depending on compile time options.
$SYS/broker/load/connections/+

    The moving average of the number of CONNECT packets received by the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of connections received in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/bytes/received/+

    The moving average of the number of bytes received by the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of bytes received in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/bytes/sent/+

    The moving average of the number of bytes sent by the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of bytes sent in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/messages/received/+

    The moving average of the number of all types of MQTT messages received by the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of messages received in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/messages/sent/+

    The moving average of the number of all types of MQTT messages sent by the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of messages send in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/publish/dropped/+

    The moving average of the number of publish messages dropped by the broker over different time intervals. This shows the rate at which durable clients that are disconnected are losing messages. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of messages dropped in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/publish/received/+

    The moving average of the number of publish messages received by the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of publish messages received in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/publish/sent/+

    The moving average of the number of publish messages sent by the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of publish messages sent in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/load/sockets/+

    The moving average of the number of socket connections opened to the broker over different time intervals. The final "+" of the hierarchy can be 1min, 5min or 15min. The value returned represents the number of socket connections in 1 minute, averaged over 1, 5 or 15 minutes.
$SYS/broker/messages/inflight

    The number of messages with QoS>0 that are awaiting acknowledgments.
$SYS/broker/messages/received

    The total number of messages of any type received since the broker started.
$SYS/broker/messages/sent

    The total number of messages of any type sent since the broker started.
$SYS/broker/messages/stored

    The number of messages currently held in the message store. This includes retained messages and messages queued for durable clients.
$SYS/broker/publish/messages/dropped

    The total number of publish messages that have been dropped due to inflight/queuing limits. See the max_inflight_messages and max_queued_messages options in mosquitto.conf(5) for more information.
$SYS/broker/publish/messages/received

    The total number of PUBLISH messages received since the broker started.
$SYS/broker/publish/messages/sent

    The total number of PUBLISH messages sent since the broker started.
$SYS/broker/retained messages/count

    The total number of retained messages active on the broker.
$SYS/broker/subscriptions/count

    The total number of subscriptions active on the broker.
$SYS/broker/timestamp

    The timestamp at which this particular build of the broker was made. Static.
$SYS/broker/uptime

    The amount of time in seconds the broker has been online.
$SYS/broker/version

    The version of the broker. Static.

# Wildcard Topic Subscriptions

In addition to allowing clients to subscribe to specific topics, mosquitto also allows the use of two wildcards in subscriptions. + is the wildcard used to match a single level of hierarchy. For example, for a topic of "a/b/c/d", the following example subscriptions will match:

    a/b/c/d

    +/b/c/d

    a/+/c/d

    a/+/+/d

    +/+/+/+

The following subscriptions will not match:

    a/b/c

    b/+/c/d

    +/+/+

The second wildcard is # and is used to match all subsequent levels of hierarchy. With a topic of "a/b/c/d", the following example subscriptions will match:

    a/b/c/d

    #

    a/#

    a/b/#

    a/b/c/#

    +/b/c/#

The $SYS hierarchy does not match a subscription of "#". If you want to observe the entire $SYS hierarchy, subscribe to $SYS/#.

Note that the wildcards must be only ever used on their own, so a subscription of "a/b+/c" is not valid use of a wildcard. The # wildcard must only ever be used as the final character of a subscription.
# Bridges

Multiple brokers can be connected together with the bridging functionality. This is useful where it is desirable to share information between locations, but where not all of the information needs to be shared. An example could be where a number of users are running a broker to help record power usage and for a number of other reasons. The power usage could be shared through bridging all of the user brokers to a common broker, allowing the power usage of all users to be collected and compared. The other information would remain local to each broker.

For information on configuring bridges, see mosquitto.conf(5).
# Signals

SIGHUP

    Upon receiving the SIGHUP signal, mosquitto will attempt to reload configuration file data, assuming that the -c argument was provided when mosquitto was started. Not all configuration parameters can be reloaded without restarting. See mosquitto.conf(5) for details.
SIGUSR1

    Upon receiving the SIGUSR1 signal, mosquitto will write the persistence database to disk. This signal is only acted upon if persistence is enabled.
SIGUSR2

    The SIGUSR2 signal causes mosquitto to print out the current subscription tree, along with information about where retained messages exist. This is intended as a testing feature only and may be removed at any time.

# Files

/etc/mosquitto/mosquitto.conf

    Configuration file. See mosquitto.conf(5).
/var/lib/mosquitto/mosquitto.db

    Persistent message data storage location if persist enabled.
/etc/hosts.allow, /etc/hosts.deny

    Host access control via tcp-wrappers as described in hosts_access(5).

