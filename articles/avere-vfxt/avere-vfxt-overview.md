---
title: Avere vFXT for Azure
description: Inleiding tot Avere vFXT for Azure, een cloud-cachelaag voor HPC
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: a9dab2ca844feb1c24dfffc48b4f29cd1a311314
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76153188"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Wat is Avere vFXT for Azure?

Avere vFXT for Azure is een oplossing voor bestandssysteemcaching voor gegevensintensieve high performance computing (HPC)-taken. Hiermee kunt u profiteren van de schaalbaarheid van cloudcomputing om uw gegevens toegankelijk te maken waar en wanneer ze nodig zijn; ook voor gegevens die zijn opgeslagen op uw eigen on-premises hardware.

Avere vFXT ondersteunt de volgende veelvoorkomende computerscenario's:

* Hybride cloudarchitectuur - Avere vFXT voor Azure kan werken met een hardwareopslagsysteem, dat het voordeel van cloud computing biedt zonder bestanden te hoeven verplaatsen.

* Cloud bursting - Avere vFXT voor Azure kan u helpen uw gegevens naar de cloud te verplaatsen voor één project, of de hele workflow permanent op te tillen en te verschuiven.

![diagram met details van het Avere vFXT-systeem in een Azure-abonnement dat is verbonden met Blob-opslag en een on-premises datacenter](media/avere-vfxt-hybrid.png)

Avere vFXT voor Azure is het meest geschikt voor deze situaties:

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

Met Avere vFXT kunnen onderzoekers secundaire analyseworkflows uitvoeren in Azure Compute en toegang krijgen tot genomische gegevens, ongeacht hun locatie.

In farmaceutisch onderzoek kunnen Avere vFXT-clusters de ontdekking van geneesmiddelen versnellen door onderzoekers te helpen interacties met drugstarget te voorspellen en onderzoeksgegevens te analyseren.

### <a name="financial-services-analytics"></a>Analyse voor financiële dienstverlening

Met een Avere vFXT-cluster kunnen kwantitatieve analyses worden versneld, waardoor financiële dienstverleners beter inzicht kunnen krijgen bij het maken van strategische besluiten.

## <a name="features-and-specifications"></a>Functies en specificaties

Het Avere vFXT-systeem bestaat uit drie of meer virtuele Edge-filerknooppunten die in een cluster zijn geconfigureerd. Het kan in de buurt van de clients worden geplaatst, waaraan het cluster wordt gekoppeld, in plaats van de opslag rechtsreeks.

In het Avere vFXT-cluster worden bestanden in de cache opgeslagen terwijl ze worden aangevraagd. Herhaalde aanvragen kunnen in meer dan tachtig procent van de tijd worden uitgevoerd vanuit de cache.

### <a name="compatibility"></a>Compatibiliteit

* Compatibel met hardware NAS-systemen van NetApp of Dell EMC Isilon
* Compatibel met Azure Blob
* Gebruikt het NFSv3- of SMB2-protocol

Avere vFXT voor Azure gebruikt de volgende Azure-resources:

|Azure-onderdeel|   |
|----------|-----------|
|Virtuele machines|3 of meer E32s_v3|
|Premium SSD-opslag|200 GB ruimte in het besturingssysteem plus 1 tot 4 TB ruimte in de cache per knooppunt |
|Opslagaccount (optioneel) |v2|
|Back-endopslag voor gegevens (optioneel) | Eén lege LRS Blob-container |

## <a name="next-steps"></a>Volgende stappen

Lees deze artikelen om uw eigen Avere vFXT voor Azure-implementatie te plannen en te maken.

* [Uw systeem plannen](avere-vfxt-deploy-plan.md)
* [Implementatieoverzicht](avere-vfxt-deploy-overview.md)
* [vFXT maken](avere-vfxt-deploy.md)
