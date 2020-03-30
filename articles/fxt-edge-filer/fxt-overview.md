---
title: Overzicht van Microsoft Azure FXT Edge Filer
description: Beschrijft de hybride opslagcache van Azure FXT Edge Filer, een actieve oplossing voor archive- en bestandstoegangsversnellervoor high performance computing
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72254839"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Wat is de hybride opslagcache van Azure FXT Edge Filer?

Azure FXT Edge Filer is een hybride opslagapparaat dat snelle bestandstoegang en actief archief biedt voor HPC-taken (High-Performance Computing).

Het werkt met meerdere gegevensbronnen, of het nu is opgeslagen in een lokaal datacenter, op afstand of in de cloud. De Azure FXT Edge Filer kan een uniforme naamruimte bieden voor gegevens in diverse opslagsystemen.

Drie of meer FXT Edge Filer-hardwareapparaten werken samen als een geclusterd bestandssysteem om de cache te bieden. Neem contact op met uw Microsoft-vertegenwoordiger voor meer informatie over de aankoop van de vereiste hardware. 

Lees voor meer informatie het productinformatie- en gegevensblad op [Azure FXT Edge Filer.](https://azure.microsoft.com/services/fxt-edge-filer/)

## <a name="use-cases"></a>Gebruiksvoorbeelden

De Azure FXT Edge Filer verhoogt de productiviteit het beste voor workflows zoals deze:

* Werkstroom voor leeszware bestandstoegang 
* NFSv3- of SMB2-protocollen
* Compute farms van 1000 tot 100.000 CPU-cores

### <a name="nas-optimization-and-scaling"></a>NAS-optimalisatie en -schaling

U de Azure FXT Edge Filer-cache gebruiken om de toegang tot bestaande NetApp- en Dell EMC Isilon-NAS-systemen soepel te maken. U ook Azure Blob of andere cloudopslag toevoegen om schaalbaarheid te bieden zonder dat u gegevenstoegangsprocessen aan de clientzijde opnieuw hoeft te werken. 

### <a name="wan-caching"></a>WAN-caching

Azure FXT Edge Filer kan worden gebruikt om snelle bestandstoegang van powerusers te ondersteunen wanneer de gegevens die ze nodig hebben elders worden opgeslagen. Zorg voor toegang en houd back-up- en andere gegevensbeheersystemen in een gecentraliseerd datacenter. 

### <a name="active-archive-in-azure-blob"></a>Actief archief in Azure Blob

Breid uw datacenter uit naar cloudopslag met Azure FXT Edge Filer als toegangspunt. 

## <a name="features"></a>Functies 

Er zijn twee hardwaremodellen beschikbaar. 

| Model | Dram | NVMe SSD | Netwerkpoorten | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |


## <a name="next-steps"></a>Volgende stappen

* Blijf meer te weten komen over de Azure FXT Edge Filer door de [specificaties](fxt-specs.md) of [installatiezelfstudie te](fxt-install.md)lezen.
* Meer informatie over de aankoop van Azure FXT Edge Filer op de [productpagina van Azure FXT Edge Filer.](https://azure.microsoft.com/services/fxt-edge-filer/)