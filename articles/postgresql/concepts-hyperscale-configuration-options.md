---
title: Azure Database for PostgreSQL – grootschalige (Citus)-prestatie opties
description: Opties voor een grootschalige (Citus)-Server groep, waaronder compute, opslag en regio's van knoop punten.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2b848827d3d2017e5e787989553c0bf1e26d48e7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482635"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – grootschalige (Citus)-prestatie opties

## <a name="compute-and-storage"></a>Compute en opslag
 
U kunt de berekenings-en opslag instellingen onafhankelijk selecteren voor worker-knoop punten en het coördinator knooppunt in een grootschalige (Citus)-Server groep.  Reken bronnen worden weer gegeven als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. De opslag grootte voor inrichting heeft betrekking op de capaciteit die beschikbaar is voor de coördinator en worker-knoop punten in uw grootschalige (Citus)-Server groep. De opslag omvat database bestanden, tijdelijke bestanden, transactie logboeken en de post gres-server Logboeken. De totale hoeveelheid opslag ruimte die u hebt ingericht, definieert ook de I/O-capaciteit die beschikbaar is voor elke werk nemer en coördinator knooppunt.
 
|                       | Worker-knoop punt           | Coördinator knooppunt      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Geheugen per vCore, GiB | 8                     | 4                     |
| Opslag grootte, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Opslagtype          | Algemeen gebruik (SSD) | Algemeen gebruik (SSD) |
| IOPS                  | Maxi maal 3 IOPS/GiB      | Maxi maal 3 IOPS/GiB      |


## <a name="regions"></a>Regio's
Grootschalige (Citus)-Server groepen zijn beschikbaar in de volgende Azure-regio's:

* Amerikaanse
    * Canada-centraal *
    * US - oost
    * US - oost 2
    * VS Noord-Centraal *
    * US - west 2
* Azië en Stille Oceaan:
    * Australië-oost *
    * Azië - zuidoost
* Europa
    * Europa - noord
    * VK - zuid
    * Europa -west

Regio's met een asterisk (\*) bieden nog geen ondersteuning voor [hoge Beschik baarheid](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Prijzen
Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/)voor services voor de meest actuele prijs informatie.
Voor een overzicht van de kosten voor de gewenste configuratie [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) , worden de maandelijkse kosten weer gegeven op het tabblad **configureren** op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit en kies **Azure database for PostgreSQL – grootschalige (Citus)** om de opties aan te passen.
 
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [maken van een grootschalige (Citus)-Server groep in de portal](quickstart-create-hyperscale-portal.md).
