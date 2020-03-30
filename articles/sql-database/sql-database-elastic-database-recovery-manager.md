---
title: Recovery Manager om problemen met de shardkaart op te lossen
description: De klasse RecoveryManager gebruiken om problemen met shardkaarten op te lossen
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823870"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Problemen met shard-toewijzingen oplossen met de RecoveryManager-klasse

De klasse [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) biedt ADO.NET toepassingen de mogelijkheid om eventuele inconsistenties tussen de globale shardkaart (GSM) en de lokale shardkaart (LSM) in een verharde databaseomgeving eenvoudig te detecteren en te corrigeren.

De GSM en LSM volgen de mapping van elke database in een geshard omgeving. Af en toe treedt er een breuk op tussen de GSM en de LSM. Gebruik in dat geval de klasse RecoveryManager om de breuk te detecteren en te herstellen.

De klasse RecoveryManager maakt deel uit van de [clientbibliotheek Elastic Database](sql-database-elastic-database-client-library.md).

![Shardkaart][1]

Zie [Woordenlijst voor elastic database-gereedschappen](sql-database-elastic-scale-glossary.md)voor termdefinities . Zie **Shard mapmanagement** voor meer informatie over het beheer van [Shard map management](sql-database-elastic-scale-shard-map-management.md)gegevens in een verharde oplossing.

## <a name="why-use-the-recovery-manager"></a>Waarom de herstelmanager gebruiken

In een geshard databaseomgeving is er één tenant per database en veel databases per server. Er kunnen ook veel servers in de omgeving zijn. Elke database wordt toegewezen in de shardkaart, zodat oproepen kunnen worden doorgestuurd naar de juiste server en database. Databases worden bijgehouden op basis van een **shardingsleutel**en aan elke shard wordt een **reeks belangrijke waarden**toegewezen . Een shardingsleutel kan bijvoorbeeld de namen van de klant vertegenwoordigen van 'D' naar 'F'. Het in kaart brengen van alle scherven (ook bekend als databases) en hun kaartbereiken zijn opgenomen in de **global shard map (GSM).** Elke database bevat ook een kaart van de bereiken op de scherf die bekend staat als de **lokale shardkaart (LSM).** Wanneer een app verbinding maakt met een scherf, wordt de toewijzing in de cache opgeslagen met de app voor snel ophalen. De LSM wordt gebruikt om gegevens in de cache te valideren.

De GSM en LSM kunnen om de volgende redenen niet meer synchroon lopen:

1. Het verwijderen van een scherf waarvan wordt aangenomen dat het bereik niet meer in gebruik is, of het hernoemen van een scherf. Het verwijderen van een scherf resulteert in een **verweesde scherf toewijzing**. Op dezelfde manier kan een hernoemde database leiden tot een verweesde shardtoewijzing. Afhankelijk van de bedoeling van de wijziging moet de scherf mogelijk worden verwijderd of moet de shardlocatie worden bijgewerkt. Zie Een verwijderde database [herstellen](sql-database-recovery-using-backups.md)als u een verwijderde database wilt herstellen.
2. Er vindt een gebeurtenis voor geo-failover plaats. Om door te gaan, moet men de servernaam en de databasenaam van shardmapbeheer in de toepassing bijwerken en vervolgens de details van de shardtoewijzing voor alle scherven in een shardkaart bijwerken. Als er een geo-failover is, moet een dergelijke herstellogica worden geautomatiseerd binnen de failover-workflow. Het automatiseren van herstelacties maakt een wrijvingsloze beheerbaarheid mogelijk voor geo-enabled databases en voorkomt handmatige menselijke acties. Zie [Bedrijfscontinuïteit](sql-database-business-continuity.md) en [herstel na noodgevallen](sql-database-disaster-recovery.md)voor meer informatie over opties om een database te herstellen als er een datacenterstoring is.
3. Een shard of de ShardMapManager-database wordt hersteld naar een eerdere inlooptijd. Zie Herstel met back-ups voor meer informatie over point-in-time recovery met [behulp van back-ups.](sql-database-recovery-using-backups.md)

Zie het volgende voor meer informatie over azure SQL Database Elastic Database-hulpprogramma's voor elastic database, geo-replicatie en herstel:

