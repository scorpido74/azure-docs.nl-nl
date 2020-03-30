---
title: Azure HPC-cachegegevens inbeslagnemen - handmatigkopiëren
description: Cp-opdrachten gebruiken om gegevens naar een Blob-opslagdoel in Azure HPC-cache te verplaatsen
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fc397088e46f0d2b623080f3deed24c386e7d8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168484"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC-cachegegevens inbeslagname - handmatige kopieermethode

In dit artikel vindt u gedetailleerde instructies voor het handmatig kopiëren van gegevens naar een Blob-opslagcontainer voor gebruik met Azure HPC-cache. Het maakt gebruik van multi-threaded parallelle bewerkingen om de kopieersnelheid te optimaliseren.

Lees [Gegevens verplaatsen naar Azure Blob-opslag voor](hpc-cache-ingest.md)meer informatie over het verplaatsen van gegevens naar Blob-opslag voor uw Azure HPC-cache.

## <a name="simple-copy-example"></a>Eenvoudig voorbeeld van kopiëren

U handmatig een kopiëren met meerdere threaden op een client maken door meer dan één kopieeropdracht tegelijk op de achtergrond uit te voeren tegen vooraf gedefinieerde sets bestanden of paden.

De Opdracht ``cp`` Linux/UNIX ``-p`` bevat het argument om eigendom en mtime metadata te behouden. Het toevoegen van dit argument aan de onderstaande opdrachten is optioneel. (Als u het argument toevoegt, wordt het aantal oproepen van het bestandssysteem dat van de client naar het doelbestandssysteem wordt verzonden voor het wijzigen van metagegevens verhoogd.)

Dit eenvoudige voorbeeld kopieert twee bestanden parallel:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Na het uitgeven van `jobs` deze opdracht geeft de opdracht aan dat er twee threads worden uitgevoerd.

## <a name="copy-data-with-predictable-file-names"></a>Gegevens kopiëren met voorspelbare bestandsnamen

Als uw bestandsnamen voorspelbaar zijn, u expressies gebruiken om parallelle kopieerthreads te maken. 

Als uw map bijvoorbeeld 1000 bestanden bevat die `0001` achtereenvolgens zijn genummerd van naar `1000`, u de volgende expressies gebruiken om tien parallelle threads te maken die elk 100-bestanden kopiëren:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Gegevens kopiëren met ongestructureerde bestandsnamen

Als uw bestandsnaamgevingsstructuur niet voorspelbaar is, u bestanden groeperen op directorynamen. 

In dit voorbeeld worden hele ``cp`` mappen verzamelt om opdrachten te verzenden die als achtergrondtaken worden uitgevoerd:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Nadat de bestanden zijn verzameld, u parallelle kopieeropdrachten uitvoeren om de submappen en al hun inhoud opnieuw te kopiëren:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Wanneer bevestigingspunten toevoegen

Nadat u genoeg parallelle draden hebt die tegen één enkel doelsysteemsysteemmountpunt gaan, zal er een punt zijn waar het toevoegen van meer draden niet meer doorvoer geeft. (Doorvoer wordt gemeten in bestanden per seconde of bytes/seconde, afhankelijk van uw type gegevens.) Of erger nog, over-threading kan soms leiden tot een doorvoer degradatie.  

Wanneer dit gebeurt, u bevestigingspunten aan de clientzijde toevoegen aan andere azure HPC-cachebevestigingsadressen, met behulp van hetzelfde raspad voor het monteren van externe bestandssystemen:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Door de bevestigingspunten aan de clientzijde toe te `/mnt/destination[1-3]` voegen, u extra kopieeropdrachten naar de extra bevestigingspunten afschrijven, waardoor verdere parallellisme wordt bereikt.  

Als uw bestanden bijvoorbeeld erg groot zijn, u de kopieeropdrachten definiëren om verschillende doelpaden te gebruiken en meer opdrachten parallel verzenden van de client die de kopie uitvoert.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

In het bovenstaande voorbeeld worden alle drie de doelbevestigingspunten getarget door de processen voor het kopiëren van het clientbestand.

## <a name="when-to-add-clients"></a>Wanneer clients toevoegen

Ten slotte, wanneer u de mogelijkheden van de client hebt bereikt, zal het toevoegen van meer kopieerthreads of extra bevestigingspunten geen extra bestanden per seconde of bytes/sec-verhogingen opleveren. In dat geval u een andere client implementeren met dezelfde set bevestigingspunten die zijn eigen sets bestandskopieprocessen uitvoeren. 

Voorbeeld:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Bestandsmanifesten maken

Na het begrijpen van de bovenstaande benaderingen (meerdere copy-threads per bestemming, meerdere bestemmingen per client, meerdere clients per netwerktoegankelijk bronbestandssysteem), overweeg dan deze aanbeveling: Bouw bestandsmanifesten en gebruik ze vervolgens met kopie opdrachten voor meerdere clients.

In dit scenario ``find`` wordt de UNIX-opdracht gebruikt om manifesten van bestanden of mappen te maken:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Leid dit resultaat door naar een bestand:`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Vervolgens u door het manifest heen gaan, met BEHULP van BASH-opdrachten om bestanden te tellen en de grootte van de submappen te bepalen:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Ten slotte moet u de werkelijke bestandskopieopdrachten aan de clients maken.  

Als u vier clients hebt, gebruikt u de opdracht:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Als je vijf clients hebt, gebruik dan zoiets als dit:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

En voor zes.... Extrapolaat als dat nodig is.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

U krijgt *N* resulterende bestanden, een voor elk van uw *N-clients* die de padnamen heeft `find` naar de niveau-vier mappen die zijn verkregen als onderdeel van de uitvoer van de opdracht. 

Gebruik elk bestand om de opdracht kopiëren samen te bouwen:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Het bovenstaande geeft u *N-bestanden,* elk met een kopie opdracht per regel, die kunnen worden uitgevoerd als een BASH script op de client. 

Het doel is om meerdere threads van deze scripts gelijktijdig per client parallel op meerdere clients uit te voeren.
