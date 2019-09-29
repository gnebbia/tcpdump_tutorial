
Vediamo ora una lista di filtri che potrebbero essere di uso comune.


## Trovare User Agents HTTP

```sh
tcpdump -vvAls0 | grep 'User-Agent:'
```


## Trovare richieste HTTP in cleartext

```sh
tcpdump -vvAls0 | grep 'GET'
```

Possiamo anche cercare richieste POST attraverso lo stesso filtro
e cambiando la stringa in 'POST'.


## Trovare Header Host di richieste HTTP

```sh
 tcpdump -vvAls0 | grep 'Host:'
```


## Trovare Cookies HTTP

```sh
 tcpdump -vvAls0 | grep 'Set-Cookie|Host:|Cookie:'
```


## Trovare connessioni SSH

```sh
 tcpdump 'tcp[(tcp[12]>>2):4] = 0x5353482D'
```


## Trovare Traffico DNS

```sh
 tcpdump -vvAs0 port 53
```


## Trovare Traffico FTP

```sh
 tcpdump -vvAs0 port ftp or ftp-data
```


## Trovare Traffico NTP

```sh
tcpdump -vvAs0 port 123
```

## Trovare Password in cleartext

```sh
tcpdump port http or port ftp or port smtp or port imap or port pop3 or port telnet -lA | egrep -i -B5 \
'pass=|pwd=|log=|login=|user=|username=|pw=|passw=|passwd= |password=|pass:|user:|username:|password:|login:|pass |user '
```