* [Overzicht: Cloud business continuity en database disaster recovery met SQL Database](sql-database-business-continuity.md)
* [Aan de slag met elastische databasetools](sql-database-elastic-scale-get-started.md)  
* [ShardMapbeheer](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>RecoveryManager ophalen uit een ShardMapManager

De eerste stap is het maken van een Instantie RecoveryManager. De [methode GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) retourneert de herstelmanager voor het huidige [exemplaar ShardMapManager.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Als u eventuele inconsistenties in de shardkaart wilt aanpakken, moet u eerst de RecoveryManager ophalen voor de specifieke shardkaart.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

In dit voorbeeld wordt de RecoveryManager geïnitialiseerd vanuit de ShardMapManager. De ShardMapManager met een ShardMap is ook al geïnitialiseerd.

Aangezien deze toepassingscode de shardkaart zelf manipuleert, moeten de referenties die in de fabrieksmethode worden gebruikt (in het voorgaande voorbeeld smmConnectionString) referenties zijn met lees-schrijfmachtigingen op de GSM-database waarnaar wordt verwezen door de verbinding Tekenreeks. Deze referenties verschillen meestal van referenties die worden gebruikt om verbindingen te openen voor gegevensafhankelijke routering. Zie [Referenties gebruiken in de elastische databaseclient](sql-database-elastic-scale-manage-credentials.md)voor meer informatie.

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Een scherf van de shardmap verwijderen nadat een scherf is verwijderd

De [methode Detachshard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) ontkoppelt de gegeven scherf van de shardkaart en verwijdert toewijzingen die aan de shard zijn gekoppeld.  

* De locatieparameter is de shardlocatie, met name de naam van de server en de naam van de database, van de ontkoppeling van de shard.
* De parameter shardMapName is de naam van de shardkaart. Dit is alleen vereist wanneer meerdere shardkaarten worden beheerd door dezelfde shardmapmanager. Optioneel.

> [!IMPORTANT]
> Gebruik deze techniek alleen als u zeker weet dat het bereik voor de bijgewerkte toewijzing leeg is. De bovenstaande methoden controleren geen gegevens voor het bereik dat wordt verplaatst, dus het is het beste om controles in uw code op te nemen.

In dit voorbeeld worden scherven van de shardkaart verwijderd.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

De shardkaart geeft de shardlocatie in de GSM weer voordat de scherf wordt verwijderd. Omdat de scherf is verwijderd, wordt aangenomen dat dit opzettelijk was en het shardingssleutelbereik niet meer in gebruik is. Zo niet, dan u point-in time restore uitvoeren. om de scherf terug te krijgen van een eerdere point-in-time. (Controleer in dat geval de volgende sectie om shardinconsistenties op te sporen.) Zie Punt [in tijdherstel](sql-database-recovery-using-backups.md)om te herstellen.

Aangezien wordt aangenomen dat de verwijdering van de database opzettelijk was, is de uiteindelijke administratieve opruimactie het verwijderen van de vermelding van de shard in de shardmapbeheerder. Dit voorkomt dat de toepassing per ongeluk informatie schrijft naar een bereik dat niet wordt verwacht.

## <a name="to-detect-mapping-differences"></a>Kaartverschillen detecteren

De [methode DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) selecteert en retourneert een van de shardkaarten (lokaal of globaal) als de bron van de waarheid en brengt toewijzingen op beide shardkaarten (GSM en LSM) met elkaar in overeenstemming.

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* De *locatie* geeft de naam van de server en de naam van de database op.
* De parameter *shardMapName* is de naam van de shardkaart. Dit is alleen vereist als meerdere shardkaarten worden beheerd door dezelfde shardmapmanager. Optioneel.

## <a name="to-resolve-mapping-differences"></a>Kaartverschillen oplossen

De [methode ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) selecteert een van de shardkaarten (lokaal of globaal) als de bron van de waarheid en brengt toewijzingen op beide shardkaarten (GSM en LSM) met elkaar in overeenstemming.

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* De parameter *RecoveryToken* somt de verschillen in de toewijzingen tussen de GSM en de LSM op voor de specifieke shard.
* De [opgesomd mappingdifferenceresolution](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) wordt gebruikt om de methode aan te geven voor het oplossen van het verschil tussen de shardtoewijzingen.
* **MappingDifferenceResolution.KeepShardMapping** wordt aanbevolen dat wanneer de LSM de nauwkeurige toewijzing bevat en daarom de toewijzing in de shard moet worden gebruikt. Dit is meestal het geval als er een failover is: de shard bevindt zich nu op een nieuwe server. Aangezien de scherf eerst van de GSM moet worden verwijderd (met behulp van de Methode RecoveryManager.DetachShard), bestaat er geen toewijzing meer op de GSM. Daarom moet de LSM worden gebruikt om de shardmapping opnieuw vast te stellen.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Bevestig een scherf aan de ShardMap nadat een scherf is hersteld

De [AttachShard methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) hecht de gegeven scherf aan de shardkaart. Het detecteert vervolgens eventuele shard kaart inconsistenties en werkt de mappings aan de scherf op het punt van de scherf restauratie wedstrijd. Er wordt aangenomen dat de database ook wordt hernoemd naar de oorspronkelijke databasenaam (voordat de shard werd hersteld), omdat de point-in time restauratie standaard wordt toegevoegd aan een nieuwe database die is toegevoegd aan de tijdstempel.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* De *locatieparameter* is de naam van de server en de naam van de database van de shard die wordt gekoppeld.
* De parameter *shardMapName* is de naam van de shardkaart. Dit is alleen vereist wanneer meerdere shardkaarten worden beheerd door dezelfde shardmapmanager. Optioneel.

In dit voorbeeld wordt een scherf toegevoegd aan de shardkaart die onlangs is hersteld vanaf een eerdere inlooptijd. Aangezien de scherf (namelijk de mapping voor de scherf in de LSM) is hersteld, is het mogelijk niet in overeenstemming met de shard vermelding in de GSM. Buiten deze voorbeeldcode werd de scherf hersteld en omgedoopt tot de oorspronkelijke naam van de database. Sinds het is hersteld, wordt aangenomen dat de toewijzing in de LSM de vertrouwde toewijzing is.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Shardlocaties bijwerken na een geo-failover (restore) van de scherven

Als er een geo-failover is, wordt de secundaire database toegankelijk gemaakt voor schrijven en wordt de nieuwe primaire database. De naam van de server en mogelijk de database (afhankelijk van uw configuratie) kunnen afwijken van de oorspronkelijke primaire. Daarom moeten de toewijzingsingangen voor de shard in GSM en LSM worden bevestigd. Als de database wordt hersteld naar een andere naam of locatie of naar een eerder tijdstip, kan dit inconsistenties veroorzaken in de shardkaarten. De Shard Map Manager verzorgt de distributie van open verbindingen naar de juiste database. De distributie is gebaseerd op de gegevens in de shardkaart en de waarde van de shardingsleutel die het doel is van de aanvraag voor toepassingen. Na een geo-failover moet deze informatie worden bijgewerkt met de nauwkeurige servernaam, databasenaam en shardtoewijzing van de herstelde database.

## <a name="best-practices"></a>Aanbevolen procedures

Geo-failover en herstel zijn bewerkingen die doorgaans worden beheerd door een cloudbeheerder van de toepassing die opzettelijk gebruik maakt van een van de bedrijfscontinuïteitsfuncties van Azure SQL-databases. Bedrijfscontinuïteitsplanning vereist processen, procedures en maatregelen om ervoor te zorgen dat de bedrijfsactiviteiten zonder onderbreking kunnen worden voortgezet. De methoden die beschikbaar zijn in het kader van de klasse RecoveryManager moeten binnen deze werkstroom worden gebruikt om ervoor te zorgen dat de GSM en LSM up-to-date worden gehouden op basis van de genomen herstelmaatregelen. Er zijn vijf basisstappen om ervoor te zorgen dat de GSM en LSM de juiste informatie weergeven na een failovergebeurtenis. De toepassingscode voor het uitvoeren van deze stappen kan worden geïntegreerd in bestaande hulpprogramma's en workflow.

1. Haal de RecoveryManager op uit de ShardMapManager.
2. Maak de oude scherf los van de shardkaart.
3. Bevestig de nieuwe scherf aan de shardkaart, inclusief de nieuwe shardlocatie.
4. Detecteer inconsistenties in de mapping tussen de GSM en LSM.
5. Los verschillen op tussen de GSM en de LSM, vertrouwen op de LSM.

In dit voorbeeld worden de volgende stappen uitgevoerd:

1. Hiermee verwijdert u shards uit de shardkaart die shardlocaties weergeven vóór de failovergebeurtenis.
2. Hiermee worden shards aan de shardkaart bevestigd die de nieuwe shardlocaties weergeeft (de parameter "Configuration.SecondaryServer" is de nieuwe servernaam, maar dezelfde databasenaam).
3. Hiermee haalt u de hersteltokens op door kaartverschillen tussen de GSM en de LSM voor elke shard te detecteren.
4. Hiermee worden de inconsistenties opgelost door de toewijzing van de LSM van elke shard te vertrouwen.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
