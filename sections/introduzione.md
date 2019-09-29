
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
 # in genere l'opzione -n la vogliamo sempre in quanto risolvere indirizzi IP
 # puo' costare molto tempo
```

```sh
 tcpdump -i wlan0 -nn
 # non risolve gli hostname e i nomi dei servizi associati alle porte più comuni
 # anche in questo caso in genere non vogliamo risolvere il nome della porta
 # ma vedere la versione numerica, quindi il flag -nn e' quasi sempre usato
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

## Comandi Preferiti


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


