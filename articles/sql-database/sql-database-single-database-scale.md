---
title: De schaal van de resources voor één database aanpassen
description: In dit artikel wordt beschreven hoe u de beschikbare reken- en opslagbronnen schalen voor één database in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061601"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Eén databaseresources schalen in Azure SQL Database

In dit artikel wordt beschreven hoe u de reken- en opslagbronnen schalen die beschikbaar zijn voor een Azure SQL-database in de ingerichte compute-laag. Als alternatief biedt de [serverless compute-laag](sql-database-serverless.md) automatische schaling en facturen per seconde voor compute used.

Nadat u in eerste instantie het aantal vCores of DTU's hebt gekozen, u een enkele database dynamisch omhoog of omlaag schalen op basis van de werkelijke ervaring met behulp van de [Azure-portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)de [Azure CLI](/cli/azure/sql/db#az-sql-db-update)of de [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

In de volgende video ziet u dynamisch het wijzigen van de servicelaag en de rekengrootte om de beschikbare DTU's voor één database te vergroten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

## <a name="impact"></a>Impact

Het wijzigen van de servicelaag of rekengrootte van voornamelijk de service die de volgende stappen uitvoert:

1. Nieuwe rekeninstantie voor de database maken  

    Er wordt een nieuwe rekeninstantie gemaakt met de gevraagde servicelaag en rekengrootte. Voor sommige combinaties van wijzigingen in de servicelaag en de rekengrootte moet een replica van de database worden gemaakt in de nieuwe rekeninstantie, waarbij gegevens worden gekopieerd en de algehele latentie sterk kan worden beïnvloed. Hoe dan ook, de database blijft online tijdens deze stap en verbindingen worden nog steeds doorgestuurd naar de database in de oorspronkelijke rekeninstantie.

2. Routering van verbindingen naar nieuwe rekeninstantie overschakelen

    Bestaande verbindingen met de database in de oorspronkelijke rekeninstantie worden verwijderd. Eventuele nieuwe verbindingen worden tot stand gebracht met de database in de nieuwe rekeninstantie. Voor sommige combinaties van servicelagen en wijzigingen in de rekengrootte worden databasebestanden tijdens de switch losgemaakt en opnieuw gekoppeld.  Hoe dan ook, de schakelaar kan resulteren in een korte onderbreking van de service wanneer de database over het algemeen minder dan 30 seconden en vaak slechts enkele seconden niet beschikbaar is. Als er langlopende transacties worden uitgevoerd wanneer verbindingen worden verwijderd, kan de duur van deze stap langer duren om afgebroken transacties te herstellen. [Versneld databaseherstel](sql-database-accelerated-database-recovery.md) kan de impact van het afbreken van langlopende transacties verminderen.

> [!IMPORTANT]
> Er gaan geen gegevens verloren tijdens een stap in de workflow. Zorg ervoor dat u een aantal [logica voor nieuwe try](sql-database-connectivity-issues.md) hebt geïmplementeerd in de toepassingen en componenten die Azure SQL Database gebruiken terwijl de servicelaag wordt gewijzigd.

## <a name="latency"></a>Latentie 

De geschatte latentie om de servicelaag te wijzigen of de rekengrootte van één database of elastische groep te wijzigen, wordt als volgt geparameteriseerd:

|Servicelaag|Basisdatabase,</br>Standaard (S0-S1)|Basis elastisch zwembad,</br>Standaard (S2-S12), </br>Hyperschaal, </br>Single Database of elastic pool voor algemeen gebruik|Premium of Business Critical enkele database of elastische pool|
|:---|:---|:---|:---|
|**Basisdatabase,</br> Standaard (S0-S1)**|&bull;&nbsp;Constante tijdlatentie onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Meestal minder dan 5 minuten|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|
|**Basiselastische </br>pool, Standaard (S2-S12), </br>Hyperscale, </br>Algemene Doel enkele database of elastische pool**|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Constante tijdlatentie onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Meestal minder dan 5 minuten|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|
|**Premium of Business Critical enkele database of elastische pool**|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|

