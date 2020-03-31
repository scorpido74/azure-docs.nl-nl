---
title: Aan de slag met Elastic Database Tools
description: Basisuitleg van de functie Elastic Database Tools van Azure SQL Database, inclusief een eenvoudig uit te voeren voorbeeld-app.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 4577f611287a27366b1168e07565861c16fe0e38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987019"
---
# <a name="get-started-with-elastic-database-tools"></a>Aan de slag met Elastic Database Tools

Dit document laat u kennismaken met de ontwikkelaarservaring voor de [elastische databaseclientbibliotheek](sql-database-elastic-database-client-library.md) door u te helpen een voorbeeld-app uit te voeren. De voorbeeld-app maakt een eenvoudige geshardtoepassing en verkent de belangrijkste mogelijkheden van de functie Elastic Database Tools van Azure SQL Database. Het richt zich op use cases voor [shard map management,](sql-database-elastic-scale-shard-map-management.md) [data-dependent routing,](sql-database-elastic-scale-data-dependent-routing.md)en [multi-shard querying](sql-database-elastic-scale-multishard-querying.md). De clientbibliotheek is beschikbaar voor .NET en Java. 

## <a name="elastic-database-tools-for-java"></a>Elastic Database Tools voor Java

### <a name="prerequisites"></a>Vereisten

