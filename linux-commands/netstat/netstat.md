## netstat

### NAME

netstat -- show network status

### SYNOPSIS

> netstat [-AaLlnW] [-f address_family | -p protocol]

> netstat [-gilns] [-v] [-f address_family] [-I interface]

> netstat -i | -I interface [-w wait] [-c queue] [-abdgqRtS]

> netstat -s [-s] [-f address_family | -p protocol] [-w wait]

> netstat -i | -I interface -s [-f address_family | -p protocol]

> netstat -m [-m]

> netstat -r [-Aaln] [-f address_family]

> netstat -rs [-s]

### DESCRIPTION

The netstat command symbolically displays the contents of various network-related data structures.  There are a number of output formats, depending on the options for the information presented.  

The first form of the command displays a list of active sockets for each protocol.  

The second form presents the contents of one of the other network data structures according to the option selected. 

Using the third form, with a wait interval specified, netstat will continuously display the information regarding packet traffic on the configured network interfaces.  

The fourth form displays statistics for the specified protocol or address family. If a wait interval is specified, the protocol information over the last interval seconds will be displayed. 

The fifth form displays per-interface statistics for the specified protocol or address family.  

The sixth form displays mbuf(9) statistics.  

The seventh form displays routing table for the specified address family.  

The eighth form displays routing statistics. 

* A brief description of each state -
  * ESTABLISHED
    * The socket has an established connection.
  * SYN_SENT
    * The socket is actively attempting to establish a connection.
  * SYN_RECV
    * A connection request has been received from the network.
  * FIN_WAIT1
    * The socket is closed, and the connection is shutting down.
  * FIN_WAIT2
    * Connection is closed, and the socket is waiting for  a shutdown from the remote end.
  * TIME_WAIT
    * The socket is waiting after close to handle packets still in the network.
  * CLOSE  
    * The socket is not being used.
  * CLOSE_WAIT
    * The remote end has shut down, waiting for the socket to close.
  * LAST_ACK
    * The remote end has shut down, and the socket is closed.  Waiting for acknowledgement.
  * LISTEN 
    * The  socket is listening for incoming connections.  Such sockets are not included in the output  unless you specify the --listening (-l) or --all (-a) option.
  * CLOSING
    * Both  sockets are shut down but we still don't have all our data sent.
  * UNKNOWN
    * The state of the socket is unknown.

Consider two programs attempting a socket connection (call them a and b). Both set up sockets and transition to the LISTEN state. Then one program (say a) tries to connect to the other (b). a sends a request and enters the SYN_SENT state, and b receives the request and enters the SYN_RECV state. When b acknowledges the request, they enter the ESTABLISHED state, and do their business. Now a couple of things can happen:
* a wishes to close the connection, and enters FIN_WAIT1. b receives the FIN request, sends an ACK (then a enters FIN_WAIT2), enters CLOSE_WAIT, tells a it is closing down and the enters LAST_ACK. Once a acknowledges this (and enters TIME_WAIT), b enters CLOSE. a waits a bit to see if anythings is left, then enters CLOSE.
* a and b have finished their business and decide to close the connection (simultaneous closing). When a is in FIN_WAIT, and instead of receiving an ACK from b, it receives a FIN (as b wishes to close it as well), a enters CLOSING. But there are still some messages to send (the ACK that a is supposed to get for its original FIN), and once this ACK arrives, a enters TIME_WAIT as usual.


### OPTIONS

* -a    
  * With the default display, show the state of all sockets; normally sockets used by server processes are not shown. With the routing table display (option -r, as described below), show protocol-cloned routes (routes generated by a RTF_PRCLONING parent route); normally these routes are not shown.
* -l    
  * Print full IPv6 address.
* -n    
  * Show network addresses as numbers (normally netstat interprets addresses and attempts to display them symbolically).  This option may be used with any of the display formats.
* -f address_family
  * Limit statistics or address control block reports to those of the specified address family.  The following address families are recognized: inet, for AF_INET, inet6, for AF_INET6 and unix, for AF_UNIX.
* -p protocol
  * Show statistics about protocol, which is either a well-known name for a protocol or an alias for it.  Some protocol names and aliases are listed in the file /etc/protocols. The special protocol name ``bdg'' is used to show bridging statistics.  A null response typically means that there are no interesting numbers to report. The program will complain if protocol is unknown or if there is no statistics routine for it.
  
  
### EXAMPLES

Linux netstat syntax

```bash
$ netstat -tulpn | grep LISTEN
```

FreeBSD/MacOS X netstat syntax

```bash
$ netstat -anp tcp | grep LISTEN
tcp46      0 0 *.3283                 *.* LISTEN     
tcp4       0 0 127.0.0.1.29754        *.* LISTEN
```

OpenBSD netstat syntax

```bash
$ netstat -na -f inet | grep LISTEN
$ netstat -nat | grep LISTEN
```