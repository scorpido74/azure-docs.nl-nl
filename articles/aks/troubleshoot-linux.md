---
title: Linux-prestatietools
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het oplossen en oplossen van veelvoorkomende problemen bij het gebruik van Azure Kubernetes Service (AKS)
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925603"
---
# <a name="linux-performance-troubleshooting"></a>Problemen met de prestaties van Linux

Uitputting van hulpbronnen op Linux-machines is een veel voorkomend probleem en kan zich manifesteren door een breed scala aan symptomen. Dit document biedt een overzicht op hoog niveau van de beschikbare tools om dergelijke problemen te diagnosticeren.

Veel van deze gereedschappen accepteren een interval waarop rollend vermogen kan worden geproduceerd. Dit uitvoerformaat maakt het spotten van patronen doorgaans veel gemakkelijker. Indien aanvaard, omvat `[interval]`de voorbeeldaanroep .

Veel van deze tools hebben een uitgebreide geschiedenis en een brede set configuratie-opties. Deze pagina biedt slechts een eenvoudige subset van aanroepingen om veelvoorkomende problemen te markeren. De canonieke bron van informatie is altijd de referentiedocumentatie voor elk specifiek instrument. Die documentatie zal veel grondiger zijn dan wat hier wordt verstrekt.

## <a name="guidance"></a>Richtlijnen

Wees systematisch in uw benadering van het onderzoeken van prestatieproblemen. Twee veelvoorkomende benaderingen zijn GEBRUIK (gebruik, verzadiging, fouten) en ROOD (snelheid, fouten, duur). RED wordt meestal gebruikt in de context van services voor op aanvragen gebaseerde monitoring. USE wordt meestal gebruikt voor het bewaken van resources: voor elke resource in een machine, monitorgebruik, verzadiging en fouten. De vier belangrijkste soorten resources op elke machine zijn cpu, geheugen, schijf en netwerk. Hoge benuttings-, verzadigings- of foutpercentages voor een van deze resources duiden op een mogelijk probleem met het systeem. Wanneer er een probleem is, onderzoekt u de hoofdoorzaak: waarom is schijf-IO-latentie hoog? Zijn de schijven of virtuele machine SKU gasgevend? Welke processen schrijven naar de apparaten en naar welke bestanden?

Enkele voorbeelden van veelvoorkomende problemen en indicatoren om ze te diagnosticeren:
- IOPS throttling: gebruik iostat om iops per apparaat te meten. Zorg ervoor dat geen enkele afzonderlijke schijf boven de limiet is en dat de som voor alle schijven lager is dan de limiet voor de virtuele machine.
- Bandbreedte beperking: gebruik iostat als voor IOPS, maar het meten van lees / schrijf doorvoer. Zorg ervoor dat zowel de doorvoer per apparaat als de totale doorvoer onder de bandbreedtelimieten ligt.
- SNAT uitputting: dit kan zich manifesteren als hoge actieve (uitgaande) verbindingen in SAR. 
- Pakketverlies: dit kan worden gemeten door proxy via TCP retransmit count ten opzichte van verzonden / ontvangen telling. Beide `sar` `netstat` en kunnen deze informatie laten zien.

## <a name="general"></a>Algemeen

Deze instrumenten zijn algemeen doel en omvatten basissysteeminformatie. Ze zijn een goed uitgangspunt voor verder onderzoek.

### <a name="uptime"></a>Uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

uptime biedt systeemuptime en 1, 5 en 15 minuten load gemiddelden. Deze belastingsgemiddelden komen ruwweg overeen met threads die werken of wachten op onbreekbaar werk. In absolute absolute deze aantallen kan moeilijk te interpreteren, maar gemeten in de tijd kunnen ze ons nuttige informatie vertellen:

- 1-minuten gemiddelde > gemiddelde van 5 minuten betekent dat de belasting toeneemt.
- 1-minuten gemiddelde < gemiddelde van 5 minuten betekent dat de belasting afneemt.

uptime kan ook verlichten waarom informatie niet beschikbaar is: het probleem kan zijn opgelost op zijn eigen of door een herstart voordat de gebruiker toegang tot de machine.

Het laden van gemiddelden die hoger zijn dan het aantal beschikbare CPU-threads, kan duiden op een prestatieprobleem met een bepaalde werkbelasting.

### <a name="dmesg"></a>Dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg dumpt de kernel buffer. Gebeurtenissen zoals OOMKill voegen een vermelding toe aan de kernelbuffer. Het vinden van een OOMKill of andere resource uitputting berichten in dmesg logs is een sterke indicator van een probleem.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`biedt een breed overzicht van de huidige systeemstatus. De kopteksten bevatten nuttige geaggregeerde informatie:

- staat van taken: hardlopen, slapen, gestopt.
- CPU-gebruik, in dit geval meestal met idle tijd.
- totaal, gratis en gebruikt systeemgeheugen.

`top`kunnen kortstondige processen missen; alternatieven `htop` zoals `atop` en bieden soortgelijke interfaces, terwijl de vaststelling van een aantal van deze tekortkomingen.

## <a name="cpu"></a>CPU

Deze tools bieden informatie over cpu-gebruik. Dit is vooral handig bij rollende output, waar patronen gemakkelijk te herkennen zijn.

### <a name="mpstat"></a>mpstat mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`drukt soortgelijke CPU-informatie naar boven, maar opgesplitst door CPU-thread. Het zien van alle kernen tegelijk kan handig zijn voor het detecteren van een zeer onevenwichtig CPU-gebruik, bijvoorbeeld wanneer een single threaded-toepassing één kern gebruikt bij 100% gebruik. Dit probleem kan moeilijker te herkennen zijn wanneer deze worden samengevoegd over alle CPU's in het systeem.

