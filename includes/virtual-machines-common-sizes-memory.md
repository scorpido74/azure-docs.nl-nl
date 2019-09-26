---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 18a19f5f853206b06c617307c761a99033c9f92a
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266922"
---
Voor geheugen geoptimaliseerde VM-grootten bieden een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, gemiddeld tot grote caches en analyse in het geheugen. In dit artikel vindt u informatie over het aantal Vcpu's, gegevens schijven en Nic's, evenals opslag doorvoer en netwerk bandbreedte voor elke grootte in deze groep.

* De Ev3-serie bevat de E5-2673 2,3 v4-processor (Broadwell) in een configuratie met Hyper-Threading, met een betere waarde voor de meeste werk belastingen voor algemeen gebruik en brengt de Ev3 in op uitlijning met de virtuele machines voor algemeen gebruik van de meeste andere Clouds.  Het geheugen is uitgevouwen (van 7 GiB/vCPU tot 8 GiB/vCPU), terwijl de schijf-en netwerk limieten per kern zijn aangepast om te worden uitgelijnd met de verplaatsing van hyperthreading.  De Ev3 is de follow-up van de VM-grootten van het hoge geheugen van de D/dv2-families.

* De preview-grootten van de Eav3-serie en de Easv3-serie maken gebruik van de 2.35 GHz EPYC<sup>TM</sup> 7452-processor van AMD in een configuratie met meerdere threads met Maxi maal 256 MB L3-cache, waarmee u de meeste geheugen optimalisatie opties kunt uitvoeren.  De Eav3-serie en de Easv3-serie hebben dezelfde geheugen-en schijf configuraties als de Ev3 & Esv3-serie.

