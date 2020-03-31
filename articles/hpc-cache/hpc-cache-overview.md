---
title: Overzicht van Azure HPC-cache
description: Beschrijft Azure HPC Cache, een systeemversneller voor bestandstoegang voor krachtige computing
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2a008d22de5df8d091e868153205697b4bb343ee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241190"
---
# <a name="what-is-azure-hpc-cache"></a>Wat is Azure HPC Cache?

Azure HPC Cache versnelt de toegang tot uw gegevens voor HPC-taken (High-Performance Computing). Door bestanden in Azure te plaatsen, brengt Azure HPC Cache de schaalbaarheid van cloud computing naar uw bestaande workflow. Deze service kan zelfs worden gebruikt voor werkstromen waarbij uw gegevens worden opgeslagen via WAN-koppelingen, zoals in uw lokale NAS-omgeving (Network-attached Storage).

Azure HPC-cache is eenvoudig te starten en te bewaken vanaf de Azure-portal. Bestaande NFS-opslag of nieuwe Blob-containers kunnen deel gaan uitmaken van de geaggregeerde naamruimte, waardoor clienttoegang eenvoudig is, zelfs als u het back-endopslagdoel wijzigt.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Azure HPC-cache verhoogt de productiviteit het beste voor workflows zoals deze:

* Werkstroom voor leeszware bestandstoegang
* Gegevens die zijn opgeslagen in nfs-toegankelijke opslag, Azure Blob of beide
* Compute farms van maximaal 75.000 CPU-cores

Azure HPC-cache kan worden toegevoegd aan een breed scala aan werkstromen in vele bedrijfstakken. Elk systeem waarbij een groot aantal machines toegang nodig heeft tot een set bestanden op schaal en met een lage latentie, zal van deze service profiteren. De onderstaande secties geven specifieke voorbeelden.

### <a name="visual-effects-vfx-rendering"></a>Visual effects (VFX) rendering

In media en entertainment kan Azure HPC Cache de toegang tot gegevens versnellen voor tijdkritische renderingprojecten. VFX-renderingworkflows vereisen vaak last-minute verwerking door grote aantallen compute nodes. Gegevens voor deze werkstromen bevinden zich doorgaans in een on-premises NAS-omgeving. Azure HPC-cache kan die bestandsgegevens in de cloud in de cache opslaan om de latentie te verminderen en de flexibiliteit voor on-demand rendering te verbeteren.

### <a name="life-sciences"></a>Biowetenschappen

Veel life sciences workflows kunnen profiteren van scale-out file caching.

Een onderzoeksinstituut dat zijn genomische analyseworkflows wil overzetten naar Azure, kan deze eenvoudig verschuiven met Azure HPC-cache. Omdat de cache POSIX-bestandstoegang biedt, zijn er geen wijzigingen aan de clientzijde nodig om hun bestaande clientworkflow in de cloud uit te voeren.

Azure HPC-cache kan ook worden gebruikt om de efficiëntie te verbeteren in taken zoals secundaire analyse, farmacologische simulatie of AI-gestuurde beeldanalyse.

### <a name="financial-services-analytics"></a>Analyse voor financiële dienstverlening

Een Azure HPC-cache-implementatie kan helpen bij het versnellen van kwantitatieve analyseberekeningen, risicoanalyseworkloads en Monte Carlo-simulaties om financiële dienstverleners beter inzicht te geven om strategische beslissingen te nemen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Azure HPC-cache is beschikbaar in deze Azure-regio's:

* VS - oost
* VS - oost 2
* Europa - noord
* Europa -west
* Azië - zuidoost
* Sydney
* VS - west 2
* Korea - centraal

Controleer de [productpagina azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) voor de meest recente beschikbaarheidsinformatie.

## <a name="service-availability"></a>Beschikbaarheid van service

U moet toegang aanvragen voor elk abonnement dat u gebruikt met Azure HPC-cache. Deze beperking zorgt voor de kwaliteit van de dienstverlening in de eerste maanden van algemene beschikbaarheid.

Vraag toegang aan door [dit formulier](https://aka.ms/onboard-hpc-cache)in te vullen. Nadat uw abonnement is toegevoegd aan de toegangslijst, u caches maken.

## <a name="next-steps"></a>Volgende stappen

* Lees de [productpagina azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) voor meer informatie over de mogelijkheden
* Meer informatie over [productvereisten](hpc-cache-prereqs.md)
* [Een Azure HPC-cache maken](hpc-cache-create.md) vanuit de Azure-portal
