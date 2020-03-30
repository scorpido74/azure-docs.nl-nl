---
title: Elastische databaseclientbibliotheek gebruiken met Dapper
description: Met behulp van elastische database client bibliotheek met Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 83d24d45d7628a2e02068c8757fa6568d6d3fc37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823479"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Elastische databaseclientbibliotheek gebruiken met Dapper
Dit document is voor ontwikkelaars die afhankelijk zijn van Dapper om applicaties te bouwen, maar ook [elastische databasetooling](sql-database-elastic-scale-introduction.md) willen omarmen om toepassingen te maken die sharding implementeren om hun gegevenslaag uit te schalen.  Dit document illustreert de wijzigingen in dapper-gebaseerde toepassingen die nodig zijn om te integreren met elastische database tools. Onze focus ligt op het samenstellen van de elastic database shard management en data-dependent routing met Dapper. 

**Voorbeeldcode:** [Elastic database-hulpprogramma's voor Azure SQL Database - Dapper-integratie](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Het integreren van **Dapper** en **DapperExtensions** met de elastische databaseclientbibliotheek voor Azure SQL Database is eenvoudig. Uw toepassingen kunnen gegevensafhankelijke routering gebruiken door het maken en openen van nieuwe [SqlConnection-objecten](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) te wijzigen om de [OpenConnectionForKey-aanroep](https://msdn.microsoft.com/library/azure/dn807226.aspx) uit de [clientbibliotheek](https://msdn.microsoft.com/library/azure/dn765902.aspx)te gebruiken. Dit beperkt wijzigingen in uw toepassing tot alleen wanneer nieuwe verbindingen worden gemaakt en geopend. 

## <a name="dapper-overview"></a>Overzicht van Dapper
**Dapper** is een objectrelationele mapper. Het brengt .NET-objecten van uw toepassing in kaart met een relationele database (en vice versa). Het eerste deel van de voorbeeldcode illustreert hoe u de elastische databaseclientbibliotheek integreren met dapper-gebaseerde toepassingen. Het tweede deel van de voorbeeldcode illustreert hoe u integreren bij het gebruik van zowel Dapper als DapperExtensions.  

De mapper-functionaliteit in Dapper biedt extensiemethoden op databaseverbindingen die het indienen van T-SQL-instructies voor uitvoering of het opvragen van de database vereenvoudigen. Dapper maakt het bijvoorbeeld eenvoudig om tussen uw .NET-objecten en de parameters van SQL-instructies voor **Uitvoeren-oproepen** in kaart te brengen of de resultaten van uw SQL-query's te gebruiken in .NET-objecten met **queryoproepen** van Dapper. 

Wanneer u DapperExtensions gebruikt, hoeft u de SQL-instructies niet meer op te geven. Met extensiesmethoden zoals **GetList** of **Invoegen** via de databaseverbinding worden de SQL-instructies achter de schermen gemaakt.

Een ander voordeel van Dapper en ook DapperExtensions is dat de toepassing de creatie van de databaseverbinding regelt. Dit helpt interactie met de elastische database client bibliotheek die makelaars database verbindingen op basis van het toewijzen van shardlets naar databases.

Om de Dapper assemblages te krijgen, zie [Dapper dot net](https://www.nuget.org/packages/Dapper/). Zie DapperExtensions voor de [Dapper-extensies.](https://www.nuget.org/packages/DapperExtensions)

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Een snelle blik op de elastische databaseclientbibliotheek
Met de elastische databaseclientbibliotheek definieert u partities van uw toepassingsgegevens die *shardlets*worden genoemd, brengt u ze in kaart aan databases en identificeert u ze door *sleutels te sharden.* U zoveel databases hebben als u nodig hebt en uw shardlets over deze databases distribueren. Het in kaart brengen van de kernwaarden voor de databases wordt opgeslagen door een shardkaart die wordt geleverd door de API's van de bibliotheek. Deze mogelijkheid wordt **shard mapmanagement**genoemd. De shard kaart dient ook als de makelaar van database verbindingen voor verzoeken die een sharding sleutel dragen. Deze mogelijkheid wordt aangeduid als **gegevensafhankelijke routering**.

![Shardkaarten en gegevensafhankelijke routering][1]

De shardmapmanager beschermt gebruikers tegen inconsistente weergaven in shardletgegevens die kunnen optreden wanneer gelijktijdige beheerbewerkingen voor shardlet in de databases plaatsvinden. Om dit te doen, de shard kaarten makelaar de database verbindingen voor een toepassing gebouwd met de bibliotheek. Wanneer shardbeheerbewerkingen van invloed kunnen zijn op de shardlet, kan de shardkaartfunctionaliteit automatisch een databaseverbinding verwijderen. 

In plaats van de traditionele manier te gebruiken om verbindingen voor Dapper te maken, moet u de [Methode OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn824099.aspx)gebruiken. Dit zorgt ervoor dat alle validatie plaatsvindt en verbindingen correct worden beheerd wanneer gegevens tussen shards worden verplaatst.

### <a name="requirements-for-dapper-integration"></a>Eisen voor Dapper integratie
Wanneer u werkt met zowel de elastische databaseclientbibliotheek als de Dapper API's, wilt u de volgende eigenschappen behouden:

* **Uitschalen**: We willen databases toevoegen of verwijderen uit de gegevenslaag van de geshard toepassing als dat nodig is voor de capaciteitsvereisten van de toepassing. 
* **Consistentie:** Aangezien de toepassing is uitgeschroefd met sharding, moet u gegevensafhankelijke routering uitvoeren. Hiervoor willen we gebruik maken van de gegevensafhankelijke routeringsmogelijkheden van de bibliotheek. In het bijzonder wilt u de validatie- en consistentiegaranties behouden die worden geboden door verbindingen die zijn bebrokereerd via de shardmapbeheerder om beschadiging of verkeerde queryresultaten te voorkomen. Dit zorgt ervoor dat verbindingen met een bepaalde shardlet worden geweigerd of gestopt als (bijvoorbeeld) de shardlet momenteel wordt verplaatst naar een andere shard met behulp van Split/Merge API's.
* **Object mapping**: We willen het gemak van de mappings van Dapper behouden om te vertalen tussen klassen in de applicatie en de onderliggende databasestructuren. 

De volgende sectie geeft richtlijnen voor deze vereisten voor toepassingen op basis **van Dapper** en **DapperExtensions**.

## <a name="technical-guidance"></a>Technische richtlijnen
### <a name="data-dependent-routing-with-dapper"></a>Gegevensafhankelijke routering met Dapper
Met Dapper is de toepassing doorgaans verantwoordelijk voor het maken en openen van de verbindingen met de onderliggende database. Als dapper queryresultaten door een type T van de toepassing geeft, retourneert als .NET-verzamelingen van type T. Dapper voert de toewijzing uit van de Resultatenrijen van T-SQL naar de objecten van type T. Op dezelfde manier brengt Dapper .NET-objecten in SQL-waarden of -parameters voor DML-instructies (Data Manipulation Language). Dapper biedt deze functionaliteit via extensiemethoden op het reguliere [SqlConnection-object](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) van de ADO .NET SQL Client-bibliotheken. De SQL-verbinding die wordt geretourneerd door de Elastic Scale API's voor DDR zijn ook gewone [SqlConnection-objecten.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) Dit stelt ons in staat om Dapper-extensies rechtstreeks te gebruiken over het type dat wordt geretourneerd door de DDR-API van de clientbibliotheek, omdat het ook een eenvoudige SQL-clientverbinding is.

Deze observaties maken het eenvoudig om verbindingen te gebruiken die door de elastische bibliotheek van de gegevensbestandcliënt voor Dapper worden bemiddeld.

Dit codevoorbeeld (uit het begeleidende voorbeeld) illustreert de aanpak waarbij de shardingssleutel door de toepassing aan de bibliotheek wordt geleverd om de verbinding met de juiste shard te bemiddelen.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

De aanroep naar de [OpenConnectionForKey-API](https://msdn.microsoft.com/library/azure/dn807226.aspx) vervangt de standaardcreatie en het openen van een SQL-clientverbinding. De [OpenConnectionForKey-aanroep](https://msdn.microsoft.com/library/azure/dn807226.aspx) voert de argumenten aan die nodig zijn voor gegevensafhankelijke routering: 

* De shardkaart voor toegang tot de gegevensafhankelijke routeringsinterfaces
* De sharding sleutel om de shardlet te identificeren
* De referenties (gebruikersnaam en wachtwoord) om verbinding te maken met de scherf

Het shardmapobject maakt een verbinding met de scherf die de shardlet voor de opgegeven shardingssleutel vasthoudt. De elastische databaseclient API's taggen ook de verbinding om de consistentiegaranties te implementeren. Aangezien de aanroep naar [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) een normaal SQL Client-verbindingsobject retourneert, volgt de volgende aanroep naar de **extensiemethode Uitvoeren** van Dapper de standaard Dapper-praktijk.

Query's werken op dezelfde manier - u opent eerst de verbinding met [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) vanuit de client-API. Vervolgens gebruikt u de reguliere Dapper-extensiemethoden om de resultaten van uw SQL-query in .NET-objecten in kaart te brengen:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Houd er rekening mee dat het **gebruiksblok** met de DDR-verbinding alle databasebewerkingen binnen het blok beperkt tot de ene shard waar tenantId1 wordt bewaard. De query retourneert alleen blogs die zijn opgeslagen op de huidige shard, maar niet de blogs die zijn opgeslagen op andere scherven. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Gegevensafhankelijke routering met Dapper en DapperExtensions
Dapper wordt geleverd met een ecosysteem van extra extensies die meer gemak en abstractie uit de database kunnen bieden bij het ontwikkelen van databasetoepassingen. DapperExtensions is een voorbeeld. 

Het gebruik van DapperExtensions in uw toepassing verandert niets aan de manier waarop databaseverbindingen worden gemaakt en beheerd. Het is nog steeds de verantwoordelijkheid van de toepassing om verbindingen te openen en regelmatige SQL Client-verbindingsobjecten worden verwacht door de extensiemethoden. We kunnen vertrouwen op de [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) zoals hierboven beschreven. Zoals de volgende codevoorbeelden laten zien, is de enige wijziging dat u de T-SQL-instructies niet meer hoeft te schrijven:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

En hier is de code voorbeeld voor de query: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Tijdelijke fouten verwerken
Het team Microsoft Patterns & Practices heeft het [tijdelijke foutafhandelingstoepassingsblok](https://msdn.microsoft.com/library/hh680934.aspx) gepubliceerd om toepassingsontwikkelaars te helpen veelvoorkomende tijdelijke foutomstandigheden te beperken die zich voordoen bij het uitvoeren in de cloud. Zie [Volharding, Geheim van alle triomfen: Het tijdelijke storingsbehandelingstoepassingsblok gebruiken](https://msdn.microsoft.com/library/dn440719.aspx)voor meer informatie.

Het codevoorbeeld is gebaseerd op de tijdelijke foutbibliotheek om te beschermen tegen tijdelijke fouten. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** in de bovenstaande code wordt gedefinieerd als een **SqlDatabaseTransientErrorDetectionStrategy** met een hernieuwingsaantal van 10 en 5 seconden wachttijd tussen nieuwe pogingen. Als u transacties gebruikt, moet u ervoor zorgen dat uw nieuwe scope teruggaat tot het begin van de transactie in het geval van een tijdelijke fout.

## <a name="limitations"></a>Beperkingen
De in dit document beschreven benaderingen brengen een aantal beperkingen met zich mee:

* De voorbeeldcode voor dit document laat niet zien hoe u het schema over shards beheren.
* Gezien een verzoek, gaan we ervan uit dat al de database verwerking is opgenomen in een enkele scherf zoals geïdentificeerd door de sharding sleutel die door het verzoek. Deze veronderstelling houdt echter niet altijd stand, bijvoorbeeld wanneer het niet mogelijk is om een shardingsleutel beschikbaar te stellen. Om dit aan te pakken, bevat de elastische databaseclientbibliotheek de [klasse MultiShardQuery.](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx) De klasse implementeert een verbindingsabstractie voor query's over meerdere shards. Het gebruik van MultiShardQuery in combinatie met Dapper valt buiten het bereik van dit document.

## <a name="conclusion"></a>Conclusie
Toepassingen met Dapper en DapperExtensions kunnen eenvoudig profiteren van elastische databasehulpprogramma's voor Azure SQL Database. Via de stappen die in dit document worden beschreven, kunnen deze toepassingen de mogelijkheden van de tool gebruiken voor gegevensafhankelijke routering door het maken en openen van nieuwe [SqlConnection-objecten](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) te wijzigen om de [OpenConnectionForKey-aanroep](https://msdn.microsoft.com/library/azure/dn807226.aspx) van de elastische databaseclientbibliotheek te gebruiken. Dit beperkt de toepassingswijzigingen die nodig zijn voor de plaatsen waar nieuwe verbindingen worden gemaakt en geopend. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
