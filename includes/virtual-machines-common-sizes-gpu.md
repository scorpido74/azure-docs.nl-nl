---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: b9637265d263a75949d5a70c3e4f0ce06044d93c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901623"
---
Geoptimaliseerde VM-grootten zijn gespecialiseerde virtuele machines die beschikbaar zijn met één of meer NVIDIA-Gpu's. Deze grootten zijn ontworpen voor computerintensieve, grafisch intensieve en visualisatie werk belastingen. Dit artikel bevat informatie over het aantal en het type Gpu's, Vcpu's, gegevens schijven en Nic's. Opslag doorvoer en netwerk bandbreedte worden ook voor elke grootte in deze groepering opgenomen.

* **NC-, NCv2-en NCv3-** grootten zijn geoptimaliseerd voor computerintensieve en netwerkintensieve toepassingen en algoritmen. Enkele voor beelden zijn CUDA-en OpenCL-toepassingen en simulaties, AI en diep gaande lessen. De NCv3-serie is gericht op werk belastingen met hoge prestaties, met de Tesla V100 GPU van NVIDIA. De NC-serie maakt gebruik van de Intel Xeon E5-2690 v3 2,60 GHz v3-processor (Haswell) en de virtuele machines uit de NCv2-en NCv3-serie gebruiken de Intel Xeon E5-2690 v4-processor (Broadwell).

* **ND en NDv2** De ND-serie is gericht op trainings-en ingrijpende scenario's voor diep gaande lessen. De oplossing maakt gebruik van de NVIDIA Tesla P40 GPU en de Intel Xeon E5-2690 v4-processor (Broadwell). De NDv2-serie maakt gebruik van de Intel Xeon Platinum 8168-processor (Skylake).

* De grootte van **NV en NVv3** is geoptimaliseerd en ontworpen voor externe visualisatie-, streaming-, Game-, coderings-en VDI-scenario's met behulp van frameworks zoals OpenGL en DirectX.  Deze Vm's worden ondersteund door de NVIDIA Tesla M60 GPU.

* **NVv4** -grootten zijn geoptimaliseerd en ontworpen voor VDI en externe visualisatie. Met partioned Gpu's biedt NVv4 de juiste grootte voor werk belastingen waarvoor kleinere GPU-bronnen nodig zijn.  Deze Vm's worden ondersteund door de AMD Radeon instinct MI25 GPU.


## <a name="nc-series"></a>NC-serie

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Virtuele machines in de NC-serie worden aangedreven door de [Nvidia Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) -kaart en de Intel Xeon E5-2690 v3-processor (Haswell). Gebruikers kunnen sneller gegevens belichten door gebruik te maken van CUDA voor energie onderzoek toepassingen, crash simulaties, Ray traceed rendering, dieper leren en meer. De NC24r-configuratie biedt een netwerk interface met lage latentie en hoge door Voer die is geoptimaliseerd voor nauw gekoppelde werk belastingen op parallelle computers.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Maximale aantal NIC's |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = halve K80-kaart.

*RDMA-compatibel

## <a name="ncv2-series"></a>NCv2-serie

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Virtuele machines uit de NCv2-serie worden aangedreven door [Nvidia Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) gpu's. Deze Gpu's kunnen meer dan twee maal de reken prestaties van de NC-serie bieden. Klanten kunnen profiteren van deze bijgewerkte Gpu's voor traditionele HPC-workloads, zoals het maken van tank modellen, DNA-sequentiëren, eiwithoudende analyse, Monte Carlo-simulaties en andere. Naast de Gpu's worden de virtuele machines uit de NCv2-serie ook aangedreven door de Intel Xeon E5-2690 v4-Cpu's (Broadwell).

De NC24rs v2-configuratie biedt een netwerk interface met lage latentie en hoge door Voer die is geoptimaliseerd voor nauw gekoppelde werk belastingen voor parallelle berekeningen.

> [!IMPORTANT]
> Voor deze grootte familie is het vCPU-quotum (core) in uw abonnement in eerste instantie ingesteld op 0 in elke regio. [Vraag een toename van een vCPU-quotum](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) voor deze familie aan in een [beschik bare regio](https://azure.microsoft.com/regions/services/).
>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD): GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale aantal NIC's |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = One P100-kaart.

*RDMA-compatibel