* Een Java Developer Kit (JDK), versie 1.8 of hoger
* [Maven](https://maven.apache.org/download.cgi)
* Een SQL Database-server in Azure of een lokaal SQL Server-exemplaar

### <a name="download-and-run-the-sample-app"></a>De voorbeeld-app downloaden en uitvoeren

Ga als volgt te werk om de JAR-bestanden te bouwen en aan de slag te gaan met het voorbeeldproject: 
1. Kloon de [GitHub-repository](https://github.com/Microsoft/elastic-db-tools-for-java) met de clientbibliotheek, samen met de voorbeeld-app. 

2. Bewerk het bestand _./sample/src/main/resources/resource.properties_ om het volgende in te stellen:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Voer de volgende opdracht uit om het voorbeeldproject te bouwen in de _./sample-map:_

    ```
    mvn install
    ```
    
4. Voer de volgende opdracht uit om het voorbeeldproject te starten in de _./sample-map:_ 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Voor meer informatie over de mogelijkheden van de clientbibliotheek, experimenteert u met de verschillende opties. Ontdek gerust de code om meer te weten te komen over de implementatie van de voorbeeld-app.

    ![Voortgang-java][5]
    
Gefeliciteerd! U hebt uw eerste geshard toepassing met succes gebouwd en uitgevoerd met behulp van Elastic Database Tools op Azure SQL Database. Gebruik Visual Studio of SQL Server Management Studio om verbinding te maken met uw SQL-database en snel te kijken naar de scherven die het voorbeeld heeft gemaakt. U ziet nieuwe voorbeeldsharddatabases en een shardmapmanagerdatabase die het voorbeeld heeft gemaakt. 

Als u de clientbibliotheek wilt toevoegen aan uw eigen Maven-project, voegt u de volgende afhankelijkheid toe aan uw POM-bestand:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Hulpmiddelen voor elastische database voor .NET

### <a name="prerequisites"></a>Vereisten

* Visual Studio 2012 of hoger met C#. Download een gratis versie bij [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 of hoger. Zie [NuGet installeren voor](https://docs.nuget.org/docs/start-here/installing-nuget)de nieuwste versie.

### <a name="download-and-run-the-sample-app"></a>De voorbeeld-app downloaden en uitvoeren

Als u de bibliotheek wilt installeren, gaat u naar [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). De bibliotheek is geïnstalleerd met de voorbeeld-app die in de volgende sectie wordt beschreven.

Voer de volgende stappen uit om het voorbeeld te downloaden en uit te voeren: 

1. Download het [voorbeeld Elastic DB Tools voor Azure SQL - Aan de slag.](https://github.com/Azure/elastic-db-tools) Rits het voorbeeld uit naar een locatie die u kiest.

2. Als u een project wilt maken, opent u de *ElasticScaleStarterKit.sln-oplossing* vanuit de *C#-map.*

3. Open het *bestand app.config* in de oplossing voor het voorbeeldproject. Volg vervolgens de instructies in het bestand om de naam van uw Azure SQL Database-server en uw aanmeldingsgegevens (gebruikersnaam en wachtwoord) toe te voegen.

4. Maak de toepassing en voer deze uit. Wanneer u wordt gevraagd, schakelt u Visual Studio in om de NuGet-pakketten van de oplossing te herstellen. Met deze actie wordt de nieuwste versie van de elastische databaseclientbibliotheek van NuGet gedownload.

5. Voor meer informatie over de mogelijkheden van de clientbibliotheek, experimenteert u met de verschillende opties. Let op de stappen die de toepassing neemt in de console-uitvoer en voel je vrij om de code achter de schermen te verkennen.
   
    ![Voortgang][4]

Gefeliciteerd! U hebt uw eerste geshard toepassing met succes gebouwd en uitgevoerd met Elastic Database Tools in SQL Database. Gebruik Visual Studio of SQL Server Management Studio om verbinding te maken met uw SQL-database en snel te kijken naar de scherven die het voorbeeld heeft gemaakt. U ziet nieuwe voorbeeldsharddatabases en een shardmapmanagerdatabase die het voorbeeld heeft gemaakt.

> [!IMPORTANT]
> We raden u aan altijd de nieuwste versie van Management Studio te gebruiken, zodat u gesynchroniseerd blijft met updates voor Azure en SQL Database. [SQL Server Management Studio bijwerken](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Sleutelstukken van het codemonster

* **Shards en shardkaarten beheren:** de code illustreert hoe u met shards, bereiken en toewijzingen werken in het *ShardManagementUtils.cs* bestand. Zie [Databases uitschalen met de shardmapmanager](https://go.microsoft.com/?linkid=9862595)voor meer informatie.  

* **Gegevensafhankelijke routering**: Het routeren van transacties naar de juiste shard wordt weergegeven in het *DataDependentRoutingSample.cs* bestand. Zie [Gegevensafhankelijke routering voor](https://go.microsoft.com/?linkid=9862596)meer informatie. 

* **Query's over meerdere scherven:** Het opvragen over shards wordt geïllustreerd in het *MultiShardQuerySample.cs* bestand. Zie [Multishard query's voor](https://go.microsoft.com/?linkid=9862597)meer informatie.

* **Lege scherven toevoegen:** Het iteratieve toevoegen van nieuwe lege scherven wordt uitgevoerd door de code in het *CreateShardSample.cs* bestand. Zie [Databases uitschalen met de shardmapmanager](https://go.microsoft.com/?linkid=9862595)voor meer informatie.

## <a name="other-elastic-scale-operations"></a>Andere elastische schaalbewerkingen

* **Een bestaande shard splitsen**: de mogelijkheid om shards te splitsen wordt geleverd door het gereedschap splitsen. Zie [Gegevens verplaatsen tussen geschaalde clouddatabases](sql-database-elastic-scale-overview-split-and-merge.md)voor meer informatie.

* **Bestaande shards samenvoegen**: Shard-samenvoegingen worden ook uitgevoerd met het gereedschap splitsen samenvoegen. Zie [Gegevens verplaatsen tussen geschaalde clouddatabases](sql-database-elastic-scale-overview-split-and-merge.md)voor meer informatie.   

## <a name="cost"></a>Kosten

De bibliotheek Elastic Database Tools is gratis. Wanneer u Hulpmiddelen voor elastic database gebruikt, hoeft u geen extra kosten te maken die hoger zijn dan de kosten van uw Azure-gebruik. 

De voorbeeldtoepassing maakt bijvoorbeeld nieuwe databases. De kosten van deze mogelijkheid zijn afhankelijk van de SQL Database-editie die u kiest en het Azure-gebruik van uw toepassing.

Zie SQL [Database-prijsdetails voor](https://azure.microsoft.com/pricing/details/sql-database/)prijsinformatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over hulpmiddelen voor elastic database:

* Codevoorbeelden: 
  * Elastic Database Tools ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic Database Tools voor Azure SQL - Entiteitframework-integratie](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Shardelasticiteit op scriptcentrum](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Aankondiging elastic scale](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Kanaal 9: [Overzichtsvideo Elastische schaal](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Discussieforum: [Azure SQL Database-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Om prestaties te meten: [Prestatiemeteritems voor shardmapbeheer](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

