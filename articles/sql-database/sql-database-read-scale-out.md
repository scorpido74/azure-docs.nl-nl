---
title: Query's lezen op replica's
description: De Azure SQL Database biedt de mogelijkheid om alleen-lezen -regelwerk te laden met behulp van de capaciteit van alleen-lezen replica's - lees scale-out genoemd.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206942"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Use read-only replicas to load-balance read-only query workloads (Alleen-lezen replica's gebruiken om de workloads van alleen-lezen query's te verdelen)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als onderdeel van de [architectuur met hoge beschikbaarheid](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)wordt elke database in de servicelaag Premium en Bedrijfskritisch automatisch ingericht met een primaire replica en verschillende secundaire replica's. De secundaire replica's zijn ingericht met dezelfde rekengrootte als de primaire replica. Met de functie **Uitgelezen uitlezen** u sql-database-alleen-lezen workloads laden met behulp van de capaciteit van een van de alleen-lezen replica's in plaats van de voorleesschrijfreplica te delen. Op deze manier wordt de alleen-lezen workload gescheiden van de belangrijkste workload voor lezen/schrijven en worden de prestaties van deze workload niet beïnvloed. De functie is bedoeld voor de toepassingen die logisch gescheiden alleen-lezen workloads bevatten, zoals analytics. In de servicelagen Premium en Business Critical kunnen toepassingen prestatievoordelen behalen met deze extra capaciteit zonder extra kosten.

De functie **Uitgelezen** is ook beschikbaar in de servicelaag Hyperscale wanneer ten minste één secundaire replica wordt gemaakt. Meerdere secundaire replica's kunnen worden gebruikt als alleen-lezen workloads meer resources vereisen dan beschikbaar is op één secundaire replica. De serviceniveaus voor hoge beschikbaarheid van basic-, standaard- en algemene gebruiksklassen bevat geen replica's. De functie **Uitgelezen** is niet beschikbaar in deze servicelagen.

Het volgende diagram illustreert het met behulp van een Business Critical-database.