## <a name="ncv3-series"></a>NCv3-serie

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Virtuele machines uit de NCv3-serie worden aangedreven door [Nvidia Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) gpu's. Deze Gpu's kunnen 1,5 x de reken prestaties van de NCv2-serie bieden. Klanten kunnen profiteren van deze bijgewerkte Gpu's voor traditionele HPC-workloads, zoals het maken van tank modellen, DNA-sequentiëren, eiwithoudende analyse, Monte Carlo-simulaties en andere. De NC24rs v3-configuratie biedt een netwerk interface met lage latentie en hoge door Voer die is geoptimaliseerd voor nauw gekoppelde werk belastingen voor parallelle berekeningen. Naast de Gpu's worden de virtuele machines uit de NCv3-serie ook aangedreven door de Intel Xeon E5-2690 v4-Cpu's (Broadwell).

> [!IMPORTANT]
> Voor deze grootte familie is het vCPU-quotum (core) in uw abonnement in eerste instantie ingesteld op 0 in elke regio. [Vraag een toename van een vCPU-quotum](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) voor deze familie aan in een [beschik bare regio](https://azure.microsoft.com/regions/services/).
>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD): GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale aantal NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = One V100-kaart.

*RDMA-compatibel

## <a name="updated-ndv2-series-preview"></a>Bijgewerkte NDv2-serie (preview-versie)

Premium Storage: ondersteund

Premium Storage caching: ondersteund

InfiniBand: ondersteund

De virtuele machine uit de NDv2-serie is een nieuwe toevoeging aan de GPU-familie, ontworpen voor de behoeften van de meest veeleisende AI-, machine learning-, simulatie-en HPC-workloads van GPU. 

NDv2 wordt aangedreven door 8 NVIDIA Tesla V100 NVLINK-verbonden Gpu's, elk met 32 GB aan GPU-geheugen. Elke NDv2-VM beschikt ook over 40 niet-HyperThreaded Intel Xeon Platinum 8168 (Skylake)-kernen en 672 GiB van het systeem geheugen. 

NDv2-instanties bieden uitstekende prestaties voor HPC-en AI-workloads die gebruikmaken van CUDA GPU-geoptimaliseerde reken kernels en de vele AI-, ML-en analyse hulpprogramma's die ondersteuning bieden voor GPU-versnelling ' out-of-Box ', zoals tensor flow, Pytorch, Caffe, RAPIDS en andere regelingen. 

De NDv2 is in de praktijk opgebouwd voor zowel reken kundige, intense schaling (8 Gpu's per VM) en uitschalen (waarbij meerdere Vm's samen werken). De NDv2-serie biedt nu ondersteuning voor 100 Gigabit InfiniBand EDR-back-end-netwerken, vergelijkbaar met die van de HB-serie HPC-VM, om hoogwaardige clusters mogelijk te maken voor parallelle scenario's, waaronder gedistribueerde trainingen voor AI en ML. Dit back-upnetwerk biedt ondersteuning voor alle belang rijke InfiniBand-protocollen, met inbegrip van die van de NCCL2-bibliotheken van NVIDIA, waardoor het clusteren van Gpu's naadloos kan worden gegroepeerd.

> Als u InfiniBand op de ND40rs_v2 virtuele machine [inschakelt](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) , gebruikt u het OFED-stuur programma 4.7-1.0.0.1 Mellanox.

> Vanwege het toegenomen GPU-geheugen vereist de nieuwe ND40rs_v2 VM het gebruik van [virtuele machines van de tweede generatie](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) en Marketplace-installatie kopieën. 

> [Meld u aan om vroegtijdige toegang tot de preview-versie van de virtuele machine NDv2 te vragen.](https://aka.ms/AzureNDrv2Preview)

> Opmerking: de ND40s_v2 met 16 GB per GPU-geheugen is niet meer beschikbaar voor preview en is vervangen door de bijgewerkte ND40rs_v2.
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD): GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale netwerk bandbreedte | Maximale aantal NIC's |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

## <a name="nd-series"></a>ND-serie

Premium Storage: ondersteund

Premium Storage caching: ondersteund

