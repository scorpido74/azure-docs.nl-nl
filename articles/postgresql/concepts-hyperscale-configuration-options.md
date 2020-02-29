---
title: Prestatie opties – grootschalige (Citus)-Azure Database for PostgreSQL
description: Opties voor een grootschalige (Citus)-Server groep, waaronder compute, opslag en regio's van knoop punten.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77462408"
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
    * Canada - midden
    * VS - centraal
    * VS - oost
    * VS - oost 2
    * VS - noord-centraal
    * VS - west 2
* Azië en Stille Oceaan:
    * Australië - oost
    * Japan - oost
    * Korea - centraal
    * Azië - zuidoost
* Europa
    * Europa - noord
    * Verenigd Koninkrijk Zuid
    * Europa -west

Sommige van deze regio's kunnen niet eerst worden geactiveerd op alle Azure-abonnementen. Als u een regio uit de bovenstaande lijst wilt gebruiken en deze niet in uw abonnement wilt zien, of als u een regio wilt gebruiken die niet in deze lijst voor komt, opent u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Prijzen
Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/)voor services voor de meest actuele prijs informatie.
Voor een overzicht van de kosten voor de gewenste configuratie [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) , worden de maandelijkse kosten weer gegeven op het tabblad **configureren** op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit en kies **Azure database for PostgreSQL – grootschalige (Citus)** om de opties aan te passen.
 
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [maken van een grootschalige (Citus)-Server groep in de portal](quickstart-create-hyperscale-portal.md).
