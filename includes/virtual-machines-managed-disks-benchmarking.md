---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673303"
---
*De cache opwarmen*  
De schijf met ReadOnly host caching zijn in staat om hogere IOPS dan de schijf limiet te geven. Om deze maximale leesprestaties uit de hostcache te krijgen, moet u eerst de cache van deze schijf opwarmen. Dit zorgt ervoor dat de Read IOs dat de benchmarking tool zal rijden op CacheReads volume, daadwerkelijk raakt de cache, en niet de schijf direct. De cache hits resulteren in extra IOPS van de single cache enabled disk.

> [!IMPORTANT]
> U moet de cache opwarmen voordat u benchmarking uitvoert, elke keer dat VM opnieuw wordt opgestart.

## <a name="tools"></a>Hulpprogramma's

### <a name="iometer"></a>Iometer

[Download het iometer-gereedschap](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) op de VM.

#### <a name="test-file"></a>Testbestand

Iometer maakt gebruik van een testbestand dat is opgeslagen op het volume waarop u de benchmarkingtest uitvoert. Het station leest en schrijft op dit testbestand om de schijf IOPS en doorvoer te meten. Iometer maakt dit testbestand als u er geen hebt opgegeven. Maak een testbestand van 200 GB genaamd iobw.tst op de volumes CacheReads en NoCacheWrites.

#### <a name="access-specifications"></a>Toegangsspecificaties

De specificaties, aanvraag IO-grootte, % lezen/schrijven, % random/sequential worden geconfigureerd met behulp van het tabblad "Toegangsspecificaties" in Iometer. Maak een toegangsspecificatie voor elk van de onderstaande scenario's. Maak de toegangsspecificaties en "Opslaan" met een\_toepasselijke naam\_zoals - RandomWrites 8K, RandomReads 8K. Selecteer de bijbehorende specificatie bij het uitvoeren van het testscenario.