### <a name="vmstat"></a>vmstat vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`biedt vergelijkbare `mpstat` `top`informatie en , opsommen aantal processen te wachten op CPU (r kolom), geheugen statistieken, en het percentage van de CPU-tijd doorgebracht in elke werkstaat.

## <a name="memory"></a>Geheugen

Geheugen is een zeer belangrijke, en gelukkig gemakkelijk, bron te volgen. Sommige hulpprogramma's kunnen zowel `vmstat`CPU als geheugen rapporteren, zoals . Maar tools `free` zoals kan nog steeds nuttig zijn voor snelle debugging.

### <a name="free"></a>Gratis

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`presenteert basisinformatie over het totale geheugen, evenals gebruikt en gratis geheugen. `vmstat`kan nuttiger zijn, zelfs voor basisgeheugenanalyse vanwege de mogelijkheid om rolling output te bieden.

## <a name="disk"></a>Schijf

Deze hulpprogramma's meten schijf-IOPS, wachtrijen wachten en totale doorvoer. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`biedt diepgaande inzichten in het gebruik van schijven. Deze aanroep `-x` gaat over `-y` voor uitgebreide statistieken, om de initiële uitvoerafdruksysteemgemiddelden sinds het opstarten over te slaan en `1 1` om op te geven dat we een interval van 1 seconde willen, eindigend na één blok uitvoer. 

`iostat`onthult veel nuttige statistieken:

- `r/s`en `w/s` zijn leest per seconde en schrijft per seconde. De som van deze waarden is IOPS.
- `rkB/s`en `wkB/s` worden kilobytes gelezen/geschreven per seconde. De som van deze waarden is doorvoer.
- `await`is de gemiddelde iowait-tijd in milliseconden voor aanvragen in de wachtrij.
- `avgqu-sz`is de gemiddelde wachtrijgrootte over het opgegeven interval.

Op een Azure VM:

- de som van `r/s` en `w/s` voor een individueel blokapparaat mag de SKU-limieten van die schijf niet overschrijden.
- de som van `rkB/s` en `wkB/s` voor een individueel blokapparaat mag de SKU-limieten van die schijf niet overschrijden
- de som van `r/s` en `w/s` voor alle blokapparaten mag de limieten voor de VM SKU niet overschrijden.
- de som van `rkB/s` en 'wkB/s voor alle blokapparaten mag de limieten voor de VM SKU niet overschrijden.

Houd er rekening mee dat de OS-schijf telt als een beheerde schijf van de kleinste SKU die overeenkomt met de capaciteit. Een 1024GB OS-schijf komt bijvoorbeeld overeen met een P30-schijf. Kortstondige OS-schijven en tijdelijke schijven hebben geen afzonderlijke schijflimieten; ze worden alleen beperkt door de volledige VM-limieten.

Niet-nul waarden van wachten of avgqu-sz zijn ook goede indicatoren van IO stelling.

## <a name="network"></a>Netwerk

Deze hulpprogramma's meten netwerkstatistieken zoals doorvoer, transmissiefouten en gebruik. Diepere analyse kan fijnkorrelige TCP-statistieken over congestie en gedropte pakketten blootleggen.

### <a name="sar"></a>Sar

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`is een krachtig hulpmiddel voor een breed scala aan analyses. Hoewel dit voorbeeld zijn vermogen gebruikt om netwerkstatistieken te meten, is het even krachtig voor het meten van CPU- en geheugenverbruik. In dit `sar` voorbeeld `-n` wordt met `DEV` vlag aanknopingspunt aangenomen om het trefwoord (netwerkapparaat) op te geven, waarbij de netwerkdoorvoer per apparaat wordt weergegeven.

- De som van `rxKb/s` en `txKb/s` is totale doorvoer voor een bepaald apparaat. Wanneer deze waarde de limiet voor de ingerichte Azure NIC overschrijdt, worden workloads op de machine ervaren een verhoogde netwerklatentie.
- `%ifutil`meet het gebruik van een bepaald apparaat. Aangezien deze waarde 100% nadert, zullen workloads een verhoogde netwerklatentie ervaren.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Deze aanroep `sar` gebruikt `TCP,ETCP` de trefwoorden om TCP-verbindingen te onderzoeken. De derde kolom van de laatste rij, "retrans", is het aantal TCP-retransmits per seconde. Hoge waarden voor dit veld duiden op een onbetrouwbare netwerkverbinding. In de eerste en derde rij betekent "actief" een verbinding die afkomstig is van het lokale apparaat, terwijl 'afstandsbediening' een binnenkomende verbinding aangeeft.  Een veelvoorkomend probleem op Azure is `sar` SNAT-poortuitputting, die kan helpen bij het detecteren. SNAT-poortuitputting zou zich manifesteren als hoge "actieve" waarden, omdat het probleem te wijten is aan een hoge mate van uitgaande, lokaal geïnitieerde TCP-verbindingen.

Als `sar` duurt een interval, het drukt rollende uitvoer en vervolgens drukt de laatste rijen van de uitvoer met de gemiddelde resultaten van de aanroep.

### <a name="netstat"></a>Netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`kan introspect een breed scala van netwerk statistieken, hier aangeroepen met samenvatting output. Er zijn veel nuttige velden hier, afhankelijk van het probleem. Een nuttig veld in de tcp-sectie is 'mislukte verbindingspogingen'. Dit kan een indicatie zijn van snat-poortuitputting of andere problemen met het maken van uitgaande verbindingen. Een hoge snelheid van opnieuw verzonden segmenten (ook onder de TCP-sectie) kan duiden op problemen met de levering van pakketten. 
