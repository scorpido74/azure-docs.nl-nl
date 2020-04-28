---
title: Over clouddatabases gedistribueerde transacties
description: Overzicht van Elastic Database transacties met Azure SQL Database
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68568287"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Over clouddatabases gedistribueerde transacties

Met Elastic data base trans Actions for Azure SQL Database (SQL DB) kunt u trans acties uitvoeren die meerdere data bases omvatten in SQL DB. Elastic data base-trans acties voor SQL DB zijn beschikbaar voor .NET-toepassingen met behulp van ADO .NET en kunnen worden geïntegreerd met de bekende programmeer ervaring met behulp van de [System. Trans Action](https://msdn.microsoft.com/library/system.transactions.aspx) -klassen. Zie [.NET Framework 4.6.1 (web installer)](https://www.microsoft.com/download/details.aspx?id=49981)om de tape wisselaar op te halen.

On-premises, een scenario dat doorgaans vereist is voor het uitvoeren van micro soft Distributed Transaction Coordinator (MSDTC). Omdat MSDTC niet beschikbaar is voor platform-as-a-service-toepassingen in azure, is de mogelijkheid om gedistribueerde trans acties te coördineren, nu rechtstreeks geïntegreerd in SQL DB. Toepassingen kunnen verbinding maken met alle SQL Database om gedistribueerde trans acties te starten. een van de data bases kan de gedistribueerde trans actie transparant coördineren, zoals in de volgende afbeelding wordt weer gegeven. 

  ![Gedistribueerde trans acties met Azure SQL Database met Elastic data base-trans acties ][1]

## <a name="common-scenarios"></a>Algemene scenario's

Met Elastic data base-trans acties voor SQL data base kunnen toepassingen atomische wijzigingen aanbrengen in gegevens die zijn opgeslagen in verschillende SQL-data bases. Het voor beeld is gericht op de ontwikkelings ervaringen aan client zijde in C# en .NET. Een server ervaring met T-SQL is gepland voor een later tijdstip.  
Elastische-database transacties zijn gericht op de volgende scenario's:

* Toepassingen voor meerdere data bases in Azure: in dit scenario worden gegevens verticaal gepartitioneerd over verschillende data bases in SQL DB, zodat verschillende soorten gegevens zich op verschillende data bases bevinden. Voor sommige bewerkingen zijn wijzigingen in gegevens vereist die in twee of meer data bases worden bewaard. De toepassing maakt gebruik van Elastic data base-trans acties voor het coördineren van de wijzigingen in data bases en zorgt voor een atomiciteit.
* Shard-database toepassingen in Azure: in dit scenario gebruikt de gegevenslaag de [Elastic database-client bibliotheek](sql-database-elastic-database-client-library.md) of Self-sharding om de gegevens in verschillende data bases horizon taal te partitioneren in SQL DB. Eén prominente use case is de nood zaak om atomische wijzigingen uit te voeren voor een Shard multi tenant-toepassing wanneer de tenants worden gewijzigd. Denk na over een overdracht van de ene Tenant naar de andere, beide op verschillende data bases. Een tweede geval is een nauw keurige sharding om de capaciteits behoeften voor een grote Tenant in te passen die doorgaans in de praktijk impliceert dat sommige atomische bewerkingen moeten worden uitgerekt over verschillende data bases die voor dezelfde Tenant worden gebruikt. Een derde geval is atomische updates om te verwijzen naar gegevens die in data bases worden gerepliceerd. Atomische, transactionele bewerkingen op deze regels kunnen nu worden gecoördineerd over verschillende data bases met behulp van de preview-versie.
  Elastische-database transacties gebruiken twee fasen door voeren om transactie atomiciteit in data bases te garanderen. Het is geschikt voor trans acties met minder dan 100 data bases tegelijk binnen één trans actie. Deze limieten worden niet afgedwongen, maar één moet rekening houden met prestaties en succes percentages voor Elastic data base-trans acties wanneer deze limieten worden overschreden.

## <a name="installation-and-migration"></a>Installatie en migratie

De mogelijkheden voor Elastic data base-trans acties in SQL DB worden geboden via updates voor de .NET-bibliotheken System. data. dll en System. Trans actions. dll. De Dll's zorgen ervoor dat door voeren in twee fasen wordt gebruikt wanneer dit nodig is om atomisch te garanderen. Als u toepassingen wilt ontwikkelen met Elastic data base trans acties, installeert u [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) of een latere versie. Bij het uitvoeren van een eerdere versie van .NET Framework kunnen trans acties niet worden gepromoveerd naar een gedistribueerde trans actie. er wordt een uitzonde ring gegenereerd.

Na de installatie kunt u de Api's voor Distributed Trans Action gebruiken in System. Trans actions met verbindingen met SQL data base. Als u beschikt over bestaande MSDTC-toepassingen die gebruikmaken van deze Api's, bouwt u uw bestaande toepassingen voor .NET 4,6 opnieuw op nadat u het 4.6.1-Framework hebt geïnstalleerd. Als uw projecten .NET 4,6 doel hebben, zullen ze automatisch gebruikmaken van de bijgewerkte dll-bestanden van de nieuwe Framework-versie en gedistribueerde API-aanroepen in combi natie met verbindingen met SQL data base.

Houd er rekening mee dat voor Elastic data base-trans acties geen MSDTC moet worden geïnstalleerd. In plaats daarvan worden Elastic data base-trans acties rechtstreeks beheerd door en in SQL data base. Hierdoor worden Cloud scenario's aanzienlijk vereenvoudigd omdat een implementatie van MSDTC niet nodig is voor het gebruik van gedistribueerde trans acties met SQL DB. Sectie 4 bevat meer informatie over het implementeren van elastische database transacties en de vereiste .NET Framework samen met uw Cloud toepassingen naar Azure.

## <a name="development-experience"></a>Ontwikkel ervaring

### <a name="multi-database-applications"></a>Toepassingen voor meerdere data bases

De volgende voorbeeld code maakt gebruik van de bekende programmeer ervaring met .NET System. Trans actions. De TransactionScope-klasse brengt een ambient trans actie in .NET tot stand. (Een ' Ambient trans actie ' is een ' in de huidige thread '.) Alle verbindingen die in de TransactionScope zijn geopend, nemen deel aan de trans actie. Als verschillende data bases deel nemen, wordt de trans actie automatisch verhoogd naar een gedistribueerde trans actie. De uitkomst van de trans actie wordt bepaald door het bereik in te stellen op voltooid om aan te geven dat er een doorvoer bewerking is.

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

### <a name="sharded-database-applications"></a>Shard-database toepassingen

Elastic data base-trans acties voor SQL data base ondersteunen ook het coördineren van gedistribueerde trans acties waarbij u de methode OpenConnectionForKey van de client bibliotheek voor Elastic data base gebruikt om verbindingen te openen voor een geschaalde gegevenslaag. Houd rekening met gevallen waarin u transactionele consistentie moet garanderen voor wijzigingen in verschillende sharding-sleutel waarden. Verbindingen met de Shards die de verschillende sharding-sleutel waarden hosten, zijn met behulp van OpenConnectionForKey Broker. In het algemeen is het mogelijk om verbinding te maken met verschillende Shards die ervoor zorgen dat voor transactionele garanties een gedistribueerde trans actie is vereist. In het volgende code voorbeeld ziet u deze aanpak. Hierbij wordt ervan uitgegaan dat een variabele met de naam shardmap wordt gebruikt om een Shard-toewijzing te vertegenwoordigen uit de client bibliotheek voor Elastic Data Base:

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

Azure biedt verschillende aanbiedingen voor het hosten van .NET-toepassingen. Een vergelijking van de verschillende aanbiedingen is beschikbaar in [Azure app service, Cloud Services en virtual machines vergelijking](/azure/architecture/guide/technology-choices/compute-decision-tree). Als het gast besturingssysteem van de aanbieding kleiner is dan .NET 4.6.1 die vereist zijn voor elastische trans acties, moet u het gast besturingssysteem upgraden naar 4.6.1. 

Voor Azure-app services worden upgrades naar het gast besturingssysteem momenteel niet ondersteund. Voor Azure Virtual Machines meldt u zich aan bij de virtuele machine en voert u het installatie programma uit voor de nieuwste versie van .NET Framework. Voor Azure Cloud Services moet u de installatie van een nieuwere versie van .NET toevoegen aan de opstart taken van uw implementatie. De concepten en stappen worden beschreven in [.net installeren op een Cloud service functie](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Houd er rekening mee dat het installatie programma voor .NET 4.6.1 mogelijk meer tijdelijke opslag vereist tijdens het Boots trap proces van Azure Cloud Services dan het installatie programma voor .NET 4,6. Om ervoor te zorgen dat de installatie is voltooid, moet u de tijdelijke opslag voor uw Azure-Cloud service verhogen in het bestand ServiceDefinition. csdef in het gedeelte LocalResources en de omgevings instellingen van de opstart taak, zoals wordt weer gegeven in het volgende voor beeld:

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

## <a name="transactions-across-multiple-servers"></a>Trans acties op meerdere servers

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

Elastische-database transacties worden ondersteund op verschillende SQL Database servers in Azure SQL Database. Wanneer trans acties SQL Database Server grenzen, moeten de deelnemende servers eerst worden ingevoerd in een wederzijdse communicatie relatie. Zodra de communicatie relatie tot stand is gebracht, kan elke data base in een van de twee servers deel nemen aan elastische trans acties met data bases van de andere server. Bij trans acties die meer dan twee SQL Database servers omspannen, moet er een communicatie relatie bestaan voor elk paar SQL Database-servers.

Gebruik de volgende Power shell-cmdlets voor het beheren van communicatie relaties tussen servers voor Elastic data base-trans acties:

* **New-AzSqlServerCommunicationLink**: gebruik deze cmdlet om een nieuwe communicatie relatie te maken tussen twee SQL database servers in Azure SQL database. De relatie is symmetrisch, wat betekent dat beide servers trans acties kunnen initiëren met de andere server.
* **Get-AzSqlServerCommunicationLink**: gebruik deze cmdlet om bestaande communicatie relaties en hun eigenschappen op te halen.
* **Remove-AzSqlServerCommunicationLink**: gebruik deze cmdlet om een bestaande communicatie relatie te verwijderen. 

## <a name="monitoring-transaction-status"></a>Controle transactie status

Gebruik dynamische beheer weergaven (Dmv's) in SQL Data Base om de status en voortgang van uw lopende elastische database transacties te controleren. Alle Dmv's die betrekking hebben op trans acties zijn relevant voor gedistribueerde trans acties in SQL data base. U vindt de bijbehorende lijst met Dmv's hier: [trans actie-gerelateerde dynamische beheer weergaven en-functies (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Deze Dmv's zijn bijzonder nuttig:

* **sys.DM\_Tran\_actieve\_trans acties**: een lijst met momenteel actieve trans acties en hun status. De kolom UOW (eenheid van werk) kan de verschillende onderliggende trans acties identificeren die deel uitmaken van dezelfde gedistribueerde trans actie. Alle trans acties binnen dezelfde gedistribueerde trans actie hebben dezelfde UOW-waarde. Raadpleeg de [dmv-documentatie](https://msdn.microsoft.com/library/ms174302.aspx) voor meer informatie.
* **sys.DM\_Tran\_-\_database transacties**: biedt aanvullende informatie over trans acties, zoals plaatsing van de trans actie in het logboek. Raadpleeg de [dmv-documentatie](https://msdn.microsoft.com/library/ms186957.aspx) voor meer informatie.
* **sys.DM\_Tran\_-vergren delingen**: bevat informatie over de vergren delingen die momenteel worden bewaard door lopende trans acties. Raadpleeg de [dmv-documentatie](https://msdn.microsoft.com/library/ms190345.aspx) voor meer informatie.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn momenteel van toepassing op elastische data base-trans acties in SQL DB:

* Alleen trans acties in data bases in SQL DB worden ondersteund. Andere [X//Open XA-](https://en.wikipedia.org/wiki/X/Open_XA) resource providers en data bases buiten SQL DB kunnen niet deel nemen aan Elastic data base-trans acties. Dit betekent dat Elastic data base-trans acties niet kunnen worden uitgerekt over on-premises SQL Server en Azure SQL Database. Ga verder met het gebruik van MSDTC voor gedistribueerde trans acties op locatie. 
* Alleen door de client samengestelde trans acties van een .NET-toepassing worden ondersteund. De server ondersteuning voor T-SQL zoals BEGIN gedistribueerde trans actie is gepland, maar is nog niet beschikbaar. 
* Trans acties via WCF-services worden niet ondersteund. U hebt bijvoorbeeld een WCF-service methode waarmee een trans actie wordt uitgevoerd. Het sluiten van de aanroep binnen een transactie bereik mislukt als [System. service model. ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Volgende stappen

Voor vragen kunt u contact opnemen met ons op het [SQL database-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) en voor functie aanvragen, voeg ze toe aan het [forum van SQL database feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
