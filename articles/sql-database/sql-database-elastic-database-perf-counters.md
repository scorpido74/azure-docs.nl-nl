---
title: Prestatie meter items maken om de prestaties van Shard-toewijzings beheer bij te houden
description: Prestatie meter items ShardMapManager-klasse en gegevens afhankelijke route ring
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568671"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Prestatie meter items maken om de prestaties van Shard-toewijzings beheer bij te houden

Prestatie meter items worden gebruikt voor het bijhouden van de prestaties van [gegevens afhankelijke route](sql-database-elastic-scale-data-dependent-routing.md) bewerkingen. Deze prestatie meter items zijn toegankelijk in de prestatie meter, onder de Elastic Database: Shard beheer categorie.

U kunt de prestaties van een [Shard-kaart beheer](sql-database-elastic-scale-shard-map-management.md)vastleggen, met name bij het gebruik van [gegevens afhankelijke route ring](sql-database-elastic-scale-data-dependent-routing.md). Tellers worden gemaakt met methoden van de klasse micro soft. Azure. SqlDatabase. ElasticScale. client.  


**Voor de meest recente versie:** Ga naar [micro soft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zie ook [een app bijwerken voor het gebruik van de meest recente Elastic data base-client bibliotheek](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Vereisten

* De gebruiker moet lid zijn van de lokale groep Administrators op de computer die als host voor de toepassing fungeert om de prestatie categorie en prestatie meter items te maken.  
* Als u een exemplaar van een prestatie meter item wilt maken en de tellers wilt bijwerken, moet de gebruiker lid zijn van de groep Administrators of **prestatie meter gebruikers** .

## <a name="create-performance-category-and-counters"></a>Prestatie categorie en tellers maken

Als u de items wilt maken, roept u de methode CreatePerformanceCategoryAndCounters van de [klasse ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)aan. Alleen een beheerder kan de volgende methode uitvoeren:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

U kunt [Dit](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) Power shell-script ook gebruiken om de-methode uit te voeren.
Met de-methode worden de volgende prestatie meter items gemaakt:  

* **Toewijzingen in cache**: Het aantal toewijzingen in de cache voor de Shard-kaart.
* **DDR-bewerkingen per seconde**: Frequentie van gegevens afhankelijke routerings bewerkingen voor de Shard-kaart. Dit item wordt bijgewerkt wanneer een aanroep van [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulteert in een geslaagde verbinding met de doel-Shard.
* **Treffers in cache voor toewijzingen per seconde**: Verhouding van geslaagde cache opzoek bewerkingen voor toewijzingen in de Shard-toewijzing.
* **Missers in de cache voor het opzoeken van toewijzingen per seconde**: Verhouding van mislukte opzoek bewerkingen in de cache voor toewijzingen in de Shard-toewijzing.
* **Toewijzingen toegevoegd of bijgewerkt in cache per seconde**: De snelheid waarmee toewijzingen worden toegevoegd of bijgewerkt in de cache voor de Shard-toewijzing.
* **Toewijzingen verwijderd uit cache/SEC**: De snelheid waarmee toewijzingen worden verwijderd uit de cache voor de Shard-kaart.

Prestatie meter items worden gemaakt voor elke Shard-kaart in de cache per proces.  

## <a name="notes"></a>Opmerkingen

Met de volgende gebeurtenissen wordt het maken van de prestatie meter items geactiveerd:  

* Initialisatie van de [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) met behulp van een te [laden](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), als de ShardMapManager een Shard-toewijzing bevat. Dit zijn onder andere de methoden [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) en [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Het opzoeken van een Shard-kaart is geslaagd (met behulp van [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) of [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Het maken van een Shard-toewijzing met CreateShardMap () is voltooid.

De prestatie meter items worden bijgewerkt door alle cache bewerkingen die worden uitgevoerd op de Shard-kaart en toewijzingen. Het verwijderen van de Shard-toewijzing met DeleteShardMap () resulteert in het verwijderen van het prestatie meter item-exemplaar.  

## <a name="best-practices"></a>Aanbevolen procedures

* Het maken van de prestatie categorie en prestatie meter items moet slechts één keer worden uitgevoerd voordat het ShardMapManager-object wordt gemaakt. Elke uitvoering van de opdracht CreatePerformanceCategoryAndCounters () wist de vorige prestatie meter items (gegevens verlies van alle instanties) en maakt nieuwe.  
* Instanties voor prestatie meter items worden per proces gemaakt. Het vastlopen van toepassingen of het verwijderen van een Shard-kaart uit de cache leidt ertoe dat de instanties van prestatie meter items worden verwijderd.  

### <a name="see-also"></a>Zie ook

[Overzicht over functies voor Elastic Database](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
