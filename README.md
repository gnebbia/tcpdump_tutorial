# Tcpdump Tutorial

## Basics

Tcpdump costituisce un'ottima alternativa a wireshark, molto più 
frequente tra chi si occupa di sicurezza informatica, essendo un 
programma che offre molte possibilità, vedremo solo alcuni 
comandi d'esempio, e andremo ad arricchire mano a mano il lancio 
del comando:

```sh
 tcpdump -D 
 # mostra l'elenco delle interfacce disponibili per catturare pacchetti
```

```sh
 tcpdump -i any 
 # cattura pacchetti su qualsiasi interfaccia di rete, il flag "-i" è 
 # utilizzato per indicare l'interfaccia di rete
```

```sh
 tcpdump -i wlan0 
 # cattura pacchetti sull'interfaccia di rete wlan0
```

```sh
 tcpdump -i wlan0 -n 
 # non risolve gli hostname, mostrando gli indirizzi IP grazie al flag "-n"
```

```sh
 tcpdump -i wlan0 -nn 
 # non risolve gli hostname e i nomi dei servizi associati alle porte più comuni
```

```sh
 tcpdump -i wlan0 -nn -q 
 # è più quiet (quindi meno verbose), con il flag "-q"
```

```sh
 tcpdump -i wlan0 -nn -q -t 
 # non stampa il timestamp col flag "-t"
```

```sh
 tcpdump -i wlan0 -nn -q -tttt 
 # stampa un timestamp più completo contenente anche la data utilizzando 
 # il flag "-tttt"
```

```sh
 tcpdump -i wlan0 -nn -q -X 
 # è conciso sulle informazioni di testa del pacchetto "-q" ma 
 # stampa anche il contenuto di ogni pacchetto "-X"
```

```sh
 tcpdump -i wlan0 -nn -X 
 # stampa informazioni sul pacchetto di default + contenuto
```
```sh
 tcpdump -i wlan0 -nn -v --number
 # mostra più informazioni, il flag "-v" sta per verbose, e possiamo essere 
 # ancora più verbose utilizzando "-vv" o "-vvv", a differenza di 
 # quante informazioni vogliamo sui pacchetti, numera i pacchetti
```

```sh
 tcpdump -i wlan0 -nn -v -e --number
 # in questo modo mostriamo anche le informazioni sull'header ethernet, 
 # utile ad esempio per visualizzare indirizzi MAC, inoltre numera i pacchetti
```

```sh
 tcpdump -i wlan0 -nn -v -e -X -c10 
 # cattura pacchetti includendo più informazioni "-v" 
 # (incluse quelle relative all'header ethernet "-e"), 
 # cattura anche il contenuto dei pacchetti "-X" ma cattura solo 
 # 10 pacchetti grazie al flag "-c"
```

```sh
 tcpdump -i wlan0 -vvv -tttt -nn -e -X -w capture_file.pcap 
  
 # esegue una scansione, salvando anche informazioni su header ethernet "-e"
 # e sul contenuto "-X" in un file ".pcap" grazie al flag "-w", questo è 
 # utile per fare analisi a posteriori
```

```sh
 tcpdump -r capture_file.pcap 
 # con il flag "-r" riusciamo a leggere ed analizzare un file .pcap
```

```sh
 tcpdump -nn -r capture_file.pcap -X 
 # anche in modalità lettura si applicano gli stessi flag, quindi
 # possiamo ad esempio visualizzare il contenuto dei pacchetti col flag "-X"
 # it is useful to use -nn to have an immediate read of the file without trying
 # to resolve each address
```

### Comandi Preferiti


Per lanciare una cattura di pacchetti e capire ad alto livello cosa sta
succedendo il mio comando preferito e':
```sh
 tcpdump -i <interface> -nn -q
 # con -nn non perdiamo tempo a risolvere indirizzi e con -q abbiamo un
 # pacchetto per linea
```

Nel caso volessimo una vista piu' dettagliata allora possiamo stampare il
contenuto del pacchetto eseguendo:
```sh
 tcpdump -i <interface> -nn -q -X
 # con -X stampiamo il contenuto del pacchetto in HEX e in ASCII
```

Possiamo avere informazioni aggiuntive andando ad omettere il flag di quiet `-q`
e quindi eseguendo:
```sh
 tcpdump -i <interface> -nn -X
```

Nota che in tutti questi casi non stiamo salvando l'output in un file, per poter
salvare l'output in un pcap e allo stesso momento poter visualizzare a schermo
su stdout cosa sta avvenendo possiamo eseguire:
```sh
 tcpdump -i wlp1s0 -nn -q -w - | tee capture.pcap | tcpdump -nn -q -r   -
 # ricorda di inserire il flag `-nn` in entrambi i comandi tcpdump
 # se non vogliamo risolvere gli indirizzi
```

Per leggere un file pcap il mio comando preferito e':
```sh
 tcpdump -nn -r <file.pcap>
 # ricorda di specificare `-nn` se non si vuole perdere tempo a risolvere gli
 # indirizzi
 # nel caso questo flag venisse omesso, in pratica viene speso del tempo per
 # ogni pacchetto in cui un indirizzo puo' essere risolto
```


## Filtri

Queste sono solo alcuni flag di base di tcpdump, ma il cuore è 
costituito dalla possibilità di inserire capture filters che 
utilizzano la notazione "Berkeley Packet Filter" notation, 
vediamo qualche esempio di filtro:

