---
title: Configuratieopties – Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Opties voor een Citus-servergroep (Hyperscale), inclusief node compute, opslag en regio's.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804585"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database voor PostgreSQL – Configuratieopties voor Hyperscale (Citus)

## <a name="compute-and-storage"></a>Compute en opslag
 
U de reken- en opslaginstellingen onafhankelijk selecteren voor werknemersknooppunten en het coördinatorknooppunt in een Servergroep Hyperscale (Citus).  Compute resources worden geleverd als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. De opslaggrootte voor inprovisioning verwijst naar de capaciteit die beschikbaar is voor de coördinator en werknemersknooppunten in uw Hyperscale-servergroep (Citus). De opslag omvat databasebestanden, tijdelijke bestanden, transactielogboeken en de Postgres-serverlogboeken.
 
|                       | Worker node           | Coördinator knooppunt      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Geheugen per vCore, GiB | 8                     | 4                     |
| Opslaggrootte, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Opslagtype          | Algemeen doel (SSD) | Algemeen doel (SSD) |
| IOPS                  | Maximaal 3 IOPS/GiB      | Maximaal 3 IOPS/GiB      |

De totale hoeveelheid RAM in één Knooppunt van Hyperscale (Citus) is gebaseerd op het geselecteerde aantal vCores.

| vCores | Eén werkknooppunt, GiB RAM | Coördinator knooppunt, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

De totale hoeveelheid opslagruimte die u indient, definieert ook de I/O-capaciteit die beschikbaar is voor elke werknemer en coördinatorknooppunt.

| Opslaggrootte, TiB | Maximale IOPS |
|-------------------|--------------|
| 0.5               | 1536        |
| 1                 | 3072        |
| 2                 | 6,148        |

Voor het gehele Cluster Hyperscale (Citus) werkt het geaggregeerde IOPS uit tot de volgende waarden:

| Werkknooppunten | 0,5 TiB, totaal IOPS | 1 TiB, totaal IOPS | 2 TiB, totaal IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3072               | 6144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

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
