---
title: Upgraden naar de nieuwste elastische databaseclientbibliotheek
description: Gebruik Nuget om de elastische databaseclientbibliotheek te upgraden.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: a1ab684f13c56698d4359f2bf74826f3dd696c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823513"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Een app upgraden om de nieuwste elastische databaseclientbibliotheek te gebruiken

Nieuwe versies van de [Elastic Database-clientbibliotheek](sql-database-elastic-database-client-library.md) zijn beschikbaar via NuGet en de NuGet Package Manager-interface in Visual Studio. Upgrades bevatten bugfixes en ondersteuning voor nieuwe mogelijkheden van de clientbibliotheek.

**Voor de nieuwste versie:** Ga naar [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Herbouw uw toepassing met de nieuwe bibliotheek en wijzig uw bestaande shardmapbeheermetgegevens die zijn opgeslagen in uw Azure SQL-databases om nieuwe functies te ondersteunen.

Als u deze stappen uitvoert, zorgt u ervoor dat oude versies van de clientbibliotheek niet meer aanwezig zijn in uw omgeving wanneer metagegevensobjecten worden bijgewerkt, wat betekent dat metagegevens van de oude versie niet worden gemaakt na een upgrade.

## <a name="upgrade-steps"></a>Upgradestappen

**1. Upgrade uw toepassingen.** Download en verwijs in Visual Studio de nieuwste versie van de clientbibliotheek naar al uw ontwikkelingsprojecten die de bibliotheek gebruiken; vervolgens opnieuw opbouwen en implementeren.

* Selecteer in uw Visual Studio-oplossing **Tools** --> **NuGet Package Manager** -->  **Beheer NuGet-pakketten voor oplossing.**
* (Visual Studio 2013) Selecteer in het linkerdeelvenster **Updates**en selecteer vervolgens de knop **Bijwerken** in de azure **SQL-database-beveiligingsbibliotheek** voor elastische waarden in het pakket dat in het venster wordt weergegeven.
* (Visual Studio 2015) Stel het vak Filter in **op Upgrade beschikbaar**. Selecteer het pakket dat u wilt bijwerken en klik op de knop **Bijwerken.**
* (Visual Studio 2017) Selecteer Boven aan het dialoogvenster de optie **Updates**. Selecteer het pakket dat u wilt bijwerken en klik op de knop **Bijwerken.**
* Bouwen en implementeren.

**2. Upgrade uw scripts.** Als u **PowerShell-scripts** gebruikt om shards te beheren, [downloadt u de nieuwe bibliotheekversie](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) en kopieert u deze naar de map van waaruit u scripts uitvoert.

**3. Upgrade uw split-merge-service.** Als u het gereedschap elastic database split-merge gebruikt om geshard gegevens te reorganiseren, [downloadt en implementeert u de nieuwste versie van het hulpprogramma.](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/) Gedetailleerde upgradestappen voor de Service zijn [hier](sql-database-elastic-scale-overview-split-and-merge.md)te vinden.

**4. Upgrade uw Shard Map Manager databases.** Upgrade de metagegevens die uw Shard Maps ondersteunen in Azure SQL Database.  Er zijn twee manieren waarop u dit bereiken, met behulp van PowerShell of C#. Beide opties worden hieronder weergegeven.

***Optie 1: Metagegevens upgraden met PowerShell***

1. Download het nieuwste hulpprogramma voor de opdrachtregel voor NuGet vanaf [hier](https://nuget.org/nuget.exe) en sla op in een map.
2. Open een opdrachtprompt, navigeer naar dezelfde map en geef de opdracht uit:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navigeer naar de submap met de nieuwe client DLL-versie die u zojuist hebt gedownload, bijvoorbeeld:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Download het upgradescript voor elastische databaseclient uit het [Scriptcenter](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)en sla het op in dezelfde map met de DLL.
5. Voer vanuit die map 'PowerShell .\upgrade.ps1' uit vanaf de opdrachtprompt en volg de prompts.

***Optie 2: Metagegevens upgraden met C #***

U ook een Visual Studio-toepassing maken die uw ShardMapManager opent, alle shards doorvoert en de metagegevensupgrade uitvoert door de methoden [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) en [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) aan te roepen zoals in dit voorbeeld:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Deze technieken voor metadata upgrades kunnen meerdere keren zonder schade worden toegepast. Als een oudere clientversie bijvoorbeeld per ongeluk een shard maakt nadat u deze al hebt bijgewerkt, u de upgrade opnieuw uitvoeren op alle shards om ervoor te zorgen dat de nieuwste versie metagegevens overal in uw infrastructuur aanwezig is.

**Let op:**  Nieuwe versies van de tot op heden gepubliceerde clientbibliotheek blijven werken met eerdere versies van de shardmapbeheermetagegevens op Azure SQL DB en vice versa.   Om echter te profiteren van enkele van de nieuwe functies in de nieuwste client, moeten metadata worden bijgewerkt.   Houd er rekening mee dat metagegevens-upgrades geen invloed hebben op gebruikersgegevens of toepassingsspecifieke gegevens, alleen op objecten die zijn gemaakt en gebruikt door Shard Map Manager.  En toepassingen blijven werken via de upgrade sequentie hierboven beschreven.

## <a name="elastic-database-client-version-history"></a>Versiegeschiedenis van elastische databaseclient

Ga voor versiegeschiedenis naar [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
