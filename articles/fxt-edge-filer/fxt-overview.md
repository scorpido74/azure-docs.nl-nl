---
title: Overzicht van Microsoft Azure FXT Edge-bestand
description: Beschrijft de hybride opslag cache van Azure FXT Edge, een Active Archive-en File Access Accelerator-oplossing voor High Performance Computing
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72254839"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Wat is de hybride opslag cache van de Azure FXT Edge-bestands opslag?

Azure FXT Edge filer is een hybride opslag cache-apparaat dat snelle toegang tot bestanden en een actief archief biedt voor HPC-taken (High-Performance Computing).

Het werkt met meerdere gegevens bronnen, ongeacht of deze zijn opgeslagen in een lokaal Data Center, op afstand of in de Cloud. De Azure FXT Edge-Bestandsr kan een uniforme naam ruimte bieden voor gegevens in verschillende opslag systemen.

Er zijn drie of meer hardwareapparaten voor FXT-apparaten die samen als geclusterd bestands systeem worden gebruikt om de cache op te geven. Neem contact op met uw micro soft-vertegenwoordiger voor meer informatie over het aanschaffen van de vereiste hardware. 

Lees voor meer informatie de product informatie en het gegevens blad op de [Azure FXT Edge-bestandsr](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Gebruiksvoorbeelden

De Azure FXT Edge-bestands extensie verbetert de productiviteit die het beste is voor werk stromen als deze:

* Werk stroom voor lezen van zware bestands toegang 
* NFSv3-of SMB2-protocollen
* Reken bedrijven van 1000 tot 100.000 CPU-kernen

### <a name="nas-optimization-and-scaling"></a>NAS-Optima Lise ring en schalen

U kunt de Azure FXT Edge-bestands cache gebruiken om de toegang tot bestaande NetApp-en Dell EMC Isilon NAS-systemen soepel te maken. U kunt ook Azure Blob of andere Cloud opslag toevoegen om de schaal baarheid te bieden zonder dat u gegevens toegangs processen aan de client zijde hoeft te bewerken. 

### <a name="wan-caching"></a>WAN-caching

Azure FXT Edge-bestand kan worden gebruikt voor het ondersteunen van snelle bestands toegang van hoofd gebruikers wanneer de gegevens die ze nodig hebben, elders worden opgeslagen. Geef toegang op bij het bewaren van back-ups en andere systemen voor gegevens beheer in een gecentraliseerd Data Center. 

### <a name="active-archive-in-azure-blob"></a>Actief archief in Azure Blob

Breid uw Data Center uit in de Cloud opslag met Azure FXT Edge-bestand als het toegangs punt. 

## <a name="features"></a>Functies 

Er zijn twee hardware modellen beschikbaar. 

| Model | DRAM | NVMe-SSD | Netwerkpoorten | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 GB/10Gb + 2 x 1 GB |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 GB/10Gb + 2 x 1 GB |


## <a name="next-steps"></a>Volgende stappen

* Ga door met het lezen van de [specificaties](fxt-specs.md) of [installatie handleiding](fxt-install.md)voor meer informatie over de Azure FXT Edge-bestands extensie.
* Meer informatie over het aanschaffen van de Azure FXT Edge-bestands extensie op de [product pagina van de Azure FXT Edge-website](https://azure.microsoft.com/services/fxt-edge-filer/).