De virtuele machines uit de ND-serie vormen een nieuwe toevoeging aan de GPU-familie die is ontworpen voor AI en diep gaande werk belastingen. Ze bieden uitstekende prestaties voor training en demijnen. ND-instanties worden aangedreven door [Nvidia Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) -Gpu's en Intel Xeon E5-2690 v4-Cpu's (Broadwell). Deze instanties bieden uitstekende prestaties voor drijvende-komma bewerkingen met één precisie, voor AI-workloads die gebruikmaken van Microsoft Cognitive Toolkit, tensor flow, Caffe en andere frameworks. De ND-serie biedt daarnaast een veel groter GPU-geheugen (24 GB), waardoor het mogelijk is om veel grotere modellen met een neuraal netwerk in te zetten. Net als de NC-serie biedt de ND-serie een configuratie met een secundaire netwerk met lage latentie en een hoge door Voer via RDMA en InfiniBand-connectiviteit, zodat u grootschalige trainings taken kunt uitvoeren die veel Gpu's bespannen.

> [!IMPORTANT]
> Voor deze grootte serie wordt het vCPU (kern quotum) per regio in uw abonnement in eerste instantie ingesteld op 0. [Vraag een toename van een vCPU-quotum](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) voor deze familie aan in een [beschik bare regio](https://azure.microsoft.com/regions/services/).
>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale aantal NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = One P40-kaart.

*RDMA-compatibel

## <a name="nv-series"></a>NV-serie

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

De virtuele machines van de NV-serie worden aangedreven door [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu's en de NVIDIA Grid-technologie voor desktop-versnelde toepassingen en virtuele Bureau bladen waar klanten hun gegevens of simulaties kunnen visualiseren. Gebruikers kunnen hun grafische intensieve werk stromen op de NV-exemplaren visualiseren om de superieure grafische mogelijkheden te verkrijgen en daarnaast ook werk belastingen met één precisie, zoals code ring en rendering, uit te voeren. Virtuele machines van de NV-serie worden ook aangedreven door de Intel Xeon E5-2690 v3-Cpu's (Haswell).

Elke GPU in NV-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werk station voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de virtuele machine voor een virtuele-toepassings scenario.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Maximale aantal NIC's | Virtuele werk stations | Virtuele toepassingen |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = halve M60-kaart.

## <a name="nvv3-series--sup1sup"></a>NVv3-serie <sup>1</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

De virtuele machines uit de NVv3-serie worden aangedreven door [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu's en de NVIDIA-raster technologie met Intel E5-2690 v4-Cpu's (Broadwell). Deze virtuele machines zijn gericht op GPU-versnelde grafische toepassingen en virtuele Bureau bladen waar klanten hun gegevens willen visualiseren, de resultaten kunnen simuleren om te bekijken, te werken met CAD of inhoud te genereren en te streamen. Daarnaast kunnen deze virtuele machines enkelvoudige, nauwkeurige workloads uitvoeren zoals encoding en renderen. Virtuele machines van NVv3 ondersteunen Premium Storage en komen twee maal zoveel systeem geheugen (RAM) te staan in vergelijking met de bijbehorende voorafgaande NV-serie.  

Elke GPU in NVv3-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werk station voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de virtuele machine voor een virtuele-toepassings scenario.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale aantal NIC's | Virtuele werk stations | Virtuele toepassingen | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = halve M60-kaart.

<sup>1</sup> virtuele machines uit de NVv3-serie beschikken over de Intel Hyper-Threading-technologie

## <a name="nvv4-series-preview--sup1sup"></a>NVv4-serie (preview-versie) <sup>1</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

De virtuele machines uit de NVv4-serie worden aangedreven door [AMD Radeon instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU'S en AMD EPYC 7V12 (Rome) cpu's. Met de NVv4-serie van Azure worden virtuele machines met gedeeltelijke Gpu's geïntroduceerd. Kies de virtuele machine met de juiste grootte voor GPU-versnelde grafische toepassingen en virtuele Bureau bladen, beginnend bij 1/achtste van een GPU met 2 GiB frame buffer naar een volledige GPU met 16 GiB frame buffer. Virtuele NVv4-machines ondersteunen momenteel alleen Windows-gast besturingssystemen.

[Meld u aan en krijg tijdens de preview-periode toegang tot deze machines](https://aka.ms/nvv4signup).
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Maximaal aantal gegevensschijven | Maximale aantal NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 | 
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 | 



<sup>1</sup> virtuele machines uit de NVv4-serie beschikken over AMD gelijktijdige multi-threading-technologie

