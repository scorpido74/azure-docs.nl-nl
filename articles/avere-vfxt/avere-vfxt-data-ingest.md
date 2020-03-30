---
title: Gegevens verplaatsen naar Avere vFXT voor Azure
description: Gegevens toevoegen aan een nieuw opslagvolume voor gebruik met de Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153715"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Gegevens verplaatsen naar het vFXT-cluster - Parallelle gegevens inname

Nadat u een nieuw vFXT-cluster hebt gemaakt, is het mogelijk dat u gegevens verplaatst naar een nieuw opslagvolume in Azure. Echter, als uw gebruikelijke methode van het verplaatsen van gegevens is de afgifte van een eenvoudige kopie opdracht van een client, ziet u waarschijnlijk een langzame kopie prestaties. Single-threaded kopiëren is geen goede optie voor het kopiëren van gegevens naar de back-end opslag van het Avere vFXT-cluster.

Omdat het Avere vFXT voor Azure-cluster een schaalbare multi-clientcache is, is de snelste en meest efficiënte manier om gegevens naar het cluster te kopiëren met meerdere clients. Deze techniek parallel hiermee verzoent de opname van de bestanden en objecten.

![Diagram met gegevensbewegingen met meerdere clientnen: linksboven heeft een pictogram voor on-premises hardwareopslag meerdere pijlen die daaruit komen. De pijlen wijzen naar vier clientmachines. Van elke clientmachine wijzen drie pijlen naar de Avere vFXT. Van de Avere vFXT wijzen meerdere pijlen naar Blob-opslag.](media/avere-vfxt-parallel-ingest.png)

De ``cp`` ``copy`` of opdrachten die vaak worden gebruikt om gegevens van het ene opslagsysteem naar het andere over te dragen, zijn processen met één thread die slechts één bestand tegelijk kopiëren. Dit betekent dat de bestandsserver slechts één bestand tegelijk inneemt - wat een verspilling is van de bronnen van het cluster.

In dit artikel worden strategieën uitgelegd voor het maken van een multi-client, multi-threaded bestandskopieersysteem om gegevens naar het Avere vFXT-cluster te verplaatsen. Het verklaart bestandsoverdracht concepten en beslissingspunten die kunnen worden gebruikt voor efficiënte gegevens kopiëren met behulp van meerdere clients en eenvoudige kopieeropdrachten.

Het verklaart ook een aantal hulpprogramma's die kunnen helpen. Het ``msrsync`` hulpprogramma kan worden gebruikt om het proces van het verdelen ``rsync`` van een gegevensset in buckets en het gebruik van opdrachten gedeeltelijk te automatiseren. Het ``parallelcp`` script is een ander hulpprogramma dat de bronmap leest en automatisch kopieeropdrachten uitgeeft. Ook kan ``rsync`` de tool in twee fasen worden gebruikt om een snellere kopie te bieden die nog steeds gegevensconsistentie biedt.

Klik op de link om naar een sectie te gaan:

* [Voorbeeld van handmatige kopie](#manual-copy-example) - Een grondige uitleg met kopieeropdrachten
* [Voorbeeld van rsync in twee fasen](#use-a-two-phase-rsync-process)
* [Voorbeeld van gedeeltelijk geautomatiseerd (msrsync)](#use-the-msrsync-utility)
* [Voorbeeld van parallelle kopie](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Vm-sjabloon gegevensinname of gegevens

Een resourcemanagersjabloon is beschikbaar op GitHub om automatisch een VM te maken met de parallelle hulpprogramma's voor gegevensopname die in dit artikel worden genoemd.

![diagram met elk meerdere pijlen van blobopslag, hardwareopslag en Azure-bestandsbronnen. De pijlen wijzen naar een "data inestor vm" en van daaruit wijzen meerdere pijlen naar de Avere vFXT](media/avere-vfxt-ingestor-vm.png)

De data inestor VM is onderdeel van een tutorial waarbij de nieuw gemaakte VM het Avere vFXT-cluster monteert en het bootstrap-script uit het cluster downloadt. Lees [Bootstrap een data innameor VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) voor meer informatie.

## <a name="strategic-planning"></a>Strategische planning

Wanneer u een strategie ontwerpt om gegevens parallel te kopiëren, moet u de afwegingen in bestandsgrootte, bestandstelling en mapdiepte begrijpen.

* Wanneer bestanden klein zijn, is de statistiek van belang bestanden per seconde.
* Wanneer bestanden groot zijn (10MiBi of hoger), is de statistiek van belang bytes per seconde.

Elk kopieerproces heeft een doorvoersnelheid en een door bestanden overgedragen snelheid, die kan worden gemeten aan de doortiming van de lengte van de kopieeropdracht en factoring van de bestandsgrootte en het aantal bestanden. Uitleggen hoe de tarieven te meten is buiten het bereik van dit document, maar het is belangrijk om te begrijpen of je te maken hebt met kleine of grote bestanden.

## <a name="manual-copy-example"></a>Voorbeeld van handmatige kopiëren

U handmatig een kopiëren met meerdere threaden op een client maken door meer dan één kopieeropdracht tegelijk op de achtergrond uit te voeren tegen vooraf gedefinieerde sets bestanden of paden.

De Opdracht ``cp`` Linux/UNIX ``-p`` bevat het argument om eigendom en mtime metadata te behouden. Het toevoegen van dit argument aan de onderstaande opdrachten is optioneel. (Als u het argument toevoegt, wordt het aantal bestandssysteemoproepen dat van de client naar het doelbestandssysteem wordt verzonden voor het wijzigen van metagegevens.)

Dit eenvoudige voorbeeld kopieert twee bestanden parallel:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Na het uitgeven van `jobs` deze opdracht geeft de opdracht aan dat er twee threads worden uitgevoerd.

### <a name="predictable-filename-structure"></a>Voorspelbare bestandsnaamstructuur

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

### <a name="unknown-filename-structure"></a>Onbekende bestandsnaamstructuur

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

### <a name="when-to-add-mount-points"></a>Wanneer bevestigingspunten toevoegen

Nadat u genoeg parallelle draden hebt die tegen één enkel doelfilesystem mountpunt gaan, zal er een punt zijn waar het toevoegen van meer threads niet meer doorvoer geeft. (Doorvoer wordt gemeten in bestanden per seconde of bytes/seconde, afhankelijk van uw type gegevens.) Of erger nog, over-threading kan soms leiden tot een doorvoer degradatie.

Wanneer dit gebeurt, u bevestigingspunten aan de clientzijde toevoegen aan andere vFXT-cluster-IP-adressen, met behulp van hetzelfde remote filesystem mount-pad:

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

### <a name="when-to-add-clients"></a>Wanneer clients toevoegen

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

### <a name="create-file-manifests"></a>Bestandsmanifesten maken

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
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

## <a name="use-a-two-phase-rsync-process"></a>Een rsync-proces in twee fasen gebruiken

Het ``rsync`` standaardhulpprogramma werkt niet goed voor het vullen van cloudopslag via het Avere vFXT voor Azure-systeem, omdat het een groot aantal bestandsverzamelingen genereert en bewerkingen hernoemt om de gegevensintegriteit te garanderen. U echter veilig ``--inplace`` gebruik ``rsync`` maken van de optie met de meer zorgvuldige kopieerprocedure overslaan als u dat volgt met een tweede run die de integriteit van het bestand controleert.

Een ``rsync`` standaardkopieerbewerking maakt een tijdelijk bestand en vult het met gegevens. Als de gegevensoverdracht is voltooid, wordt het tijdelijke bestand omgedoopt tot de oorspronkelijke bestandsnaam. Deze methode garandeert consistentie, zelfs als de bestanden worden geopend tijdens het kopiëren. Maar deze methode genereert meer schrijfbewerkingen, wat de bestandsverplaatsing door de cache vertraagt.

De ``--inplace`` optie schrijft het nieuwe bestand direct op de uiteindelijke locatie. Bestanden zijn niet gegarandeerd consistent te zijn tijdens de overdracht, maar dat is niet belangrijk als u priming een opslagsysteem voor gebruik later.

De ``rsync`` tweede bewerking dient als een consistentiecontrole op de eerste bewerking. Omdat de bestanden al zijn gekopieerd, is de tweede fase een quick scan om ervoor te zorgen dat de bestanden op de bestemming overeenkomen met de bestanden op de bron. Als bestanden niet overeenkomen, worden ze gekopieerd.

U beide fasen samen in één opdracht uitgeven:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Deze methode is een eenvoudige en tijdeffectieve methode voor gegevenssets tot het aantal bestanden dat de interne directorymanager aankan. (Dit zijn meestal 200 miljoen bestanden voor een cluster met 3 node, 500 miljoen bestanden voor een cluster met zes nodes, enzovoort.)

## <a name="use-the-msrsync-utility"></a>Gebruik het msrsync-hulpprogramma

De ``msrsync`` tool kan ook worden gebruikt om gegevens te verplaatsen naar een back-end core filer voor het Avere-cluster. Deze tool is ontworpen om het ``rsync`` bandbreedtegebruik te optimaliseren door meerdere parallelle processen uit te voeren. Het is verkrijgbaar bij <https://github.com/jbd/msrsync>GitHub op .

``msrsync``breekt de bronmap op in afzonderlijke "buckets" en voert vervolgens afzonderlijke ``rsync`` processen uit op elke bucket.

Voorlopige tests met behulp van een vier-core VM toonde de beste efficiëntie bij het gebruik van 64 processen. Gebruik ``msrsync`` de ``-p`` optie om het aantal processen in te stellen op 64.

U het ``--inplace`` argument ``msrsync`` ook gebruiken met opdrachten. Als u deze optie gebruikt, u overwegen een tweede opdracht uit te voeren (zoals bij [rsync](#use-a-two-phase-rsync-process), hierboven beschreven) om de integriteit van gegevens te waarborgen.

``msrsync``kan alleen schrijven van en naar lokale volumes. De bron en bestemming moeten toegankelijk zijn als lokale bevestigingen in het virtuele netwerk van het cluster.

Volg ``msrsync`` de volgende instructies om een Azure-cloudvolume te vullen met een Avere-cluster:

1. Installeren ``msrsync`` en de vereisten (rsync en Python 2.6 of hoger)
1. Bepaal het totale aantal bestanden en mappen dat moet worden gekopieerd.

   Gebruik bijvoorbeeld het ``prime.py`` Avere-hulpprogramma ```prime.py --directory /path/to/some/directory``` met argumenten (beschikbaar door url <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>te downloaden).

   Als u ``prime.py``dit niet gebruikt, u ``find`` het aantal items met het gereedschap GNU als volgt berekenen:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Verdeel het aantal items door 64 om het aantal items per proces te bepalen. Gebruik dit nummer ``-f`` met de optie om de grootte van de buckets in te stellen wanneer u de opdracht uitvoert.

1. Geef ``msrsync`` de opdracht uit om bestanden te kopiëren:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Als ``--inplace``u deze gebruikt, voegt u een tweede uitvoering toe zonder de optie om te controleren of de gegevens correct zijn gekopieerd:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Deze opdracht is bijvoorbeeld ontworpen om 11.000 bestanden in 64 processen te verplaatsen van /test/source-repository naar /mnt/vfxt/repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Het parallelle kopieerscript gebruiken

Het ``parallelcp`` script kan ook handig zijn voor het verplaatsen van gegevens naar de back-endopslag van uw vFXT-cluster.

Het onderstaande script voegt `parallelcp`het uitvoerbare toe. (Dit script is ontworpen voor Ubuntu; als ``parallel`` u een andere distributie gebruikt, moet u afzonderlijk installeren.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Voorbeeld van parallelle kopie

In dit voorbeeld wordt het ``glibc`` parallelle kopieerscript gebruikt om te compileren met behulp van bronbestanden uit het Avere-cluster.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

De bronbestanden worden opgeslagen op het avere-clusterbevestigingspunt en de objectbestanden worden opgeslagen op de lokale harde schijf.

Dit script maakt gebruik van parallelle kopie script hierboven. De ``-j`` optie wordt ``parallelcp`` ``make`` gebruikt met en om parallelisatie te krijgen.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