* `src 2.3.4.5`;  cattura solo il traffico che ha come sorgente l'ip specificato
* `dst 3.4.5.6`; cattura solo il traffico che ha come destinazione l'ip specificato
* `net 1.2.3.0/24`; cattura solo il traffico appartenente alla rete specificata
* `port 3600`
* `src port 3333`
* `icmp`
* `ip6`
* `portrange 21-23`
* `less 32`;  cattura solo pacchetti più piccoli di 32 byte
* `greater 64`;  cattura solo pacchetti più grandi di 64 byte
* `<= 128`;  cattura solo pacchetti più piccoli o uguali alla dimensione di 128 byte

Possiamo anche combinare filtri con operatori come 
and (o &&), or (o ||) o not (o !), ad esempio:

* `dst 192.168.0.2 and src net and not icmp`
* `src 10.0.2.4 and (dst port 3389 or 22)`

Vediamo un semplice esempio che unisce qualche filtro con i 
flag appena visti:

```sh
 tcpdump -i wlan0 -nn -X -w capture_file.pcap 'port 80' 
 # combiniamo diverse opzioni e utilizziamo un filtro in BPFN,
 # gli apici sono opzionali, ma per questioni di leggibilità 
 # preferisco inserirli, in modo da separare il filtro in BPFN dal 
 # resto dei flag
```

```sh
 tcpdump -i wlan0 -vvv -tttt -nn -e -X -w capture_file.pcap \
 'src 10.0.2.4 and (dst port 3389 or 22)'
```

vediamo alcuni esempi per isolare specifici pacchetti TCP con 
determinati flag attivi:

```sh
 tcpdump 'tcp[13] & 32!=0' 
 # mostrami solo i pacchetti con il flag URG settato
```

```sh
 tcpdump 'tcp[13] & 16!=0' 
 # mostrami solo i pacchetti con il flag ACK settato
```

```sh
 tcpdump 'tcp[13] & 8!=0' 
 # mostrami solo i pacchetti con il flag PSH settato
```

```sh
 tcpdump 'tcp[13] & 4!=0' 
 # mostrami solo i pacchetti con il flag RST settato
```

```sh
 tcpdump 'tcp[13] & 2!=0' 
 # mostrami solo i pacchetti con il flag SYN settato
```

```sh
 tcpdump 'tcp[13] & 1!=0' 
 # mostrami solo i pacchetti con il flag FIN settato
```

```sh
 tcpdump 'tcp[13]=18' 
 # mostrami solo i pacchetti SYN/ACK
```

Esistono anche notazioni funzionalmente analoghe ma più 
semplici da leggere come ad esempio:

```sh
 tcpdump 'tcp[tcpflags] == tcp-syn'
```

```sh
 tcpdump 'tcp[tcpflags] == tcp-rst'
```

```sh
 tcpdump 'tcp[tcpflags] == tcp-fin'
```

Altri filtri utili in reti con macchine Windows, possono essere quelli che
escludono o catturano solo i seguenti protocolli: 

`(smb || nbns || dcerpc || nbss || dns)`


Possiamo fare diverse prove con tcpdump, magari mettendoci in 
ascolto su una porta specifica e provando a mandare traffico, con 
specifici programmi, oppure con netcat. Una combinazione 
interessante è accoppiare tcpdump con un packet crafter (e.g., 
scapy), e farli comunicare su una porta specifica magari 
sull'interfaccia di loopback, in modo da poter fare tutti i 
nostri esperimenti.

Possiamo anche utilizzare tcpdump per fare detection di arp 
poisoning, ad esempio guardando dispositivi che seguono questa 
logica:

192.168.1.100 is 00:00:00:00

192.168.1.100 is ff:ff:ff:ff cioè se lo stesso IP compare con due 
indirizzi MAC diversi in breve tempo, allora la situazione è 
sospettosa e probabilmente ci troviamo in una situazione di ARP 
cache poisoning. In genere se questo comportamento è associato 
all'indirizzo di un gateway allora MOLTO probabilmente siamo in 
una situazione di ARP cache poisoning.


### Filtri di uso comune

Vediamo ora una lista di filtri che potrebbero essere di uso comune.


#### Trovare User Agents HTTP

```sh
tcpdump -vvAls0 | grep 'User-Agent:'
```


#### Trovare richieste HTTP in cleartext

```sh
tcpdump -vvAls0 | grep 'GET'
```

Possiamo anche cercare richieste POST attraverso lo stesso filtro 
e cambiando la stringa in 'POST'.


#### Trovare Header Host di richieste HTTP

```sh
 tcpdump -vvAls0 | grep 'Host:'
```


#### Trovare Cookies HTTP

```sh
 tcpdump -vvAls0 | grep 'Set-Cookie|Host:|Cookie:'
```


#### Trovare connessioni SSH

```sh
 tcpdump 'tcp[(tcp[12]>>2):4] = 0x5353482D'
```


#### Trovare Traffico DNS

```sh
 tcpdump -vvAs0 port 53
```


#### Trovare Traffico FTP

```sh
 tcpdump -vvAs0 port ftp or ftp-data
```


#### Trovare Traffico NTP

```sh
tcpdump -vvAs0 port 123
```

#### Trovare Password in cleartext

```sh
tcpdump port http or port ftp or port smtp or port imap or port pop3 or port telnet -lA | egrep -i -B5 \
'pass=|pwd=|log=|login=|user=|username=|pw=|passw=|passwd= |password=|pass:|user:|username:|password:|login:|pass |user '
```


## Lanciare tcpdump ed altri tool di rete senza privilegi di root

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


## Lanciare tcpdump su una macchina remota e leggere le tracce con wireshark

```sh
ssh root@remotesystem 'tcpdump -s0 -c 1000 -nn -w - not port 22' | wireshark -k -i 
```