Een voorbeeld van toegangsspecificaties voor een maximaal Write IOPS-scenario wordt hieronder weergegeven,  
    ![Voorbeeld van toegangsspecificaties voor maximale schrijf-IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maximale IOPS-testspecificaties

Als u maximale IPP's wilt aantonen, gebruikt u een kleinere aanvraaggrootte. Gebruik de grootte van 8K-aanvragen en maak specificaties voor random schrijf- en leesbewerkingen.

| Toegangsspecificatie | Aanvraaggrootte | Willekeurig % | Lees % |
| --- | --- | --- | --- |
| RandomWrites\_8K RandomWrites 8K RandomWrites 8K RandomWrites |8 K |100 |0 |
| RandomReads\_8K |8 K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Specificaties maximale doorvoertest

Als u maximale doorvoer wilt aantonen, gebruikt u een grotere aanvraaggrootte. Gebruik de grootte van 64 K-aanvragen en maak specificaties voor random schrijf- en leesbewerkingen.

| Toegangsspecificatie | Aanvraaggrootte | Willekeurig % | Lees % |
| --- | --- | --- | --- |
| RandomWrites\_64K RandomWrites 64K RandomWrites 64K RandomWrites |64 K |100 |0 |
| RandomReads\_64K RandomReads 64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>De Iometer-test uitvoeren

De onderstaande stappen uitvoeren om de cache op te warmen

1. Maak twee toegangsspecificaties met onderstaande waarden,

   | Name | Aanvraaggrootte | Willekeurig % | Lees % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB RandomWrites |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Voer de Iometer-test uit voor het initialiseren van cacheschijf met volgende parameters. Gebruik drie werknemersthreads voor het doelvolume en een wachtrijdiepte van 128. Stel de duur van de test 'Run time' in op 2 uur op het tabblad 'Testsetup'.

   | Scenario | Doelvolume | Name | Duur |
   | --- | --- | --- | --- |
   | Cacheschijf initialiseren |CacheReads |RandomWrites\_1MB RandomWrites |2 uur |
1. Voer de Iometer-test uit voor het opwarmen van cacheschijf met de volgende parameters. Gebruik drie werknemersthreads voor het doelvolume en een wachtrijdiepte van 128. Stel de duur van de test 'Run time' in op 2 uur op het tabblad 'Testsetup'.

   | Scenario | Doelvolume | Name | Duur |
   | --- | --- | --- | --- |
   | Cacheschijf opwarmen |CacheReads |RandomReads\_1MB |2 uur |

Nadat de cacheschijf is opgewarmd, gaat u verder met de onderstaande testscenario's. Als u de Iometer-test wilt uitvoeren, gebruikt u ten minste drie werknemersthreads voor **elk** doelvolume. Selecteer voor elke werkdraad het doelvolume, stel wachtrijdiepte in en selecteer een van de opgeslagen testspecificaties, zoals in de onderstaande tabel, om het bijbehorende testscenario uit te voeren. De tabel toont ook verwachte resultaten voor IOPS en Doorvoer bij het uitvoeren van deze tests. Voor alle scenario's wordt een kleine IO-grootte van 8 KB en een hoge wachtrijdiepte van 128 gebruikt.

| Testscenario | Doelvolume | Name | Resultaat |
| --- | --- | --- | --- |
| Met maximaal IOPS lezen |CacheReads |RandomWrites\_8K RandomWrites 8K RandomWrites 8K RandomWrites |50.000 IOPS |
| Met maximaal IOPS schrijven |NoCacheWrites NoCacheWrites NoCacheWrites NoCache |RandomReads\_8K |64.000 IOPS |
| Met maximaal Gecombineerde IOPS |CacheReads |RandomWrites\_8K RandomWrites 8K RandomWrites 8K RandomWrites |100.000 IOPS |
| NoCacheWrites NoCacheWrites NoCacheWrites NoCache |RandomReads\_8K | &nbsp; | &nbsp; |
| Met maximaal MB/sec lezen |CacheReads |RandomWrites\_64K RandomWrites 64K RandomWrites 64K RandomWrites |524 MB/s |
| Met maximaal MB/sec schrijven |NoCacheWrites NoCacheWrites NoCacheWrites NoCache |RandomReads\_64K RandomReads 64K |524 MB/s |
| Gecombineerde MB/sec |CacheReads |RandomWrites\_64K RandomWrites 64K RandomWrites 64K RandomWrites |1000 MB/s |
| NoCacheWrites NoCacheWrites NoCacheWrites NoCache |RandomReads\_64K RandomReads 64K | &nbsp; | &nbsp; |

Hieronder vindt u screenshots van de Iometer-testresultaten voor gecombineerde IOPS- en Doorvoerscenario's.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Gecombineerd leest en schrijft maximale IOPS

![Gecombineerd leest en schrijft maximale IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Gecombineerde leest en schrijft maximale doorvoer

![Gecombineerde lees- en schrijfsnelheid Maximale doorvoer](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO is een populaire tool om opslag op de Linux VM's te benchmarken. Het heeft de flexibiliteit om verschillende IO-formaten te selecteren, sequentiële of willekeurige leest en schrijft. Het spawnt worker threads of processen om de opgegeven I/O-bewerkingen uit te voeren. U het type I/O-bewerkingen opgeven dat elke werknemersthread moet uitvoeren met behulp van taakbestanden. We hebben één taakbestand per scenario gemaakt dat wordt geïllustreerd in de onderstaande voorbeelden. U de specificaties in deze taakbestanden wijzigen om verschillende workloads te benchmarken die worden uitgevoerd op Premium Storage. In de voorbeelden gebruiken we een Standaard DS 14 VM met **Ubuntu**. Gebruik dezelfde setup beschreven in het begin van de benchmarking sectie en warm de cache op voordat u de benchmarkingtests uitvoert.

Voordat u begint, [download t/m FIO](https://github.com/axboe/fio) en installeer deze op uw virtuele machine.

Voer de volgende opdracht voor Ubuntu uit,

```
apt-get install fio
```

We gebruiken vier werknemersthreads voor het aansturen van Schrijfbewerkingen en vier werknemersthreads voor het aansturen van Leesbewerkingen op de schijven. De Write-werknemers genereren het verkeer op het "nocache"-volume, dat 10 schijven heeft met cache ingesteld op 'Geen'. De Read-werknemers genereren het verkeer op het leescache-volume, dat één schijf heeft met cache ingesteld op "ReadOnly".

#### <a name="maximum-write-iops"></a>Maximaal schrijven IOPS

Maak het taakbestand met de volgende specificaties om maximale Schrijf IOPS te krijgen. Noem het "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Let op de follow key things die in overeenstemming zijn met de ontwerprichtlijnen die in eerdere secties zijn besproken. Deze specificaties zijn essentieel om maximaal IOPS te rijden,  

* Een hoge rijdiepte van 256.  
* Een kleine blokgrootte van 8 KB.  
* Meerdere threads die willekeurige schrijft.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden af te trappen,  

```
sudo fio --runtime 30 fiowrite.ini
```

Terwijl de test wordt uitgevoerd, u het aantal schrijf-IOPS zien dat de VM- en Premium-schijven leveren. Zoals in het onderstaande voorbeeld wordt weergegeven, levert de DS14 VM zijn maximale schrijf-IOPS-limiet van 50.000 IOPS.  
    ![Aantal schrijf-IOPS VM- en Premium-schijven levert](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maximaal gelezen IOPS

Maak het taakbestand met de volgende specificaties om maximaal Lees-IOPS te krijgen. Noem het "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Let op de follow key things die in overeenstemming zijn met de ontwerprichtlijnen die in eerdere secties zijn besproken. Deze specificaties zijn essentieel om maximaal IOPS te rijden,

* Een hoge rijdiepte van 256.  
* Een kleine blokgrootte van 8 KB.  
* Meerdere threads die willekeurige schrijft.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden af te trappen,

```
sudo fio --runtime 30 fioread.ini
```

Terwijl de test wordt uitgevoerd, u het aantal gelezen IOPS zien dat de VM- en Premium-schijven leveren. Zoals in het onderstaande voorbeeld wordt weergegeven, levert de DS14 VM meer dan 64.000 Read IOPS. Dit is een combinatie van de schijf en de cacheprestaties.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maximaal lezen en schrijven IOPS

Maak het taakbestand met de volgende specificaties om maximaal gecombineerde IOPS lezen en schrijven te krijgen. Noem het "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Let op de follow key things die in overeenstemming zijn met de ontwerprichtlijnen die in eerdere secties zijn besproken. Deze specificaties zijn essentieel om maximaal IOPS te rijden,

* Een hoge rijdiepte van 128.  
* Een kleine blokgrootte van 4 KB.  
* Meerdere threads die willekeurige leest en schrijft.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden af te trappen,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Terwijl de test wordt uitgevoerd, u het aantal gecombineerde lees- en schrijf-IOPS zien dat de VM- en Premium-schijven leveren. Zoals in het onderstaande voorbeeld wordt weergegeven, levert de DS14 VM meer dan 100.000 gecombineerde Lees- en Schrijf-IOPS. Dit is een combinatie van de schijf en de cacheprestaties.  
    ![Gecombineerde lezen en schrijven IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maximale gecombineerde doorvoer

Gebruik een grotere blokgrootte en grote wachtrijdiepte met meerdere threads die lees- en schrijfbewerkingen uitvoeren om de maximale gecombineerde lees- en schrijfdoorvoer te krijgen. U een blokgrootte van 64 KB en wachtrijdiepte van 128 gebruiken.