![Alleen-lezen replica's](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

De functie Uitlezen is standaard ingeschakeld in nieuwe Premium-, Business Critical- en Hyperscale-databases. Voor Hyperscale wordt standaard één secundaire replica gemaakt voor nieuwe databases. Als uw SQL-verbindingstekenreeks `ApplicationIntent=ReadOnly`is geconfigureerd met , wordt de toepassing door de gateway doorgestuurd naar een alleen-lezen replica van die database. Zie [Toepassingsintentie opgeven](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent) `ApplicationIntent` voor informatie over het gebruik van de eigenschap.

Als u ervoor wilt zorgen dat de toepassing verbinding `ApplicationIntent` maakt met de primaire replica, ongeacht de instelling in de SQL-verbindingstekenreeks, moet u de uitgelezen uitschaing expliciet uitschakelen bij het maken van de database of bij het wijzigen van de configuratie. Als u bijvoorbeeld uw database upgradet van de laag Standard of General Purpose naar de laag Premium, Business Critical of Hyperscale en ervoor wilt zorgen dat al uw verbindingen naar de primaire replica blijven gaan, schakelt u Uitgelezen uitschaing uit. Zie [Uitlezen uitschalen inschakelen en uitschakelen](#enable-and-disable-read-scale-out)voor meer informatie over het uitschakelen ervan.

> [!NOTE]
> Query Data Store, Extended Events en SQL Profiler-functies worden niet ondersteund op de alleen-lezen replica's.

## <a name="data-consistency"></a>Gegevensconsistentie

Een van de voordelen van replica's is dat de replica's zijn altijd in de transactioneel consistente staat, maar op verschillende momenten in de tijd kan er een kleine latentie tussen de verschillende replica's. Read Scale-Out ondersteunt consistentie op sessieniveau. Dit betekent dat als de alleen-lezen sessie opnieuw verbinding maakt na een verbindingsfout veroorzaakt door de onbeschikbaarheid van replica's, deze kan worden doorgestuurd naar een replica die niet 100% up-to-date is met de lees-schrijfreplica. Evenzo, als een toepassing schrijft gegevens met behulp van een read-write sessie en onmiddellijk leest met behulp van een alleen-lezen sessie, is het mogelijk dat de laatste updates niet onmiddellijk zichtbaar zijn op de replica. De latentie wordt veroorzaakt door een asynchrone transactielogboekredobewerking.

> [!NOTE]
> Replicatielatentie binnen de regio zijn laag en deze situatie is zeldzaam.

## <a name="connect-to-a-read-only-replica"></a>Verbinding maken met een alleen-lezen replica

Wanneer u Uitgelezen voor een database `ApplicationIntent` inschakelt, bepaalt de optie in de verbindingstekenreeks die door de client wordt verstrekt of de verbinding wordt doorgestuurd naar de schrijfreplica of naar een alleen-lezen replica. Als de `ApplicationIntent` waarde `ReadWrite` (de standaardwaarde) is, wordt de verbinding specifiek naar de lees-schrijfreplica van de database geleid. Dit is identiek aan bestaand gedrag. Als `ApplicationIntent` de `ReadOnly`waarde is, wordt de verbinding doorgestuurd naar een alleen-lezen replica.

De volgende verbindingstekenreeks verbindt de client bijvoorbeeld met een alleen-lezen replica (de items in de hoekhaakjes vervangen door de juiste waarden voor uw omgeving en het laten vallen van de hoekhaakjes):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Een van de volgende verbindingstekenreeksen verbindt de client met een lees-schrijfreplica (het vervangen van de items in de hoekhaakjes door de juiste waarden voor uw omgeving en het laten vallen van de hoekhaakjes):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Controleren of een verbinding is met een alleen-lezen replica

U controleren of u bent verbonden met een alleen-lezen replica door de volgende query uit te voeren. Het zal terugkeren READ_ONLY wanneer aangesloten op een alleen-lezen replica.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Op een gegeven moment is slechts één van de AlwaysON replica's toegankelijk voor de ReadOnly sessies.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Alleen-lezen replica's controleren en oplossen

Wanneer u verbinding maakt met een alleen-lezen replica, hebt u toegang tot de prestatiestatistieken via de `sys.dm_db_resource_stats` DMV. Als u toegang wilt `sys.dm_exec_query_stats`krijgen `sys.dm_exec_query_plan` `sys.dm_exec_sql_text` tot queryplanstatistieken, gebruikt u de - en DMVs.

> [!NOTE]
> De DMV `sys.resource_stats` in de logische masterdatabase retourneert CPU-gebruiks- en opslaggegevens van de primaire replica.

## <a name="enable-and-disable-read-scale-out"></a>Leesscale-out in- en uitschakelen

Read Scale-Out is standaard ingeschakeld op premium-, bedrijfskritieke- en hyperschaalservicelagen. Read Scale-Out kan niet worden ingeschakeld in servicelagen voor basisvoorzieningen, standaard of algemeen gebruik. Read Scale-Out wordt automatisch uitgeschakeld in Hyperscale-databases die zijn geconfigureerd met 0 replica's.

U Read Scale-Out in afzonderlijke databases en elastische pooldatabases in premium- of bedrijfskritieke servicelaag uitschakelen en opnieuw inschakelen met behulp van de volgende methoden.

> [!NOTE]
> De mogelijkheid om Leesscale-out uit te schakelen is bedoeld voor achterwaartse compatibiliteit.

### <a name="azure-portal"></a>Azure Portal

U de instelling Uitlezen beheren op het databaseblad **configureren.**

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

Voor het beheren van Read Scale-Out in Azure PowerShell is de Azure PowerShell-release van december 2016 vereist of nieuwer. Zie [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)voor de nieuwste PowerShell-release.

U Read Scale-Out in Azure PowerShell uitschakelen of opnieuw inschakelen door een beroep te doen `Enabled` `Disabled` op de `-ReadScale` [cmdlet Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) en de gewenste waarde - of - door te geven voor de parameter.

De uitgelezen uitschaing van een bestaande database uitschakelen (de items in de hoekhaakjes vervangen door de juiste waarden voor uw omgeving en de hoekhaakjes laten vallen):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

De uitgelezen uitschalen in een nieuwe database uitschakelen (de items in de hoekhaakjes vervangen door de juiste waarden voor uw omgeving en de hoekhaakjes laten vallen):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Lees-out in een bestaande database opnieuw inschakelen (de items in de hoekhaakjes vervangen door de juiste waarden voor uw omgeving en de hoekhaakjes laten vallen):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Als u een database wilt maken met uitgeschakelde uitgelezen uitschaalsal of om `readScale` de `Enabled` instelling `Disabled` voor een bestaande database te wijzigen, gebruikt u de volgende methode met de eigenschap die is ingesteld op of zoals in de onderstaande voorbeeldaanvraag.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Zie [Databases maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)voor meer informatie.

## <a name="using-tempdb-on-read-only-replica"></a>TempDB gebruiken op alleen-lezen replica

De TempDB-database wordt niet gerepliceerd naar de alleen-lezen replica's. Elke replica heeft zijn eigen versie van tempdb-database die wordt gemaakt wanneer de replica wordt gemaakt. Het zorgt ervoor dat TempDB kan worden bijgewerkt en kan worden gewijzigd tijdens uw query-uitvoering. Als uw alleen-lezen werkbelasting afhankelijk is van het gebruik van TempDB-objecten, moet u deze objecten maken als onderdeel van uw queryscript.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Uitgelezen met geo-gerepliceerde databases gebruiken

Als u Read Scale-Out gebruikt om alleen-lezen-only-workloads te laden op een database die geo-gerepliceerd is (bijvoorbeeld als lid van een failovergroep), moet u ervoor zorgen dat leesscale-out is ingeschakeld op zowel de primaire als de geo-gerepliceerde secundaire databases. Deze configuratie zorgt ervoor dat dezelfde load-balancing-ervaring wordt voortgezet wanneer uw toepassing verbinding maakt met de nieuwe primaire na failover. Als u verbinding maakt met de geo-gerepliceerde secundaire database `ApplicationIntent=ReadOnly` met read-scale ingeschakeld, worden uw sessies met zal worden doorgestuurd naar een van de replica's op dezelfde manier dat we route verbindingen op de primaire database.  De sessies `ApplicationIntent=ReadOnly` zonder worden doorgestuurd naar de primaire replica van de geo-gerepliceerde secundaire, die ook alleen-lezen. Omdat geogerepliceerde secundaire database een ander eindpunt heeft dan de primaire database, was `ApplicationIntent=ReadOnly`het historisch gezien niet nodig om toegang te krijgen tot het secundaire. Om achterwaartse compatibiliteit `sys.geo_replication_links` te `secondary_allow_connections=2` garanderen, toont DMV (elke clientverbinding is toegestaan).

> [!NOTE]
> Round-robin of een andere load-balanced routing tussen de lokale replica's van de secundaire database wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Zie [Hyperscale-servicelaag voor](./sql-database-service-tier-hyperscale.md)informatie over sql database hyperscale-aanbieding .
