---
title: Groottes van Azure VM-geheugen | Microsoft Docs
description: Geeft een lijst van de verschillende voor geheugen geoptimaliseerde grootten die beschikbaar zijn voor virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM-isolatie, geïsoleerde VM, isolatie, geïsoleerd
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493525"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Grootte van virtuele machines geoptimaliseerd voor geheugen

Voor geheugen geoptimaliseerde VM-grootten bieden een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, gemiddeld tot grote caches en analyse in het geheugen. In dit artikel vindt u informatie over het aantal Vcpu's, gegevens schijven en Nic's, evenals opslag doorvoer en netwerk bandbreedte voor elke grootte in deze groep.

- [Dv2 en DSv2-Series](dv2-dsv2-series-memory.md), een follow-on voor de oorspronkelijke D-serie, bevat een krachtigere CPU. De dv2-serie is ongeveer 35% sneller dan de D-serie. Het wordt uitgevoerd op Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors en met de Intel Turbo Boost-technologie van 2,0. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

    Dv2 en DSv2-serie zijn ideaal voor toepassingen waarvoor snellere Vcpu's, betere prestaties van de tijdelijke opslag of een hoger geheugen vereist zijn. Ze bieden een krachtige combinatie voor vele toepassingen op bedrijfsniveau.

- De [Eav4-en Easv4-serie](eav4-easv4-series.md) maken gebruik van de 2.35 GHz EPYC<sup>TM</sup> 7452-processor van AMD in een configuratie met meerdere THREADS met Maxi maal 256 MB L3-cache, waarmee u de meeste opties kunt verhogen voor het uitvoeren van de meeste geheugen geoptimaliseerde workloads. De Eav4-serie en de Easv4-serie hebben dezelfde geheugen-en schijf configuraties als de Ev3 & Esv3-serie.

- De [Ev3-en Esv3-serie](ev3-esv3-series.md) Intel® Xeon® 8171M 2,1 GHz (Skylake) of Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell)-processor in een Hyper-Threaded configuratie, met een betere toegevoegde waarde voor de meeste werk belastingen voor algemeen gebruik en brengt de Ev3 in overeenstemming met de virtuele machines voor algemeen gebruik van de meeste andere Clouds. Het geheugen is uitgevouwen (van 7 GiB/vCPU tot 8 GiB/vCPU), terwijl de schijf-en netwerk limieten per kern zijn aangepast om te worden uitgelijnd met de overgang naar Hyper Threading. De Ev3 is de follow-up van de VM-grootten van het hoge geheugen van de D/dv2-families.

- De [M-serie](m-series.md) biedt een hoog aantal vCPU (maxi maal 128 vcpu's) en een grote hoeveelheid geheugen (maxi maal 3,8 TIB). Het is ook ideaal voor zeer grote data bases of andere toepassingen die profiteren van hoog aantal vCPU en grote hoeveel heden geheugen.

- De [Mv2-serie](mv2-series.md) biedt het hoogste aantal vCPU (maxi maal 416 vcpu's) en het grootste geheugen (maxi maal 8,19 TIB) van elke virtuele machine in de Cloud. Dit is ideaal voor zeer grote databases of andere toepassingen die zo kunnen profiteren van een groot aantal vCPU’s en grote hoeveelheden geheugen.

Azure Compute biedt virtuele machine grootten die zijn geïsoleerd voor een specifiek hardwaretype en die zijn toegewezen aan één klant. Deze virtuele-machine grootten zijn het meest geschikt voor werk belastingen die een hoge mate van isolatie van andere klanten vereisen voor workloads met elementen zoals naleving en regelgeving. Klanten kunnen er ook voor kiezen om de resources van deze geïsoleerde virtuele machines verder te onderverdelen met behulp [van Azure-ondersteuning voor geneste virtuele machines](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Zie de pagina's voor de virtuele-machine families hieronder voor de opties voor de geïsoleerde VM.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.