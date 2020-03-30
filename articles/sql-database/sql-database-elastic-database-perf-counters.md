---
title: Prestatiemeteritems om shardmapbeheer bij te houden
description: ShardMapManager-klasse- en gegevensafhankelijke routeringsprestatiemeteritems
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823902"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Prestatiemeteritems maken om de prestaties van shardmapbeheer bij te houden

Prestatiemeteritems worden gebruikt om de prestaties van [gegevensafhankelijke routeringsbewerkingen](sql-database-elastic-scale-data-dependent-routing.md) bij te houden. Deze tellers zijn toegankelijk in de Prestatiemonitor, onder de categorie "Elastic Database: Shard Management".

U de prestaties van een [shardkaartbeheer](sql-database-elastic-scale-shard-map-management.md)vastleggen, vooral bij het gebruik van [gegevensafhankelijke routering.](sql-database-elastic-scale-data-dependent-routing.md) Tellers worden gemaakt met methoden van de klasse Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Voor de nieuwste versie:** Ga naar [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zie ook [Een app upgraden om de nieuwste elastische databaseclientbibliotheek te gebruiken.](sql-database-elastic-scale-upgrade-client-library.md)

## <a name="prerequisites"></a>Vereisten

* Als u de prestatiecategorie en -tellers wilt maken, moet de gebruiker deel uitmaken van de lokale **groep Administrators** op de machine die de toepassing host.  
* Als u een prestatiemeterinstantie wilt maken en de tellers wilt bijwerken, moet de gebruiker lid zijn van de groep **Administrators** of **Prestatiemonitorgebruikers.**

## <a name="create-performance-category-and-counters"></a>Prestatiecategorie en -tellers maken

Als u de tellers wilt maken, roept u de methode CreatePerformanceCategoryAndCounters van de [klasse ShardMapManagementFactory aan.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory) Alleen een beheerder kan de methode uitvoeren:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

U [dit PowerShell-script](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) ook gebruiken om de methode uit te voeren.
De methode maakt de volgende prestatiemeteritems:  

* **Toewijzingen in cache:** aantal toewijzingen die in de cache zijn opgeslagen voor de shardkaart.
* **DDR-bewerkingen/sec:** Snelheid van gegevensafhankelijke routeringsbewerkingen voor de shardkaart. Deze teller wordt bijgewerkt wanneer een aanroep naar [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulteert in een succesvolle verbinding met de doelshard.
* **Zoekcachehits per seconde toewijzen:** Snelheid van succesvolle cachezoekbewerkingen voor toewijzingen in de shardkaart.
* **Zoekcache-fouten per seconde toewijzen:** snelheid van mislukte cachezoekbewerkingen voor toewijzingen in de shardkaart.
* **Toewijzingen toegevoegd of bijgewerkt in cache/sec:** Snelheid waarmee toewijzingen worden toegevoegd of bijgewerkt in de cache voor de shardkaart.
* **Toewijzingen verwijderd uit cache/sec**: Snelheid waarmee toewijzingen worden verwijderd uit de cache voor de shardkaart.

Prestatiemeteritems worden gemaakt voor elke shardkaart in de cache per proces.  

## <a name="notes"></a>Opmerkingen

De volgende gebeurtenissen leiden tot het maken van de prestatiemeteritems:  

* Initialisatie van de [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) met [gretigladen,](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)als de ShardMapManager shardkaarten bevat. Deze omvatten de [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) en de [TryGetSqlShardMapManager-methoden.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager)
* Geslaagde lookup van een shardkaart (met [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) of [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Succesvolle creatie van shardmap met CreateShardMap().

De prestatiemeteritems worden bijgewerkt door alle cachebewerkingen die worden uitgevoerd op de shardkaart en toewijzingen. Succesvolle verwijdering van de shardkaart met DeleteShardMap() resulteert in verwijdering van de instantie prestatiemeter.  

## <a name="best-practices"></a>Aanbevolen procedures

* Het maken van de prestatiecategorie en -tellers mag slechts één keer worden uitgevoerd voordat het object ShardMapManager wordt gemaakt. Elke uitvoering van de opdracht CreatePerformanceCategoryAndCounters() wist de vorige tellers (verlies van gegevens gerapporteerd door alle instanties) en maakt nieuwe.  
* Prestatiemeterexemplaren worden per proces gemaakt. Elke toepassing crash of verwijdering van een shard kaart uit de cache zal resulteren in het verwijderen van de prestaties tellers exemplaren.  

### <a name="see-also"></a>Zie ook

[Overzicht over functies voor Elastic Database](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
