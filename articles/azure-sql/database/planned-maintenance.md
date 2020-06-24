---
title: Planning voor Azure-onderhouds gebeurtenissen
description: Meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen in Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 524eaa0cfd2137f2b88102bfa5c514d5248cc743
ms.sourcegitcommit: a8928136b49362448e992a297db1072ee322b7fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84718619"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure-onderhouds gebeurtenissen plannen in Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen in uw data base in Azure SQL Database en Azure SQL Managed instance.

## <a name="what-is-a-planned-maintenance-event"></a>Wat is een gepland onderhouds gebeurtenis?

Voor elke Data Base bewaart Azure SQL Database en Azure SQL Managed instance een quorum van database replica's waarbij één replica het primaire is. Op elk moment moet een primaire replica online onderhoud zijn en moet ten minste één secundaire replica in orde zijn. Tijdens gepland onderhoud gaan leden van het database quorum één keer offline, met de bedoeling dat er één primaire replica is en ten minste één secundaire replica online om ervoor te zorgen dat er geen downtime van de client is. Wanneer de primaire replica offline moet worden gezet, treedt er een herconfiguratie/failover-proces op waarin de ene secundaire replica de nieuwe primair wordt.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Wat u kunt verwachten tijdens een geplande onderhouds gebeurtenis

Opnieuw configureren/failovers worden over het algemeen binnen 30 seconden voltooid. Het gemiddelde is 8 seconden. Als er al een verbinding is gemaakt, moet uw toepassing opnieuw verbinding maken met de goede kopie van de nieuwe primaire replica van uw data base. Als er een nieuwe verbinding tot stand wordt gebracht terwijl de Data Base een herconfiguratie ondergaat voordat de nieuwe primaire replica online is, krijgt u de fout 40613 (Data Base niet beschikbaar): ' data base {DATABASENAME} ' op server ' {servername} ' is momenteel niet beschikbaar. Probeer later opnieuw verbinding te maken.' Als uw data base een langlopende query heeft, wordt deze query onderbroken tijdens een herconfiguratie en moet deze opnieuw worden gestart.

## <a name="retry-logic"></a>Logica voor opnieuw proberen

Bij elke client productie toepassing die verbinding maakt met een Cloud database service, moet een robuuste logica voor verbinding [opnieuw](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)worden geïmplementeerd. Dit helpt u deze situaties te verhelpen en de fouten doorgaans transparant te maken voor de eind gebruiker.

## <a name="frequency"></a>Frequentie

Gemiddeld worden de 1,7 geplande onderhouds gebeurtenissen elke maand uitgevoerd.

## <a name="resource-health"></a>Status van resources

Als uw data base aanmeldings fouten ondervindt, controleert u het [resource Health](../../service-health/resource-health-overview.md#get-started) venster in het [Azure Portal](https://portal.azure.com) voor de huidige status. De sectie status geschiedenis bevat de downtime-reden voor elke gebeurtenis (indien beschikbaar).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [resource Health](resource-health-to-troubleshoot-connectivity.md) voor Azure SQL database en Azure SQL Managed instance.
- Zie voor meer informatie over logica voor opnieuw proberen [logica voor tijdelijke fouten](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
