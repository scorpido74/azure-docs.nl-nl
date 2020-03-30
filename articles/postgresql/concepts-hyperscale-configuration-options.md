---
title: Prestatieopties – Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Opties voor een Citus-servergroep (Hyperscale), inclusief node compute, opslag en regio's.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462408"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database voor PostgreSQL – Citus-prestatieopties (Hyperscale)

## <a name="compute-and-storage"></a>Compute en opslag
 
U de reken- en opslaginstellingen onafhankelijk selecteren voor werknemersknooppunten en het coördinatorknooppunt in een Servergroep Hyperscale (Citus).  Compute resources worden geleverd als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. De opslaggrootte voor inprovisioning verwijst naar de capaciteit die beschikbaar is voor de coördinator en werknemersknooppunten in uw Hyperscale-servergroep (Citus). De opslag omvat databasebestanden, tijdelijke bestanden, transactielogboeken en de Postgres-serverlogboeken. De totale hoeveelheid opslagruimte die u indient, definieert ook de I/O-capaciteit die beschikbaar is voor elke werknemer en coördinatorknooppunt.
 
|                       | Worker node           | Coördinator knooppunt      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Geheugen per vCore, GiB | 8                     | 4                     |
| Opslaggrootte, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Opslagtype          | Algemeen doel (SSD) | Algemeen doel (SSD) |
| IOPS                  | Maximaal 3 IOPS/GiB      | Maximaal 3 IOPS/GiB      |


## <a name="regions"></a>Regio's
Citus-servergroepen (Hyperscale) zijn beschikbaar in de volgende Azure-regio's:

* Noord- en Zuid-Amerika: 
    * Canada - midden
    * VS - centraal
    * VS - oost
    * VS - oost 2
    * VS - noord-centraal
    * VS - west 2
* Azië-Pacific:
    * Australië - oost
    * Japan - oost
    * Korea - centraal
    * Azië - zuidoost
* Europa:
    * Europa - noord
    * Verenigd Koninkrijk Zuid
    * Europa -west

Sommige van deze regio's worden mogelijk niet in eerste instantie geactiveerd op alle Azure-abonnementen. Als u een regio uit de bovenstaande lijst wilt gebruiken en het niet in uw abonnement wilt zien, of als u een regio wilt gebruiken die niet in deze lijst staat, opent u een [ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="pricing"></a>Prijzen
Zie de pagina serviceprijzen voor de meest actuele [prijsinformatie.](https://azure.microsoft.com/pricing/details/postgresql/)
Als u de gewenste kosten wilt zien voor de gewenste configuratie, [worden](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) de maandelijkse kosten op het tabblad Configureren weergegeven op basis **van** de opties die u selecteert. Als u geen Azure-abonnement hebt, u de azure-prijscalculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website van de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) de optie **Items toevoegen,** de categorie **Databases** uitbreiden en **Azure Database voor PostgreSQL – Hyperscale (Citus)** kiezen om de opties aan te passen.
 
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [maken van een Servergroep Hyperscale (Citus) in de portal.](quickstart-create-hyperscale-portal.md)
