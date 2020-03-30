---
title: Over clouddatabases gedistribueerde transacties
description: Overzicht van elastische databasetransacties met Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 3ca3e9074f28d66068d49b80915e98600759d9be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68568287"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Over clouddatabases gedistribueerde transacties

Met elastische databasetransacties voor Azure SQL Database (SQL DB) u transacties uitvoeren die meerdere databases in SQL DB omvatten. Elastische databasetransacties voor SQL DB zijn beschikbaar voor .NET-toepassingen met ADO .NET en integreren met de vertrouwde programmeerervaring met behulp van de klassen [System.Transaction.](https://msdn.microsoft.com/library/system.transactions.aspx) Zie [.NET Framework 4.6.1 (Web Installer)](https://www.microsoft.com/download/details.aspx?id=49981)om de bibliotheek te krijgen.

On premises, een dergelijk scenario meestal vereist draait Microsoft Distributed Transaction Coordinator (MSDTC). Aangezien MSDTC niet beschikbaar is voor Platform-as-a-Service-toepassing in Azure, is de mogelijkheid om gedistribueerde transacties te coördineren nu rechtstreeks geïntegreerd in SQL DB. Toepassingen kunnen verbinding maken met elke SQL-database om gedistribueerde transacties te starten en een van de databases coördineert de gedistribueerde transactie transparant, zoals in de volgende afbeelding wordt weergegeven. 

  ![Gedistribueerde transacties met Azure SQL Database met elastische databasetransacties ][1]

## <a name="common-scenarios"></a>Algemene scenario's

Elastische databasetransacties voor SQL DB stellen toepassingen in staat om atomaire wijzigingen aan te brengen in gegevens die zijn opgeslagen in verschillende SQL-databases. De preview richt zich op klant-side development ervaringen in C# en .NET. Een server-side ervaring met behulp van T-SQL is gepland voor een later tijdstip.  
Elastische databasetransacties zijn gericht op de volgende scenario's:

* Multi-databasetoepassingen in Azure: Met dit scenario worden gegevens verticaal verdeeld over verschillende databases in SQL DB, zodat verschillende soorten gegevens zich op verschillende databases bevinden. Voor sommige bewerkingen zijn wijzigingen in gegevens vereist die in twee of meer databases worden bewaard. De toepassing maakt gebruik van elastische databasetransacties om de wijzigingen in databases te coördineren en atomiciteit te garanderen.
* Gesharde databasetoepassingen in Azure: met dit scenario gebruikt de gegevenslaag de [elastic database-clientbibliotheek](sql-database-elastic-database-client-library.md) of zelfsharding om de gegevens horizontaal te verdelen over veel databases in SQL DB. Een prominente use case is de noodzaak om atomaire wijzigingen uit te voeren voor een geshard multi-tenant toepassing bij wijzigingen over tenants. Denk bijvoorbeeld aan een overdracht van de ene huurder naar de andere, beide woonachtig in verschillende databases. Een tweede geval is fijnkorrelige sharding om tegemoet te komen aan de capaciteitsbehoeften voor een grote tenant, wat op zijn beurt meestal impliceert dat sommige atomaire bewerkingen zich moeten uitstrekken over verschillende databases die voor dezelfde tenant worden gebruikt. Een derde geval is atoomupdates voor referentiegegevens die worden gerepliceerd in databases. Atoom, afgehandeld, bewerkingen langs deze lijnen kunnen nu worden gecoördineerd in verschillende databases met behulp van de preview.
  Elastische databasetransacties maken gebruik van tweefasencommit om transactieataliteit tussen databases te garanderen. Het is een goede pasvorm voor transacties die minder dan 100 databases tegelijk binnen een enkele transactie omvatten. Deze limieten worden niet gehandhaafd, maar men moet verwachten dat de prestaties en slagingspercentages voor elastische databasetransacties zullen lijden wanneer deze limieten worden overschreden.

## <a name="installation-and-migration"></a>Installatie en migratie

De mogelijkheden voor elastische databasetransacties in SQL DB worden geleverd via updates voor de .NET-bibliotheken System.Data.dll en System.Transactions.dll. De DLL's zorgen ervoor dat twee-fase commit waar nodig wordt gebruikt om atomiciteit te garanderen. Als u toepassingen wilt ontwikkelen met elastische databasetransacties, installeert u [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) of een latere versie. Wanneer transacties worden uitgevoerd op een eerdere versie van het .NET-framework, worden transacties niet gepromoot naar een gedistribueerde transactie en wordt een uitzondering verhoogd.

Na de installatie u de gedistribueerde transactie-API's gebruiken in System.Transactions met verbindingen met SQL DB. Als u bestaande MSDTC-toepassingen hebt met behulp van deze API's, u uw bestaande toepassingen voor .NET 4.6 opnieuw opbouwen nadat u het 4.6.1-framework hebt geïnstalleerd. Als uw projecten op .NET 4.6 zijn gericht, gebruiken ze automatisch de bijgewerkte DLL's van de nieuwe Framework-versie en zullen gedistribueerde transactie-API-aanroepen in combinatie met verbindingen met SQL DB nu slagen.

Houd er rekening mee dat elastische databasetransacties geen MSDTC hoeven te installeren. In plaats daarvan worden elastische databasetransacties rechtstreeks beheerd door en binnen SQL DB. Dit vereenvoudigt cloudscenario's aanzienlijk, omdat een implementatie van MSDTC niet nodig is om gedistribueerde transacties met SQL DB te gebruiken. In deel 4 wordt nader uitgelegd hoe elastische databasetransacties en het vereiste .NET-framework samen met uw cloudtoepassingen kunnen worden geïmplementeerd in Azure.

## <a name="development-experience"></a>Ontwikkelingservaring

### <a name="multi-database-applications"></a>Toepassingen met meerdere gegevens

De volgende voorbeeldcode maakt gebruik van de vertrouwde programmeerervaring met .NET System.Transactions. Met de klasse TransactionScope wordt een omgevingstransactie in .NET vastgelegd. (Een "ambient transactie" is er een die leeft in de huidige thread.) Alle verbindingen die binnen de TransactionScope zijn geopend, nemen deel aan de transactie. Als verschillende databases deelnemen, wordt de transactie automatisch verhoogd tot een gedistribueerde transactie. Het resultaat van de transactie wordt gecontroleerd door de scope in te stellen die moet worden voltooid om een commit aan te geven.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Geshard databasetoepassingen

Elastische databasetransacties voor SQL DB ondersteunen ook het coördineren van gedistribueerde transacties waarbij u de OpenConnectionForKey-methode van de elastische databaseclientbibliotheek gebruikt om verbindingen te openen voor een geschaalde gegevenslaag. Houd rekening met gevallen waarin u transactionele consistentie moet garanderen voor wijzigingen in verschillende sharding-hoofdwaarden. Verbindingen met de scherven die de verschillende sharding-kernwaarden hosten, worden bemiddeld met OpenConnectionForKey. In het algemeen kunnen de verbindingen worden gelegd op verschillende scherven, zodat het waarborgen van transactionele garanties een gedistribueerde transactie vereist. In het volgende voorbeeld van de code wordt deze benadering geïllustreerd. Er wordt van uitgegaan dat een variabele genaamd shardmap wordt gebruikt om een shardkaart uit de elastische databaseclientbibliotheek weer te geven:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>.NET-installatie voor Azure Cloud Services

Azure biedt verschillende aanbiedingen voor het hosten van .NET-toepassingen. Een vergelijking van de verschillende aanbiedingen is beschikbaar in [azure-appservice, cloudservices en vergelijking van virtuele machines.](/azure/architecture/guide/technology-choices/compute-decision-tree) Als het gastbesturingssysteem van het aanbod kleiner is dan .NET 4.6.1 dat vereist is voor elastische transacties, moet u het gastbesturingssysteem upgraden naar 4.6.1. 

Voor Azure App Services worden upgrades naar het besturingssysteem van de gast momenteel niet ondersteund. Voor Azure Virtual Machines u zich eenvoudig aanmelden bij de VM en het installatieprogramma uitvoeren voor het nieuwste .NET-framework. Voor Azure Cloud Services moet u de installatie van een nieuwere .NET-versie opnemen in de opstarttaken van uw implementatie. De concepten en stappen zijn gedocumenteerd in [Install .NET on a Cloud Service Role](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Houd er rekening mee dat de installer voor .NET 4.6.1 mogelijk meer tijdelijke opslag nodig heeft tijdens het bootstrappingproces op Azure-cloudservices dan de installer voor .NET 4.6. Om een succesvolle installatie te garanderen, moet u de tijdelijke opslag voor uw Azure-cloudservice in uw ServiceDefinition.csdef-bestand in de sectie LocalResources en de omgevingsinstellingen van uw opstarttaak verhogen, zoals in het volgende voorbeeld wordt weergegeven:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>

## <a name="transactions-across-multiple-servers"></a>Transacties op meerdere servers

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Elastische databasetransacties worden ondersteund op verschillende SQL Database-servers in Azure SQL Database. Wanneer transacties de grenzen van de SQL Database-server overschrijden, moeten de deelnemende servers eerst een wederzijdse communicatierelatie aangaan. Zodra de communicatierelatie is vastgesteld, kan elke database in een van de twee servers deelnemen aan elastische transacties met databases van de andere server. Met transacties die meer dan twee SQL Database-servers omvatten, moet er een communicatierelatie zijn voor elk paar SQL Database-servers.

Gebruik de volgende PowerShell-cmdlets om communicatierelaties tussen servers te beheren voor elastische databasetransacties:

* **Nieuw-AzSqlServerCommunicationLink**: Gebruik deze cmdlet om een nieuwe communicatierelatie te maken tussen twee SQL Database-servers in Azure SQL Database. De relatie is symmetrisch, wat betekent dat beide servers transacties met de andere server kunnen initiëren.
* **Get-AzSqlServerCommunicationLink**: Gebruik deze cmdlet om bestaande communicatierelaties en hun eigenschappen op te halen.
* **Remove-AzSqlServerCommunicationLink**: Gebruik deze cmdlet om een bestaande communicatierelatie te verwijderen. 

## <a name="monitoring-transaction-status"></a>Transactiestatus bewaken

Gebruik Dynamic Management Views (DMVs) in SQL DB om de status en voortgang van uw lopende elastische databasetransacties te controleren. Alle DMVs met betrekking tot transacties zijn relevant voor gedistribueerde transacties in SQL DB. U vindt de bijbehorende lijst met DMVs hier: [Transaction Related Dynamic Management Views and Functions (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Deze DM's zijn bijzonder nuttig:

* **sys.dm\_\_tran\_actieve transacties:** Lijsten momenteel actieve transacties en hun status. De kolom UOW (Unit Of Work) kan de verschillende onderliggende transacties identificeren die tot dezelfde gedistribueerde transactie behoren. Alle transacties binnen dezelfde gedistribueerde transactie hebben dezelfde UOW-waarde. Zie de [DMV documentatie](https://msdn.microsoft.com/library/ms174302.aspx) voor meer informatie.
* **sys.dm\_\_tran\_database transacties:** Biedt aanvullende informatie over transacties, zoals plaatsing van de transactie in het logboek. Zie de [DMV documentatie](https://msdn.microsoft.com/library/ms186957.aspx) voor meer informatie.
* **sys.dm\_\_tran-vergrendelingen**: geeft informatie over de vergrendelingen die momenteel worden bewaard door lopende transacties. Zie de [DMV documentatie](https://msdn.microsoft.com/library/ms190345.aspx) voor meer informatie.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn momenteel van toepassing op elastische databasetransacties in SQL DB:

* Alleen transacties in databases in SQL DB worden ondersteund. Andere [X/Open XA-bronproviders](https://en.wikipedia.org/wiki/X/Open_XA) en databases buiten SQL DB kunnen niet deelnemen aan elastische databasetransacties. Dat betekent dat elastische databasetransacties zich niet kunnen uitstrekken over sql server en Azure SQL Database. Voor gedistribueerde transacties op locatie, blijven MSDTC gebruiken. 
* Alleen door de klant gecoördineerde transacties vanuit een .NET-toepassing worden ondersteund. Server-side ondersteuning voor T-SQL, zoals BEGIN DISTRIBUTED TRANSACTION is gepland, maar nog niet beschikbaar. 
* Transacties in WCF-services worden niet ondersteund. U hebt bijvoorbeeld een WCF-servicemethode waarmee een transactie wordt uitgevoerd. Het bijvoegen van de aanroep binnen een transactiebereik mislukt als [system.servicemodel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Volgende stappen

Voor vragen u contact met ons opnemen op het [SQL Database-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) en voor functieverzoeken u deze toevoegen aan het [SQL Database-feedbackforum.](https://feedback.azure.com/forums/217321-sql-database/)

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
