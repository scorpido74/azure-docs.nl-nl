---
title: Planning voor Azure-onderhouds gebeurtenissen
description: Meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen op uw Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821306"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planning voor Azure-onderhoudsgebeurtenissen in Azure SQL Database

Meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen op uw Azure SQL database.

## <a name="what-is-a-planned-maintenance-event"></a>Wat is een gepland onderhouds gebeurtenis

Voor elke Data Base houdt Azure SQL DB een quorum bij van database replica's waarbij één replica de primaire is. Op het moment dat een primaire replica online onderhoud moet zijn en moet ten minste één secundaire replica in orde zijn. Tijdens gepland onderhoud gaan leden van het database quorum één keer offline, met de bedoeling dat er één primaire replica is en ten minste één secundaire replica online om ervoor te zorgen dat er geen downtime van de client is. Wanneer de primaire replica offline moet worden gezet, treedt er een herconfiguratie/failover-proces op waarin de ene secundaire replica de nieuwe primair wordt.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Wat u kunt verwachten tijdens een geplande onderhouds gebeurtenis

Opnieuw configureren/failovers worden over het algemeen binnen 30 seconden voltooid – het gemiddelde is 8 seconden. Als er al een verbinding is gemaakt, moet uw toepassing opnieuw verbinding maken met de goede kopie van de nieuwe primaire replica van uw data base. Als er een nieuwe verbinding tot stand wordt gebracht terwijl de Data Base een herconfiguratie ondergaat voordat de nieuwe primaire replica online is, krijgt u de fout 40613 (Data Base niet beschikbaar): ' data base {DATABASENAME} ' op server ' {servername} ' is momenteel niet beschikbaar. Probeer de verbinding later opnieuw. Als uw data base een langlopende query heeft, wordt deze query onderbroken tijdens een herconfiguratie en moet deze opnieuw worden gestart.

## <a name="retry-logic"></a>Logica voor opnieuw proberen

Bij elke client productie toepassing die verbinding maakt met een Cloud database service, moet een robuuste logica voor verbinding [opnieuw](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)worden geïmplementeerd. Dit helpt u deze situaties te verhelpen en de fouten doorgaans transparant te maken voor de eind gebruiker.

## <a name="frequency"></a>Frequency

Gemiddeld worden de 1,7 geplande onderhouds gebeurtenissen elke maand uitgevoerd.

## <a name="resource-health"></a>Status van resources

Als uw SQL database aanmeldings fouten ondervindt, controleert u het [resource Health](../service-health/resource-health-overview.md#get-started) venster in de [Azure Portal](https://portal.azure.com) voor de huidige status. De sectie status geschiedenis bevat de downtime-reden voor elke gebeurtenis (indien beschikbaar).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [resource Health](sql-database-resource-health.md) voor SQL database
- Zie voor meer informatie over logica voor opnieuw proberen [logica voor tijdelijke fouten](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
