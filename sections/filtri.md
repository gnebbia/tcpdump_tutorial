
Queste sono solo alcuni flag di base di tcpdump, ma il cuore è
costituito dalla possibilità di inserire capture filters che
utilizzano la notazione "Berkeley Packet Filter" (BPF),
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
`and` (o `&&`), `or` (o `||`) o `not` (o `!`), ad esempio:

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
 tcpdump -i wlan0 -nn 'tcp[13] & 32!=0'
 # mostrami solo i pacchetti con il flag URG settato
```

```sh
 tcpdump -i wlan0 -nn 'tcp[13] & 16!=0'
 # mostrami solo i pacchetti con il flag ACK settato
```

```sh
 tcpdump -i wlan0 -nn 'tcp[13] & 8!=0'
 # mostrami solo i pacchetti con il flag PSH settato
```

```sh
 tcpdump -i wlan0 -nn 'tcp[13] & 4!=0'
 # mostrami solo i pacchetti con il flag RST settato
```

```sh
 tcpdump -i wlan0 -nn 'tcp[13] & 2!=0'
 # mostrami solo i pacchetti con il flag SYN settato
```

```sh
 tcpdump -i wlan0 -nn 'tcp[13] & 1!=0'
 # mostrami solo i pacchetti con il flag FIN settato
```

```sh
 tcpdump -i wlan0 -nn 'tcp[13]=18'
 # mostrami solo i pacchetti SYN/ACK
```

Esistono anche notazioni funzionalmente analoghe ma più
semplici da leggere come ad esempio:

```sh
 tcpdump -i wlan0 -nn 'tcp[tcpflags] == tcp-syn'
```

```sh
 tcpdump -i wlan0 -nn 'tcp[tcpflags] == tcp-rst'
```

```sh
 tcpdump -i wlan0 -nn 'tcp[tcpflags] == tcp-fin'
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


