# Connections

Most of the protocols you use with curl speak TCP. With TCP, a client such as
curl must first figure out the IP address(es) of the host you want to
communicate with, then connect to it. "Connecting to it" means performing a
TCP protocol handshake.

For ordinary command line usage, operating on a URL, these are details which
are taken care of under the hood, and which you can mostly ignore. But at times
you might find yourself wanting to tweak the specifics…

* [Name resolve tricks](connections/name.md)
* [Connection timeout](connections/timeout.md)
* [Network interface](connections/interface.md)
* [Local port number](connections/local-port.md)
* [Keep alive](connections/keepalive.md)
