---
title: Query's voor replica's lezen
description: Azure SQL Database biedt de mogelijkheid om alleen-lezen-workloads te verdelen met behulp van de capaciteit van alleen-lezen replica's, die read scale-out wordt genoemd.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
titleSuffix: Azure SQL Database & SQL Managed Instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: a043eb5eed8f5554e42a113a3d7963be94da8a49
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985564"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Use read-only replicas to load-balance read-only query workloads (Alleen-lezen replica's gebruiken om de workloads van alleen-lezen query's te verdelen)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als onderdeel van de [architectuur met hoge Beschik baarheid](high-availability-sla.md#premium-and-business-critical-service-tier-availability)wordt elke data base in de service lagen Premium en bedrijfskritiek automatisch ingericht met een primaire replica en verschillende secundaire replica's. De secundaire replica's worden ingericht met dezelfde reken grootte als de primaire replica. Met de functie *scale-out lezen* kunt u taken verdelen SQL database alleen-lezen workloads met de capaciteit van een van de alleen-lezen replica's in plaats van de replica lezen-schrijven te delen. Op deze manier wordt de alleen-lezen workload geïsoleerd van de belangrijkste workload voor lezen en schrijven. Dit heeft geen invloed op de prestaties. De functie is bedoeld voor de toepassingen die logisch gescheiden alleen-lezen werk belastingen bevatten, zoals analyses. In de service lagen Premium en Bedrijfskritiek kunnen toepassingen prestatie voordelen krijgen met behulp van deze extra capaciteit zonder extra kosten.

De functie scale-out lezen is ook beschikbaar in de grootschalige, wanneer ten minste één secundaire replica is gemaakt. Meerdere secundaire replica's kunnen worden gebruikt als alleen-lezen workloads meer bronnen nodig hebben dan beschikbaar zijn op één secundaire replica. De architectuur met hoge Beschik baarheid van de service lagen Basic, Standard en Algemeen bevat geen replica's. De functie scale-out lezen is niet beschikbaar in deze service lagen.

Het volgende diagram illustreert het met behulp van een Bedrijfskritiek-data base.