> [!TIP]
> Zie Voor het monitoren van lopende bewerkingen: [Bewerkingen beheren met de SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), Operations beheren met [CLI,](/cli/azure/sql/db/op) [Monitor operations met Behulp van T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) en deze twee PowerShell-opdrachten: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) en [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Wijzigingen annuleren

Een wijziging van de servicelaag of een bewerking voor het opnieuw schalen van gegevens kan worden geannuleerd.

#### <a name="azure-portal"></a>Azure Portal

Navigeer in het **overzichtsblad** van de database naar Meldingen en klik op de tegel die aangeeft dat er een doorlopende bewerking is:

![Lopende operatie](media/sql-database-single-database-scale/ongoing-operations.png)

Klik vervolgens op de knop met het label **Deze bewerking annuleren**.

![Lopende bewerking annuleren](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Stel vanuit een PowerShell-opdrachtprompt de `$resourceGroupName`opdracht , `$serverName`en `$databaseName`, in en voer de volgende opdracht uit:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Aanvullende overwegingen

- Als u een upgrade uitvoert naar een hogere servicelaag of rekengrootte, wordt de maximale grootte van de database niet groter, tenzij u expliciet een groter formaat (maxsize) opgeeft.
- Als u een database wilt downgraden, moet de gebruikte databaseruimte kleiner zijn dan de maximaal toegestane grootte van de doelservicelaag en de rekengrootte.
- Bij het verlagen van **Premium** naar de **standaardlaag** gelden extra opslagkosten als zowel (1) de maximale grootte van de database wordt ondersteund in de doelgrootte en (2) de maximale grootte groter is dan de meegeleverde opslaghoeveelheid van de doelgrootte. Als een P1-database met een maximale grootte van 500 GB bijvoorbeeld is ingekrompen tot S3, zijn er extra opslagkosten van toepassing omdat S3 een maximale grootte van 1 TB ondersteunt en de meegeleverde opslagruimte slechts 250 GB bedraagt. De extra opslagruimte is dus 500 GB – 250 GB = 250 GB. Zie [SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor de prijzen van extra opslag. Als de werkelijke hoeveelheid gebruikte ruimte lager is dan het meegeleverde opslagbedrag, kunnen deze extra kosten worden vermeden door de maximale grootte van de database te verkleinen tot het inbegrepen bedrag.
- Wanneer u een database upgradet met [georeplicatie](sql-database-geo-replication-portal.md) ingeschakeld, u de secundaire databases upgraden naar de gewenste servicelaag en rekengrootte voordat u de primaire database upgradet (algemene richtlijnen voor de beste prestaties). Bij het upgraden naar een andere editie is het een vereiste dat de secundaire database eerst wordt bijgewerkt.
- Wanneer u een database downgraden met [georeplicatie](sql-database-geo-replication-portal.md) ingeschakeld, degradeert u de primaire databases naar de gewenste servicelaag en de rekengrootte voordat u de secundaire database verlaagt (algemene richtlijnen voor de beste prestaties). Bij het downgraden naar een andere editie is het een vereiste dat de primaire database eerst wordt gedegradeerd.
- De mogelijkheden om de service te herstellen verschillen voor de verschillende servicelagen. Als u downgraden naar de **basic-laag,** is er een lagere back-up bewaartermijn. Zie [Azure SQL Database Backups](sql-database-automated-backups.md).
- De nieuwe eigenschappen voor de database worden pas toegepast nadat de wijzigingen zijn voltooid.

## <a name="billing"></a>Billing 

Er worden kosten in rekening gebracht voor elk uur dat een database bestaat met behulp van de hoogste servicelaag + rekengrootte die tijdens dat uur is toegepast, ongeacht het gebruik of of de database minder dan een uur actief was. Als u bijvoorbeeld één database maakt en deze vijf minuten later verwijdert, wordt in uw factuur een toeslag voor één databaseuur weergegeven.

## <a name="change-storage-size"></a>Opslaggrootte wijzigen

### <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

- Opslag kan worden ingericht tot de maximale groottelimiet voor gegevensopslag met stappen van 1 GB. De minimale configureerbare gegevensopslag is 1 GB. Zie documentatiepagina's voor resourcelimiet voor [afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md) en [elastische groepen](sql-database-vcore-resource-limits-elastic-pools.md) voor maximale groottelimieten voor gegevensopslag in elke servicedoelstelling.
- Gegevensopslag voor één database kan worden ingericht door de maximale grootte te vergroten of te verkleinen met behulp van de [Azure-portal](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az-sql-db-update)of [REST API](https://docs.microsoft.com/rest/api/sql/databases/update). Als de maximale groottewaarde is opgegeven in bytes, moet dit een veelvoud van 1 GB (1073741824 bytes) zijn.
- De hoeveelheid gegevens die kan worden opgeslagen in de gegevensbestanden van een database wordt beperkt door de maximale grootte van de geconfigureerde gegevensopslag. Naast die opslag wijst SQL Database automatisch 30% meer opslagruimte toe voor het transactielogboek.
- SQL Database wijst automatisch 32 GB per `tempdb` vCore toe voor de database. `tempdb`bevindt zich op de lokale SSD-opslag in alle servicelagen.
- De prijs van opslag voor één database of een elastische groep is de som van de opslag van gegevensopslag en transactielogboeken vermenigvuldigd met de prijs van de opslageenheid van de servicelaag. De kosten `tempdb` van is inbegrepen in de prijs. Zie [SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de opslagprijs.

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

### <a name="dtu-based-purchasing-model"></a>DTU-gebaseerd inkoopmodel

- De DTU-prijs voor een enkele database omvat een bepaalde hoeveelheid opslagruimte zonder extra kosten. Extra opslag buiten het meegeleverde bedrag kan worden ingericht voor extra kosten tot de maximale groottelimiet in stappen van 250 GB tot 1 TB, en vervolgens in stappen van 256 GB na 1 TB. Zie [Enkele database: Opslaggroottes en rekengroottes](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)voor opgenomen opslagbedragen en maximale groottes.
- Extra opslag voor één database kan worden ingericht door de maximale grootte te vergroten met behulp van de [Azure-portal, Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)de [Azure CLI](/cli/azure/sql/db#az-sql-db-update)of de [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- De prijs van extra opslagruimte voor één database is de extra opslagruimte vermenigvuldigd met de extra opslageenheidsprijs van de servicelaag. Zie [SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag.

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

### <a name="geo-replicated-database"></a>Geo-gerepliceerde database

Als u de databasegrootte van een gerepliceerde secundaire database wilt wijzigen, wijzigt u de grootte van de primaire database. Deze wijziging wordt vervolgens gerepliceerd en geïmplementeerd in de secundaire database. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11- en P15-beperkingen bij maximale grootte groter dan 1 TB

Meer dan 1 TB aan opslag in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regions, and US Government Central. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB. De volgende overwegingen en beperkingen zijn van toepassing op P11- en P15-databases met een maximale grootte van meer dan 1 TB:

- Als de maximale grootte voor een P11- of P15-database ooit is ingesteld op een waarde van meer dan 1 TB, kan deze alleen worden hersteld of gekopieerd naar een P11- of P15-database.  Vervolgens kan de database worden geherschaald naar een andere rekengrootte, mits de hoeveelheid ruimte die is toegewezen op het moment van de herschalingbewerking de maximale groottelimieten van de nieuwe rekengrootte niet overschrijdt.
- Voor actieve scenario's voor georeplicatie:
  - Een georeplicatierelatie instellen: als de primaire database P11 of P15 is, moeten de secundaire(en) ook P11 of P15 zijn; lagere rekengrootte worden afgewezen als secondaries, omdat ze niet in staat zijn meer dan 1 TB te ondersteunen.
  - De primaire database in een georeplicatierelatie upgraden: als u de maximale grootte wijzigt in meer dan 1 TB in een primaire database, wordt dezelfde wijziging in de secundaire database geactiveerd. Beide upgrades moeten succesvol zijn voor de wijziging op de primaire van kracht te worden. Regiobeperkingen voor de optie meer dan 1 TB zijn van toepassing. Als de secundaire zich in een regio bevindt die niet meer dan 1 TB ondersteunt, wordt de primaire niet bijgewerkt.
- Het gebruik van de import/export-service voor het laden van P11/P15-databases met meer dan 1 TB wordt niet ondersteund. Gebruik SqlPackage.exe om gegevens te [importeren](sql-database-import.md) en [exporteren.](sql-database-export.md)

## <a name="next-steps"></a>Volgende stappen

Zie SQL Database [vCore-gebaseerde resourcelimieten voor](sql-database-vcore-resource-limits-single-databases.md) algemene resourcelimieten voor algemene resourcelimieten : afzonderlijke databases en [SQL Database DTU-gebaseerde resourcelimieten - elastische pools](sql-database-dtu-resource-limits-single-databases.md).
