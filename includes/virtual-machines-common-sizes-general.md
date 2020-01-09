---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: 5d652eb3bad72bded8e85a6ac841aae7a9c82a40
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663108"
---
VM-grootten voor algemeen gebruik bieden evenwichtige verhouding tussen CPU en geheugen. Ideaal voor testen en ontwikkelen, kleine tot middelgrote databases en webservers met weinig of gemiddeld verkeer. Dit artikel bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer voor grootten in deze groepering.

- De [DC-serie](#dc-series) is een familie van virtuele machines in azure die u kan helpen bij het beschermen van de vertrouwelijkheid en integriteit van uw gegevens en code tijdens het verwerken van de open bare Cloud. Deze machines zijn voorzien van de nieuwste generatie 3,7 GHz Intel XEON E-2176G-processors met SGX-technologie. Met de Intel Turbo Boost Technology kunnen de machines tot wel 4,7 GHz bereiken. Met instanties uit de DC-serie kunnen klanten veilige toepassingen met enclaves bouwen voor het beschermen van hun code en gegevens terwijl deze worden gebruikt.

- De virtuele machines uit de Av2-serie kunnen worden geïmplementeerd op diverse typen hardware en processors. VM's uit de A-serie beschikken over CPU-prestaties en geheugenconfiguraties die uitermate geschikt zijn voor workloads op instapniveau, zoals ontwikkelen en testen. De grootte is afhankelijk van de hardware, zodat er consistente processorprestaties voor het actieve exemplaar kunnen worden geboden, ongeacht de hardware waarop deze is geïmplementeerd. Om de fysieke hardware te bepalen waarop deze grootte is geïmplementeerd, vraagt u vanuit de virtuele machine gegevens over de virtuele hardware op.

  Voor beelden van use cases zijn: ontwikkelings-en test servers, webservers met weinig verkeer, kleine tot middel grote data bases, proefs van concepten en code opslagplaatsen.

- Dv2-serie, een follow-on voor de oorspronkelijke D-serie, bevat een krachtigere CPU en optimale configuratie van CPU naar geheugen, zodat deze geschikt zijn voor de meeste productie werkbelastingen. De dv2-serie is ongeveer 35% sneller dan de D-serie. Dv2-serie wordt uitgevoerd op de Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors met de Intel Turbo Boost-technologie 2,0. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

- De Dv3-serie wordt uitgevoerd op de Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors in een Hyper-Threaded configuratie, waardoor u een betere toegevoegde waarde hebt voor het algemeen werk belastingen voor het doel.  Het geheugen is uitgevouwen (van ~ 3,5 GiB/vCPU naar 4 GiB/vCPU), terwijl de schijf-en netwerk limieten per kern zijn aangepast om te worden uitgelijnd met de verplaatsing van hyperthreading.  De Dv3-serie heeft niet langer de VM-grootten van het hoge geheugen van de D/dv2-serie, die zijn verplaatst naar de Ev3 geoptimaliseerd voor geheugen voor [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#ev3-series) en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#ev3-series).

  Voor beelden van use-cases van de D-serie zijn zakelijke toepassingen, relationele data bases, caching in het geheugen en analyse.

- De Dav4-serie en de Dasv4-serie zijn nieuwe grootten die gebruikmaken van de 2.35 GHz EPYC<sup>TM</sup> 7452-processor van AMD in een configuratie met meerdere threads, met maxi maal 256 GB L3-cache van 8 GB aan de L3-cache tot elke 8 kernen die de klant opties verhogen voor het uitvoeren van hun werk belastingen voor algemeen gebruik. De Dav4-serie en de Dasv4-serie hebben dezelfde geheugen-en schijf configuraties als de D & Dsv3-serie.
  
## <a name="b-series"></a>B-serie

Premium Storage: ondersteund

Premium Storage caching: niet ondersteund

De beproefde virtuele machines uit de B-serie zijn ideaal voor werk belastingen die de volledige prestaties van de CPU niet continu nodig hebben, zoals webservers, kleine data bases en ontwikkel-en test omgevingen. Deze werk belastingen hebben doorgaans een burstieve prestatie vereisten. De B-serie biedt deze klanten de mogelijkheid om een VM-grootte aan te schaffen met een prijs bewuste basislijn prestaties waarmee de VM-instantie tegoeden kan bouwen wanneer de virtuele machine minder is dan de basis prestaties. Wanneer de virtuele machine tegoed heeft gecumuleerd, kan de virtuele machine op de basis lijn van de virtuele machine worden gebursteerd met Maxi maal 100% van de CPU wanneer voor uw toepassing hogere CPU-prestaties zijn vereist.

Voor beelden van use cases zijn: ontwikkelings-en test servers, webservers met weinig verkeer, kleine data bases, micro Services, servers voor het controleren van concepten, build-servers.


| Grootte             | vCPU  | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Basis CPU-prestaties van VM | Maximale CPU-prestaties van VM | Eerste tegoeden | Tegoeden per uur | Maximum aantal gestorte tegoeden | Maximaal aantal gegevensschijven | Max. door Voer in cache en tijdelijke opslag: IOPS/MBps | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale aantal NIC's |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200/10                                  | 160/10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480/75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640/100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls wordt alleen ondersteund op Linux

## <a name="dsv3-series-sup1sup"></a>Dsv3-serie <sup>1</sup>

ACU: 160-190

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Dsv3-serie grootten worden uitgevoerd op Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors met Intel Turbo Boost Technology 2,0 en Premium Storage gebruiken. De grootten van de Dsv3-serie bieden een combinatie van vCPU, geheugen en tijdelijke opslag voor de meeste productiewerkbelastingen.


| Grootte             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000/768 (1200)                                                    | 76800/1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000/1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> Dsv3-serie VM-functie Intel® Hyper-Threading-technologie

## <a name="dasv4-series"></a>Dasv4-serie

ACU: 230-260

Premium Storage: ondersteund

Premium Storage caching: ondersteund

De grootte van de Dasv4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een hogere maximum frequentie van 3.35 GHz kan bereiken en Premium SSD kan gebruiken. De grootte van de Dasv4-serie biedt een combi natie van vCPU, geheugen en tijdelijke opslag voor de meeste productiewerk belastingen.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 / 16000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup> Deze grootten zijn beschikbaar als preview-versie.  Als u geïnteresseerd bent in het proberen van deze grotere grootten, meldt u zich aan bij [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dv3-series-sup1sup"></a>Dv3-serie <sup>1</sup>

ACU: 160-190

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Dv3-serie grootten worden uitgevoerd op Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors met Intel Turbo Boost Technology 2,0. De Dv3-serie biedt een combinatie van vCPU, geheugen en tijdelijke opslag voor de meeste productieworkloads.

Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium Storage-schijven wilt gebruiken, gebruik dan de Dsv3-grootten. De prijs- en factureringsmeters voor de Dsv3-grootten zijn gelijk aan die van de Dv3-serie. 


| Grootte            | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximum aantal Nic's/netwerk bandbreedte (Mbps) |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> Dv3-serie VM-functie Intel® Hyper-Threading-technologie

## <a name="dav4-series"></a>Dav4-serie

ACU: 230-260

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

De grootte van de Dav4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een maximale maximum frequentie van 3.35 GHz kan bereiken. De grootte van de Dav4-serie biedt een combi natie van vCPU, geheugen en tijdelijke opslag voor de meeste productiewerk belastingen. Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium SSD wilt gebruiken, gebruikt u de Dasv4-grootten. De prijs-en facturerings meters voor Dasv4-grootten zijn gelijk aan die van de Dav4-serie.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12.000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24.000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48.000 / 750 / 375 |8 / 16000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup> Deze grootten zijn beschikbaar als preview-versie.  Als u geïnteresseerd bent in het proberen van deze grotere grootten, meldt u zich aan bij [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dsv2-series"></a>DSv2-serie

ACU: 210-250

Premium Storage: ondersteund

Premium Storage caching: ondersteund

DSv2-serie grootten worden uitgevoerd op Intel® Xeon® 8171M 2.1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors met Intel Turbo Boost Technology 2,0 en Premium Storage gebruiken.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4000/32 (43) |3200/48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400/96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000/128 (172) |12800/192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200/768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-reeksen

ACU: 210-250

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

DSv2-serie grootten worden uitgevoerd op Intel® Xeon® 8171M 2.1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors met Intel Turbo Boost Technology 2,0.

| Grootte           | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximaal aantal gegevensschijven | Door Voer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4 x 500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8 x 500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12.000 / 187 / 93                                           | 16             | 16 x 500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24.000 / 375 / 187                                          | 32             | 32 x 500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48.000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-serie

ACU: 100

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte            | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2000                     |

## <a name="dc-series"></a>DC-serie

Premium Storage: ondersteund

Premium Storage caching: ondersteund



| Grootte          | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |
