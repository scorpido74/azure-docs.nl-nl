---
title: Planning voor Azure-onderhoudsgebeurtenissen
description: Meer informatie over het voorbereiden van geplande onderhoudsgebeurtenissen in uw Azure SQL Database.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821306"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planning voor Azure-onderhoudsgebeurtenissen in Azure SQL Database

Meer informatie over het voorbereiden van geplande onderhoudsgebeurtenissen in uw Azure SQL-database.

## <a name="what-is-a-planned-maintenance-event"></a>Wat is een geplande onderhoudsgebeurtenis

Voor elke database behoudt Azure SQL DB een quorum van databasereplica's waarbij één replica de primaire is. Een primaire replica moet te allen tijde online onderhoud zijn en ten minste één secundaire replica moet gezond zijn. Tijdens gepland onderhoud gaan leden van het databasequorum één voor één offline, met de bedoeling dat er één primaire replica en ten minste één secundaire replica online is om geen downtime van de client te garanderen. Wanneer de primaire replica offline moet worden gehaald, treedt een herconfiguratie/failoverproces op waarbij één secundaire replica de nieuwe primaire replica wordt.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Wat te verwachten tijdens een gepland onderhoudsevenement

Herconfiguraties/failovers over het algemeen voltooid binnen 30 seconden - het gemiddelde is 8 seconden. Als uw toepassing al is verbonden, moet u opnieuw verbinding maken met de nieuwe primaire replica van uw database met een gezonde kopie. Als een nieuwe verbinding wordt geprobeerd terwijl de database een herconfiguratie ondergaat voordat de nieuwe primaire replica online is, wordt fout 40613 (Database niet beschikbaar): 'Database {databasename}' op server {servername}' is momenteel niet beschikbaar. Probeer de verbinding later opnieuw". Als uw database een langlopende query heeft, wordt deze query onderbroken tijdens een nieuwe configuratie en moet deze opnieuw worden gestart.

## <a name="retry-logic"></a>Logica opnieuw proberen

Elke clientproductietoepassing die verbinding maakt met een clouddatabaseservice, moet een robuuste [logica voor het opnieuw proberen van](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)verbindingen implementeren. Dit zal helpen deze situaties te beperken en moet over het algemeen de fouten transparant maken voor de eindgebruiker.

## <a name="frequency"></a>Frequency

Gemiddeld vinden er 1,7 geplande onderhoudsgebeurtenissen per maand plaats.

## <a name="resource-health"></a>Status van resources

Als uw SQL-database inlogfouten wordt ervaren, controleert u het venster [Resourcestatus](../service-health/resource-health-overview.md#get-started) in de [Azure-portal](https://portal.azure.com) op de huidige status. De sectie Statusgeschiedenis bevat de reden voor downtime voor elke gebeurtenis (indien beschikbaar).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Resource Health](sql-database-resource-health.md) voor SQL Database
- Zie [Logica opnieuw proberen voor tijdelijke fouten](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) voor meer informatie over logica opnieuw proberen
