---
title: Overzicht van de Azure HPC-cache
description: Hierin wordt de Azure HPC-cache, een oplossing voor bestands toegang voor High-Performance Computing beschreven
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: da853cd92797c6ec778043ddc39c7854d8f5e832
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70774719"
---
# <a name="what-is-azure-hpc-cache"></a>Wat is Azure HPC cache?

De Azure HPC-cache versnelt de toegang tot uw gegevens voor HPC-taken (High-Performance Computing). Door bestanden in Azure in de cache op te slaan, is de schaal baarheid van Cloud Computing ook beschikbaar voor werk stromen waarbij uw gegevens worden opgeslagen op WAN-koppelingen, zoals in uw lokale Datacenter omgeving (Network-Attached Storage).

De Azure HPC-cache is eenvoudig te starten en te controleren vanaf de Azure Portal. Bestaande NFS-opslag of nieuwe BLOB-containers kunnen deel uitmaken van de geaggregeerde naam ruimte, waardoor client toegang eenvoudig kan worden gemaakt, zelfs als u het back-end-opslag doel wijzigt.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Azure HPC cache verbetert de productiviteit die het beste is voor werk stromen als deze:

* Werk stroom voor lezen van zware bestands toegang
* Gegevens die zijn opgeslagen in met NFS toegankelijke opslag, Azure Blob of beide
* Compute-farms van Maxi maal 75.000 CPU-kernen

Een Azure HPC-cache kan worden toegevoegd aan een groot aantal werk stromen in veel branches. Elk systeem waarbij een groot aantal machines toegang moet hebben tot een set bestanden op schaal en met lage latentie voor deel is van deze service. De volgende secties geven specifieke voor beelden.

### <a name="visual-effects-vfx-rendering"></a>Rendering van visuele effecten (VFX)

In media en entertainment kan Azure HPC cache de toegang tot gegevens versnellen voor tijdgebonden rendering-projecten. VFX rendering-werk stromen vereisen vaak verwerking van de laatste minuut door grote aantallen reken knooppunten. Gegevens voor deze werk stromen bevinden zich doorgaans in een on-premises NAS-omgeving. Met de Azure HPC-cache kunt u de bestands gegevens in de Cloud opslaan om de latentie te verminderen en de flexibiliteit te verhogen voor rendering op aanvraag.

### <a name="life-sciences"></a>Biowetenschappen

Veel biowetenschappen-werk stromen kunnen profiteren van scale-out bestands caches.

Een onderzoek instituut dat de genomic-analyse werk stromen in azure wil bepoorten, kan deze eenvoudig verplaatsen met behulp van de Azure HPC-cache. Omdat de cache POSIX-bestands toegang biedt, kunnen ze hun bestaande werk stroom aan de client zijde in de Cloud uitvoeren zonder dat er wijzigingen hoeven te worden aangebracht.

De Azure HPC-cache kan ook worden gebruikt om de efficiëntie te verbeteren van taken zoals secundaire analyse, farmacologische simulatie of AI-gestuurde afbeeldings analyse.

### <a name="financial-services-analytics"></a>Analyse voor financiële dienstverlening

Een HPC-cache van Azure kan helpen bij het versnellen van kwantitatieve analyse berekeningen, workloads voor risico analyse en Monte Carlo-simulaties om financiële diensten bedrijven beter inzicht te geven in strategische beslissingen te nemen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

De Azure HPC-cache is beschikbaar in de volgende Azure-regio's:

* East US
* US - oost 2
* Europa - noord
* Europa -west
* Azië - zuidoost
* US - west 2

Controleer de [product pagina van de Azure HPC-cache](<https://azure.microsoft.com/services/hpc-cache>) voor de meest recente beschikbaarheids gegevens.

## <a name="next-steps"></a>Volgende stappen

* Lees de [product pagina van de Azure HPC-cache](<https://azure.microsoft.com/services/hpc-cache>) voor meer informatie over de mogelijkheden
* Meer informatie over product [vereisten](hpc-cache-prereqs.md)
* [Een HPC-cache van Azure maken](hpc-cache-create.md) op basis van de Azure Portal
