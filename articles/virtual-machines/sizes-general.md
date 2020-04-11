---
title: Azure VM-formaten - Algemeen doel | Microsoft Documenten
description: Hier worden de verschillende algemene doelgroottes weergegeven die beschikbaar zijn voor virtuele machines in Azure. Bevat informatie over het aantal vCPU's, gegevensschijven en NIC's, evenals opslagdoorvoer en netwerkbandbreedte voor formaten in deze serie.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 22826c32dc2aee0e580ec0b2a05c7eb8f08b7570
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115312"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Grootte van virtuele machines voor algemeen gebruik

VM-formaten voor algemeen gebruik bieden een gebalanceerde CPU-geheugenverhouding. Dit is ideaal voor testen en ontwikkelen, voor kleine tot middelgrote databases, en webservers met weinig tot gemiddeld verkeer. Dit artikel bevat informatie over het aanbod voor algemene computing.

- De VM's uit de [Av2-serie](av2-series.md) kunnen worden geïmplementeerd op verschillende hardwaretypen en -processors. Vm's uit de A-serie hebben CPU-prestaties en geheugenconfiguraties die het meest geschikt zijn voor instapworkloads zoals ontwikkeling en test. De grootte is afhankelijk van de hardware, zodat er consistente processorprestaties voor het actieve exemplaar kunnen worden geboden, ongeacht de hardware waarop deze is geïmplementeerd. Om de fysieke hardware te bepalen waarop deze grootte is geïmplementeerd, vraagt u vanuit de virtuele machine gegevens over de virtuele hardware op. Voorbeelden van voorbeelden zijn ontwikkel- en testservers, webservers met weinig verkeer, kleine tot middelgrote databases, proof-of-concepts en coderepositories.

  > [!NOTE]
  > De A8 – A11 VM's zijn gepland voor pensionering op 3/2021. Zie [HPC-migratiegids](https://azure.microsoft.com/resources/hpc-migration-guide/)voor meer informatie.

- [B-serie burstable](sizes-b-series-burstable.md) VM's zijn ideaal voor workloads die niet continu de volledige prestaties van de CPU nodig hebben, zoals webservers, kleine databases en ontwikkel- en testomgevingen. Deze workloads hebben doorgaans burstable prestatievereisten. De B-serie biedt deze klanten de mogelijkheid om een VM-formaat te kopen met een prijsbewuste basislijnprestaties waarmee de VM-instantie credits kan opbouwen wanneer de VM minder gebruikt dan zijn basisprestaties. Wanneer de VM krediet heeft verzameld, kan de VM boven de basislijn van de VM barsten met maximaal 100% van de CPU wanneer uw toepassing de hogere CPU-prestaties vereist.

- [Dav4- en Dasv4-serie](dav4-dasv4-series.md) zijn nieuwe formaten met gebruikmakende AMD's 2,35 Ghz EPYC<sup>TM</sup> 7452-processor in een multithread-configuratie met maximaal 256 MB L3-cache die 8 MB van die L3-cache wijdt aan elke 8 cores die de klantopties vergroten voor het uitvoeren van hun workloads voor algemeen gebruik. De Dav4-serie en Dasv4-serie hebben dezelfde geheugen- en schijfconfiguraties als de D-& Dsv3-serie.

- De [DCv2-serie](dcv2-series.md) kan helpen de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen terwijl deze in de openbare cloud worden verwerkt. Deze machines worden ondersteund door de nieuwste generatie Intel XEON E-2288G Processor met SGX-technologie. Met de Intel Turbo Boost Technology kunnen deze machines tot 5,0 GHz gaan. Met dcv2-serie-exemplaren kunnen klanten veilige toepassingen op basis van enclaves bouwen om hun code en gegevens te beschermen terwijl deze worden gebruikt.

- [Dv2 en Dsv2-serie](dv2-dsv2-series.md) VM's, een opvolger van de originele D-serie, beschikt over een krachtigere CPU en een optimale CPU-to-memory configuratie waardoor ze geschikt zijn voor de meeste productieworkloads. De Dv2-serie is ongeveer 35% sneller dan de D-serie. De Dv2-serie draait op de Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors met de Intel Turbo Boost Technology 2.0. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

- [Dv3 en Dsv3-serie](dv3-dsv3-series.md) Vm's draaien op de Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors in een hyper-threaded configuratie, die een betere waarde propositie voor de meeste algemene doeleinden workloads. Het geheugen is uitgebreid (van ~ 3,5 GiB / vCPU tot 4 GiB / vCPU), terwijl schijf- en netwerklimieten per core zijn aangepast om af te stemmen op de overgang naar hyperthreading. De Dv3-serie heeft niet langer het hoge geheugen VM maten van de D / Dv2-serie, die zijn verplaatst naar het geheugen [geoptimaliseerdev3 en Esv3-serie](ev3-esv3-series.md).

Voorbeelden van use cases uit de D-serie zijn enterprise-grade applicaties, relationele databases, in-memory caching en analytics.

## <a name="other-sizes"></a>Andere maten

- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
