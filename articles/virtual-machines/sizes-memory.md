---
title: Azure VM-formaten - Geheugen | Microsoft Documenten
description: Geeft een lijst van de verschillende geheugengeoptimaliseerde formaten die beschikbaar zijn voor virtuele machines in Azure. Bevat informatie over het aantal vCPU's, gegevensschijven en NIC's, evenals opslagdoorvoer en netwerkbandbreedte voor formaten in deze serie.
services: virtual-machines
documentationcenter: ''
author: mimckitt
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
ms.author: mimckitt
ms.openlocfilehash: 5a20e9c64b6ef948167333b54b16b34e84dc0e32
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273576"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Geheugen geoptimaliseerde virtuele machine maten

Geheugengeoptimaliseerde VM-formaten bieden een hoge geheugen-cpu-verhouding die ideaal is voor relationele databaseservers, middelgrote tot grote caches en in-memory analytics. In dit artikel vindt u informatie over het aantal vCPU's, gegevensschijven en NIC's, evenals opslagdoorvoer en netwerkbandbreedte voor elke grootte in deze groepering.

- [Dv2 en DSv2-serie](dv2-dsv2-series-memory.md), een vervolg op de originele D-serie, beschikt over een krachtigere CPU. De Dv2-serie is ongeveer 35% sneller dan de D-serie. Het draait op&reg; &reg; de Intel Xeon 8171M 2.1 GHz (Skylake) of&reg; de Intel Xeon&reg; E5-2673&reg; &reg; v4 2,3 GHz (Broadwell) of de Intel Xeon E5-2673 v3 2,4 GHz (Haswell) processors, en met de Intel Turbo Boost Technology 2.0. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

    De Dv2- en DSv2-serie zijn ideaal voor toepassingen die snellere vCPU's, betere tijdelijke opslagprestaties of hogere geheugeneisen vereisen. Ze bieden een krachtige combinatie voor vele toepassingen op bedrijfsniveau.

- De [Eav4- en Easv4-serie](eav4-easv4-series.md) maken gebruik van AMD's 2,35 Ghz EPYC<sup>TM</sup> 7452-processor in een multithread-configuratie met maximaal 256 MB L3-cache, waardoor de opties voor het uitvoeren van de meeste geheugengeoptimaliseerde workloads toenemen. De Eav4-serie en Easv4-serie hebben dezelfde geheugen- en schijfconfiguraties als de Ev3-& Esv3-serie.

- De [Ev3- en Esv3-serie](ev3-esv3-series.md) &reg; Intel&reg; Xeon 8171M 2,1&reg; GHz (Skylake) of de Intel Xeon&reg; E5-2673 v4 2,3 GHz (Broadwell) processor in een hyper-threaded configuratie, die een betere waarde propositie voor de meeste algemene doeleinden workloads, en waardoor de Ev3 in overeenstemming met het algemene doel VM's van de meeste andere clouds. Het geheugen is uitgebreid (van 7 GiB/vCPU naar 8 GiB/vCPU), terwijl schijf- en netwerklimieten per core zijn aangepast om af te stemmen op de overgang naar hyperthreading. De Ev3 is de opvolger van de vm-formaten met een hoog geheugen van de D/Dv2-families.

- De [M-serie](m-series.md) biedt een hoog aantal vCPU's (tot 128 vCPU's) en een grote hoeveelheid geheugen (tot 3,8 TiB). Het is ook ideaal voor extreem grote databases of andere toepassingen die profiteren van hoge vCPU-tellingen en grote hoeveelheden geheugen.

- De [Mv2-serie](mv2-series.md) biedt het hoogste vCPU-aantal (tot 416 vCPU's) en het grootste geheugen (tot 11,4 TiB) van elke VM in de cloud. Het is ideaal voor extreem grote databases of andere toepassingen die profiteren van hoge vCPU-tellingen en grote hoeveelheden geheugen.

Azure Compute biedt virtuele machineformaten die zijn geïsoleerd voor een specifiek hardwaretype en zijn toegewezen aan één klant. Deze virtuele machineformaten zijn het meest geschikt voor workloads die een hoge mate van isolatie van andere klanten vereisen voor workloads met elementen zoals naleving en wettelijke vereisten. Klanten kunnen er ook voor kiezen om de resources van deze geïsoleerde virtuele machines verder te verdelen door [Azure-ondersteuning te gebruiken voor geneste virtuele machines.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) Bekijk hieronder de pagina's voor virtuele machinefamilies voor uw geïsoleerde VM-opties.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