* De Mv2-serie biedt het hoogste aantal vCPU (Maxi maal 208 Vcpu's) en het grootste geheugen (Maxi maal 5,7 TiB) van elke virtuele machine in de Cloud. Dit is ideaal voor zeer grote databases of andere toepassingen die zo kunnen profiteren van een groot aantal vCPU’s en grote hoeveelheden geheugen.

* De M-serie biedt een hoog aantal vCPU (Maxi maal 128 Vcpu's) en een grote hoeveelheid geheugen (Maxi maal 3,8 TiB). Het is ook ideaal voor zeer grote data bases of andere toepassingen die profiteren van hoog aantal vCPU en grote hoeveel heden geheugen.

* De dv2-serie, G-serie en de DSv2/GS-equivalenten zijn ideaal voor toepassingen die snellere Vcpu's, betere prestaties van de tijdelijke opslag of een hogere hoeveelheid geheugen vereisen. Ze bieden een krachtige combinatie voor vele toepassingen op bedrijfsniveau.

* De Dv2-serie, de opvolger van de oorspronkelijke D-serie, heeft een krachtigere CPU. De CPU van de Dv2-serie is ongeveer 35% sneller dan de CPU van de D-serie. Het is gebaseerd op de nieuwste generatie 2,4 GHz Intel Xeon® E5-2673 v3 2,4 GHz (Haswell) of E5-2673 v4 2,3 GHz (Broadwell)-processors en met de Intel Turbo Boost-technologie van 2,0, kan hij tot 3,1 GHz gaan. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

* Azure Compute biedt formaten virtuele machines die zijn geïsoleerd voor een specifiek hardwaretype en bedoeld voor een enkele klant.  Deze formaten virtuele machines zijn ideaal voor workloads waarvoor een hoge mate van isolatie van andere klanten vereist is en voor workloads waarbij elementen als naleving en wettelijke vereisten een rol spelen.  Klanten kunnen er ook voor kiezen om de resources van deze geïsoleerde virtuele machines verder te onderverdelen met behulp [van Azure-ondersteuning voor geneste virtuele machines](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Raadpleeg de tabel met virtuele-machine families hieronder voor de opties voor de geïsoleerde VM.

## <a name="esv3-series"></a>Esv3-serie

ACU 160-190 <sup>1</sup>

Premium-opslag:  Ondersteund

Premium Storage caching:  Ondersteund

Exemplaren uit de ESv3-serie zijn gebaseerd op Intel XEON ® E5-2673 v4-processors van 2,3 GHz (Broadwell) en kunnen maar liefst 3,5 GHz bereiken door de Intel Turbo Boost Technology 2.0 en maken gebruik van Premium Storage. Ev3-exemplaren zijn ideaal voor geheugenintensieve bedrijfstoepassingen.


| Size             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor opslag in cache en tijdelijke opslag: IOPS/MBps (cache grootte in GiB) | Maximale doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> Esv3-serie VM-functie Intel® Hyper-Threading-technologie.

Er zijn <sup>2</sup> beperkte core-grootten beschikbaar.

<sup>3</sup> exemplaar is geïsoleerd voor hardware die is toegewezen aan één klant.

## <a name="easv3-series-preview"></a>Easv3-serie (preview-versie)

Premium-opslag: Ondersteund

Premium Storage caching: Ondersteund

Groottes van de Easv3-serie zijn gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een gestimuleerde Fmax van 3.35 GHz kan bereiken en Premium-opslag gebruiken. De grootten van de Easv3-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen.

[Klik hier om u aan te melden voor de preview-versie](http://aka.ms/azureamdpreview).

| Size | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD): GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Ev3-serie 

ACU 160 - 190 <sup>1</sup>

Premium-opslag:  Niet ondersteund

Premium Storage caching:  Niet ondersteund

Exemplaren uit de Ev3-serie zijn gebaseerd op Intel XEON ® E5-2673 v4-processors van 2,3 GHz (Broadwell) en kunnen maar liefst 3,5 GHz bereiken door de Intel Turbo Boost Technology 2.0. Ev3-exemplaren zijn ideaal voor geheugenintensieve bedrijfstoepassingen.

Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium Storage-schijven wilt gebruiken, gebruik dan de ESv3-grootten. De prijs- en factureringsmeters voor de ESv3-grootten zijn gelijk aan die van de Ev3-serie. 


| Size            | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale tijdelijke opslag doorvoer: IOPS/Lees MBps/MBps schrijven | Max. aantal NIC's/netwerkbandbreedte |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Ev3-serie VM-functie Intel® Hyper-Threading-technologie.

Er zijn <sup>2</sup> beperkte core-grootten beschikbaar.

<sup>3</sup> exemplaar is geïsoleerd voor hardware die is toegewezen aan één klant.

## <a name="eav3-series-preview"></a>Eav3-serie (preview-versie)

Premium-opslag: Niet ondersteund

Premium Storage caching: Niet ondersteund

Groottes van de Eav3-serie zijn gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een gestimuleerde Fmax van 3.35 GHz kan bereiken en Premium-opslag gebruiken. De grootten van de Eav3-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen. Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium Storage-schijven wilt gebruiken, gebruikt u de grootten van de Easv3-serie. De prijs-en facturerings meters voor Easv3-grootten zijn gelijk aan die van de Eav3-serie.

[Klik hier om u aan te melden voor de preview-versie](http://aka.ms/azureamdpreview).

| Size             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD): GiB |
|------------------|------|-------------|-------------------------|
| Standard_E2a_v3  | 2    | 16          | 50                      |
| Standard_E4a_v3  | 4    | 32          | 100                     |
| Standard_E8a_v3  | 8    | 64          | 200                     |
| Standard_E16a_v3 | 16   | 128         | 400                     |
| Standard_E32a_v3 | 32   | 256         | 800                     |
| Standard_E48a_v3 | 48   | 384         | 1200                    |
| Standard_E64a_v3 | 64   | 432         | 1600                    |

## <a name="mv2-series"></a>Mv2-serie

Premium-opslag: Ondersteund

Premium Storage caching: Ondersteund

Write Accelerator: [Ondersteund](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

De Mv2-serie biedt een hoge door Voer, lage latentie, rechtstreeks toegewezen lokale NVMe-opslag die wordt uitgevoerd op een Hyper-Threaded Intel® Xeon® Platinum 8180M 2.5 GHz (Skylake)-processor met een basis frequentie van 2,5 GHz en een maximale Turbo frequentie van 3,8 GHz. Alle grootten voor virtuele machines uit de Mv2-serie kunnen zowel standaard als permanente schijven gebruiken. Instanties van de Mv2-serie zijn geoptimaliseerd voor geheugen die ongeëvenaarde reken prestaties bieden ter ondersteuning van grote in-memory data bases en workloads, met een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, grote caches en in het geheugen analyse. 

|Size | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor opslag in cache en tijdelijke opslag: IOPS/MBps (cache grootte in GiB) | Maximale doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000/1000 | 8 / 16000 |

Mv2-serie VM-functie Intel® Hyper-Threading-technologie  

<sup>1</sup> voor deze grote vm's is een van de volgende ondersteunde gast systemen vereist: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> vm's uit de Mv2-serie zijn alleen voor de generatie 2. Als u Linux gebruikt, raadpleegt u de volgende sectie voor het zoeken en selecteren van een SUSE Linux-installatie kopie.

#### <a name="find-a-suse-image"></a>Een SUSE-installatie kopie zoeken

Een juiste SUSE Linux-installatie kopie selecteren in de Azure Portal: 

1. Selecteer in het Azure Portal **een resource maken** 
1. Zoeken naar ' SUSE SAP ' 
1. SLES voor SAP Generation 2-installatie kopieën zijn beschikbaar als betalen per gebruik of uw eigen abonnement (BYOS). Vouw in de zoek resultaten de gewenste afbeeldings categorie uit:

    * SUSE Linux Enterprise Server (SLES) voor SAP
    * SUSE Linux Enterprise Server (SLES) voor SAP (BYOS)
    
1. SUSE-installatie kopieën die compatibel zijn met de Mv2-serie, worden `GEN2:`voorafgegaan door de naam. De volgende SUSE-installatie kopieën zijn beschikbaar voor Vm's uit de Mv2-serie:

    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 voor SAP-toepassingen
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 voor SAP-toepassingen
    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 voor SAP-toepassingen (BYOS)
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 voor SAP-toepassingen (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Een SUSE-installatie kopie selecteren via Azure CLI

Gebruik de volgende [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) opdracht om een lijst weer te geven van de momenteel beschik bare SLES voor SAP-installatie kopie voor virtuele machines uit de Mv2-serie:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

Met de opdracht worden de momenteel beschik bare virtuele machines van de 2e generatie die beschikbaar zijn via SUSE, voor virtuele machines uit de Mv2-serie. 

Voorbeelduitvoer:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M-serie 

ACU 160-180 <sup>1</sup>

Premium-opslag:  Ondersteund

Premium Storage caching:  Ondersteund

Write Accelerator:  [Ondersteund](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor opslag in cache en tijdelijke opslag: IOPS/MBps (cache grootte in GiB) | Maximale doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000/1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |



<sup>1</sup> M-serie VM-functie Intel® Hyper-Threading-technologie

<sup>2</sup> voor meer dan 64 vCPU is een van de volgende ondersteunde gast systemen vereist: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 en Red Hat Enterprise Linux, CentOS 7,3 of Oracle Linux 7,3 met LIS 4.2.1.

<sup>3</sup> beperkte core-grootten beschikbaar.

<sup>4</sup> exemplaar is geïsoleerd voor hardware die is toegewezen aan één klant.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-serie 11-15

ACU 210 - 250 <sup>1</sup>

Premium-opslag:  Ondersteund

Premium Storage caching:  Ondersteund

| Size | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor opslag in cache en tijdelijke opslag: IOPS/MBps (cache grootte in GiB) | Maximale doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> de maximale schijf doorvoer (IOPS of Mbps) die mogelijk is met een virtuele machine uit de DSv2-serie kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven.  Zie [ontwerpen voor hoge prestaties](../articles/virtual-machines/windows/premium-storage-performance.md)voor meer informatie.  
<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.  
<sup>3</sup> beperkte core-grootten beschikbaar.  
<sup>4</sup> 25000 Mbps met versneld netwerken. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-serie 11-15

ACU 210-250

Premium-opslag:  Niet ondersteund

Premium Storage caching:  Niet ondersteund

| Size              | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslag doorvoer: IOPS/Lees MBps/MBps schrijven | Maximum aantal gegevens schijven/door Voer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12.000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24.000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48.000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60.000 / 937 / 468                                        | 64/64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.  
<sup>2</sup> 25000 Mbps met versneld netwerken. 
