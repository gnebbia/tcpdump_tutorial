
## Lanciare tcpdump senza root

Vediamo la procedura per poter utilizzare tcpdump e altri programmi come ad
esempio scapy (o altri packet crafter) senza privilegi di root.

```sh
 groupadd pcap
```

```sh
 usermod -a -G pcap giuseppe
 # sostituire giuseppe col nome utente
```

```sh
 chgrp pcap /usr/sbin/tcpdump
```

```sh
 chmod 750 /usr/sbin/tcpdump
```

```sh
 setcap cap_net_raw,cap_net_admin=eip /usr/sbin/tcpdump
```

```sh
 ln -s /usr/sbin/tcpdump /usr/local/bin/tcpdump
```

a volte (quindi solo su alcune distro ad esempio su alcune
OpenSUSE) è anche necessario aggiungere in alcune distribuzioni
l'opzione "file_caps=1" come kernel line nel boot manager.

Una volta eseguite queste operazioni, nel caso volessimo ad
esempio usare scapy, quello che dobbiamo fare è assegnare le
capabilities a python e a scapy, con:

```sh
 setcap cap_net_raw=eip /path/to/pythonX.X
```

```sh
 setcap cap_net_raw=eip /path/to/scapy
```

In questo caso abbiamo fornito le capabilities e i permessi piu' in generale
all'interprete python e a scapy, ma la procedura e' generale possiamo
riutilizzarla per qualsiasi altro programma.


## Leggere una cattura remota tcpdump su wireshark

```sh
ssh root@remotesystem 'tcpdump -s0 -c 1000 -nn -w - not port 22' | wireshark -k -i
```