![Alleen-lezen replica's](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

De functie voor het lezen van scale-out is standaard ingeschakeld voor nieuwe Premium-, Bedrijfskritiek-en grootschalige-data bases. Voor grootschalige wordt standaard één secundaire replica gemaakt voor nieuwe data bases. Als uw SQL-connection string is geconfigureerd met `ApplicationIntent=ReadOnly` , wordt de toepassing omgeleid door de gateway naar een alleen-lezen replica van die data base. Zie voor meer informatie over het gebruik van de `ApplicationIntent` eigenschap [toepassings intentie opgeven](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Als u er zeker van wilt zijn dat de toepassing verbinding maakt met de primaire replica, ongeacht de `ApplicationIntent` instelling in de SQL-Connection String, moet u het lezen van uitschalen expliciet uitschakelen bij het maken van de data base of bij het wijzigen van de configuratie. Als u bijvoorbeeld uw data base bijwerkt van de standaard-of Algemeen laag naar de Premium-, Bedrijfskritiek-of grootschalige-laag en u ervoor wilt zorgen dat alle verbindingen naar de primaire replica blijven, schakelt u lezen uitschalen uit. Zie voor meer informatie over het uitschakelen van de functie [uitschalen voor lezen in-en uitschakelen](#enable-and-disable-read-scale-out).

> [!NOTE]
> De functies query gegevens opslag, uitgebreide gebeurtenissen en SQL Profiler worden niet ondersteund voor de alleen-lezen replica's.

## <a name="data-consistency"></a>Gegevensconsistentie

Een van de voor delen van replica's is dat de replica's altijd in de transactionele consistente status zijn, maar op verschillende tijdstippen kan er sprake zijn van een kleine latentie tussen verschillende replica's. Uitschaal baarheid lezen ondersteunt consistentie op sessie niveau. Als de alleen-lezen sessie opnieuw verbinding maakt nadat een verbindings fout is veroorzaakt doordat de replica niet beschikbaar is, kan deze worden omgeleid naar een replica die niet 100% up-to-date is met de replica lezen-schrijven. Als een toepassing gegevens schrijft met behulp van een lees-en schrijf sessie en deze onmiddellijk leest met behulp van een alleen-lezen sessie, is het mogelijk dat de meest recente updates niet direct zichtbaar zijn op de replica. De latentie wordt veroorzaakt door een bewerking voor opnieuw uitvoeren van een asynchroon transactie logboek.

> [!NOTE]
> Replicatie latenties in de regio zijn laag en deze situatie is zeldzaam.

## <a name="connect-to-a-read-only-replica"></a>Verbinding maken met een alleen-lezen replica

Wanneer u uitschalen voor een Data Base inschakelt, `ApplicationIntent` bepaalt de optie in de Connection String van de client of de verbinding wordt doorgestuurd naar de schrijf replica of naar een alleen-lezen replica. Met name als de `ApplicationIntent` waarde is `ReadWrite` (de standaard waarde), wordt de verbinding omgeleid naar de replica voor lezen/schrijven van de data base. Dit is identiek aan het bestaande gedrag. Als de `ApplicationIntent` waarde is `ReadOnly` , wordt de verbinding doorgestuurd naar een alleen-lezen replica.

De volgende connection string de client bijvoorbeeld verbindt met een alleen-lezen replica (waarbij de items in de punt haken worden vervangen door de juiste waarden voor uw omgeving en de punt haken worden neergezet):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Een van de volgende verbindings reeksen verbindt de client met een replica met het kenmerk lezen/schrijven (waarbij de items in de punt haken worden vervangen door de juiste waarden voor uw omgeving en de punt haken worden neergezet):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Controleren of een verbinding een alleen-lezen replica is

U kunt controleren of u verbonden bent met een alleen-lezen replica door de volgende query uit te voeren. Er wordt READ_ONLY geretourneerd wanneer u bent verbonden met een alleen-lezen replica.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Op elk gewenst moment is slechts één van de AlwaysOn-replica's toegankelijk via de alleen-lezen sessies.

## <a name="monitor-and-troubleshoot-a-read-only-replica"></a>Een alleen-lezen replica controleren en problemen oplossen

Wanneer u bent verbonden met een alleen-lezen replica, hebt u toegang tot de prestatie gegevens met behulp van de `sys.dm_db_resource_stats` dmv. Gebruik de en dmv's om de statistieken van het query plan te openen `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` `sys.dm_exec_sql_text` .

> [!NOTE]
> De DMV `sys.resource_stats` in de logische hoofd database retourneert het CPU-gebruik en de opslag gegevens van de primaire replica.

## <a name="enable-and-disable-read-scale-out"></a>Uitschalen voor lezen in-en uitschakelen

Uitschalen voor lezen is standaard ingeschakeld voor Premium-, Bedrijfskritiek-en grootschalige-service lagen. Uitschalen van Lees bewerkingen kan niet worden ingeschakeld in service lagen Basic, Standard of Algemeen. Uitschalen voor lezen wordt automatisch uitgeschakeld voor grootschalige-data bases die zijn geconfigureerd met nul replica's.

Met de volgende methoden kunt u het lezen van scale-out uitschakelen voor afzonderlijke data bases en elastische pool databases in de service tier Premium of Bedrijfskritiek.

> [!NOTE]
> De mogelijkheid om Lees scale-out uit te scha kelen, wordt geboden voor achterwaartse compatibiliteit.

### <a name="azure-portal"></a>Azure Portal

U kunt de instelling Lees scale-out beheren op de Blade Data Base **configureren** .

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De Azure Resource Manager-module blijft oplossingen voor problemen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de Azure Resource Manager-modules zijn in wezen identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

Voor het beheren van Read scale-out in Azure PowerShell is de Azure PowerShell release van december 2016 of hoger vereist. Zie [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)voor de nieuwste Power shell-versie.

U kunt het lezen van uitschalen in Azure PowerShell uitschakelen of opnieuw inschakelen door de cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) aan te roepen en de gewenste waarde ( `Enabled` of `Disabled` ) voor de para meter door te geven `-ReadScale` .

Als u uitschalen wilt uitschakelen voor een bestaande data base (door de items in de punt haken te vervangen door de juiste waarden voor uw omgeving en de punt haken neer te zetten):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

U kunt uitschalen uitschakelen voor een nieuwe data base (door de items in de punt haken te vervangen door de juiste waarden voor uw omgeving en de punt haken neer te zetten):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Als u het lezen van uitschalen voor een bestaande data base opnieuw wilt inschakelen (Vervang de items in de punt haken door de juiste waarden voor uw omgeving en zet u de punt haken neer):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST-API

Als u een Data Base met lees uitschalen wilt maken of de instelling voor een bestaande Data Base wilt wijzigen, gebruikt u de volgende methode waarbij de `readScale` eigenschap is ingesteld op `Enabled` of `Disabled` , zoals in de volgende voorbeeld aanvraag.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Zie [data bases-maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)voor meer informatie.

## <a name="using-tempdb-on-a-read-only-replica"></a>TempDB gebruiken op een alleen-lezen replica

De TempDB-data base wordt niet gerepliceerd naar de alleen-lezen replica's. Elke replica heeft een eigen versie van de TempDB-data base die wordt gemaakt wanneer de replica wordt gemaakt. Het zorgt ervoor dat TempDB kan worden bijgewerkt en gewijzigd tijdens het uitvoeren van de query. Als uw alleen-lezen werk belasting afhankelijk is van het gebruik van TempDB-objecten, moet u deze objecten maken als onderdeel van uw query script.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Lezen van scale-out gebruiken met geo-gerepliceerde data bases

Als u lezen scale-out gebruikt voor het verdelen van alleen-lezen werk belastingen op een Data Base die geografisch wordt gerepliceerd (bijvoorbeeld als lid van een failovergroep), moet u ervoor zorgen dat uitschalen voor lezen is ingeschakeld op zowel de primaire als de geo-gerepliceerde secundaire data base. Deze configuratie zorgt ervoor dat dezelfde taak verdeling blijft werken wanneer uw toepassing verbinding maakt met de nieuwe primaire na een failover. 

Als u verbinding maakt met de secundaire data base met geo-replicatie en alleen-schalen is ingeschakeld, worden uw sessies met `ApplicationIntent=ReadOnly` een van de replica's op dezelfde manier doorgestuurd als de verbindingen op de primaire data base.  De sessies zonder `ApplicationIntent=ReadOnly` worden doorgestuurd naar de primaire replica van het secundaire geo-gerepliceerde secundair, wat ook alleen-lezen is. Omdat de secundaire data base met geo-replicatie een ander eind punt heeft dan de primaire Data Base `ApplicationIntent=ReadOnly` . `sys.geo_replication_links`Dmv laat zien of `secondary_allow_connections=2` een client verbinding is toegestaan om achterwaartse compatibiliteit te garanderen.

> [!NOTE]
> Round Robin of andere route ring met gelijke taak verdeling tussen de lokale replica's van de secundaire data base wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Zie [grootschalige service tier](service-tier-hyperscale.md)(Engelstalig) voor meer informatie over de SQL database grootschalige-aanbieding.
