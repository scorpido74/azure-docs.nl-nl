---
title: Avere vFXT for Azure
description: Inleiding tot Avere vFXT for Azure, een cloud-cachelaag voor HPC
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 70ae20daa81ab52d4054dcd4bea3c9432a5ceaeb
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256168"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Wat is Avere vFXT for Azure? 

Avere vFXT for Azure is een oplossing voor bestandssysteemcaching voor gegevensintensieve high performance computing (HPC)-taken. Hiermee kunt u profiteren van de schaalbaarheid van cloudcomputing om uw gegevens toegankelijk te maken waar en wanneer ze nodig zijn; ook voor gegevens die zijn opgeslagen op uw eigen on-premises hardware.

Avere vFXT ondersteunt de volgende veelvoorkomende computerscenario's: 

* Hybride-cloudarchitectuur: Avere vFXT for Azure kan worden gebruikt met hardwareopslagsystemen, wat het voordeel biedt van cloudcomputing zonder dat u bestanden hoeft te verplaatsen. 
* Krachtige cloudtoepassingen: met Avere vFXT for Azure kunt u uw gegevens voor één project naar de cloud verplaatsen of kiezen voor 'lift-and-shift', waarmee u de hele werkstroom definitief naar de cloud verplaatst. 

![diagram met details van het Avere vFXT-systeem in een Azure-abonnement dat is verbonden met Blob-opslag en een on-premises datacenter](media/avere-vfxt-hybrid.png)

Avere vFXT for Azure is het geschiktst voor deze situaties: 

* Leesintensieve bewerkingen voor HPC-workloads
* Toepassingen die gebruikmaken van het NFS-protocol
* Serverfarms met 1000 tot 40.000 CPU-cores
* Integratie met on-premises hardware NAS, Azure Blob-opslag of beide

Zie <https://azure.microsoft.com/services/storage/avere-vfxt/> voor meer informatie

## <a name="who-uses-avere-vfxt-for-azure"></a>Wie gebruiken Avere vFXT for Azure? 

Avere vFXT is handig bij het lezen van allerlei soorten leesintensieve rekentaken:

### <a name="visual-effects-rendering"></a>Rendering van visuele effecten 

In media en entertainment kan het Avere vFXT-cluster de gegevenstoegang voor tijdgebonden renderingprojecten versnellen. Omdat u in Azure meer cacheruimte kunt toevoegen evenals meer rekenknooppunten, beschikt u over de flexibiliteit grote projecten efficiënt te kunnen afhandelen. 

### <a name="life-sciences"></a>Biowetenschappen 

Met Avere vFXT kunnen onderzoekers hun secundaire analysewerkstromen in Azure Compute uitvoeren en genetische gegevens openen, ongeacht de locatie ervan.

Bij farmaceutisch onderzoek kunnen Avere vFXT-clusters worden gebruikt om het vinden van nieuwe geneesmiddelen te versnellen doordat onderzoekers interacties tussen geneesmiddel en doel kunnen voorspellen en onderzoeksgegevens analyseren.

### <a name="financial-services-analytics"></a>Analyse voor financiële dienstverlening

Met een Avere vFXT-cluster kunnen kwantitatieve analyses worden versneld, waardoor financiële dienstverleners beter inzicht kunnen krijgen bij het maken van strategische besluiten. 

## <a name="features-and-specifications"></a>Functies en specificaties

Het Avere vFXT-systeem bestaat uit drie of meer virtuele Edge-filerknooppunten die in een cluster zijn geconfigureerd. Het kan in de buurt van de clients worden geplaatst, waaraan het cluster wordt gekoppeld, in plaats van de opslag rechtsreeks. 

In het Avere vFXT-cluster worden bestanden in de cache opgeslagen terwijl ze worden aangevraagd. Herhaalde aanvragen kunnen in meer dan tachtig procent van de tijd worden uitgevoerd vanuit de cache.

### <a name="compatibility"></a>Compatibiliteit 

* Compatibel met hardware NAS-systemen van NetApp of Dell EMC Isilon
* Compatibel met Azure Blob
* Gebruikt het NFSv3- of SMB2-protocol

De Avere vFXT gebruikt de volgende Azure-resources: 

|Azure-onderdeel|   |
|----------|-----------|
|Virtuele machines|3 of meer E32s_v3|
|Premium SSD-opslag|200 GB ruimte in het besturingssysteem plus 1 tot 4 TB ruimte in de cache per knooppunt |
|Opslagaccount (optioneel) |v2|
|Back-endopslag van gegevens (optioneel) | Eén lege LRS Blob-container |

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele koppelingen om uw eigen Avere vFXT-implementatie te starten. 

* [Het systeem plannen](avere-vfxt-deploy-plan.md)
* [Implementatie-overzicht](avere-vfxt-deploy-overview.md)
* [vFXT maken](avere-vfxt-deploy.md)
