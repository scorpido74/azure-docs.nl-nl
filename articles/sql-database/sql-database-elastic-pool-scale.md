---
title: Resources voor elastische pools schalen
description: Op deze pagina worden schaalresources voor elastische groepen in Azure SQL Database beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462595"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Elastische poolresources schalen in Azure SQL Database

In dit artikel wordt beschreven hoe u de beschikbare reken- en opslagbronnen schalen voor elastische pools en samengevoegde databases in Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Compute resources (vCores of DTU's) wijzigen

Nadat u in eerste instantie het aantal vCores of eDTU's hebt gekozen, u een elastische pool dynamisch omhoog of omlaag schalen op basis van de werkelijke ervaring met behulp van de [Azure-portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)de [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)of de [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impact van het wijzigen van de servicelaag of het opnieuw schalen van de rekengrootte

Het wijzigen van de servicelaag of rekengrootte van een elastische groep volgt een vergelijkbaar patroon als voor afzonderlijke databases en houdt voornamelijk in dat de service de volgende stappen uitvoert:

1. Nieuwe rekeninstantie maken voor de elastische groep  

    Er wordt een nieuwe rekeninstantie voor de elastische pool gemaakt met de gevraagde servicelaag en rekengrootte. Voor sommige combinaties van wijzigingen in de servicelaag en de rekengrootte moet een replica van elke database worden gemaakt in de nieuwe rekeninstantie, waarbij gegevens worden gekopieerd en de algehele latentie sterk kan worden beïnvloed. Hoe dan ook, de databases blijven online tijdens deze stap en verbindingen blijven worden gericht op de databases in de oorspronkelijke rekeninstantie.

2. Routering van verbindingen naar nieuwe rekeninstantie overschakelen

    Bestaande verbindingen met de databases in de oorspronkelijke rekeninstantie worden verwijderd. Eventuele nieuwe verbindingen worden tot stand gebracht met de databases in de nieuwe rekeninstantie. Voor sommige combinaties van servicelagen en wijzigingen in de rekengrootte worden databasebestanden tijdens de switch losgemaakt en opnieuw gekoppeld.  Hoe dan ook, de schakelaar kan resulteren in een korte onderbreking van de service wanneer databases over het algemeen minder dan 30 seconden en vaak slechts enkele seconden niet beschikbaar zijn. Als er langlopende transacties worden uitgevoerd wanneer verbindingen worden verwijderd, kan de duur van deze stap langer duren om afgebroken transacties te herstellen. [Versneld databaseherstel](sql-database-accelerated-database-recovery.md) kan de impact van het afbreken van langlopende transacties verminderen.

> [!IMPORTANT]
> Er gaan geen gegevens verloren tijdens een stap in de workflow.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latentie van het wijzigen van de servicelaag of het opnieuw schalen van de rekengrootte

De geschatte latentie om de servicelaag te wijzigen of de rekengrootte van één database of elastische groep te wijzigen, wordt als volgt geparameteriseerd:

|Servicelaag|Basisdatabase,</br>Standaard (S0-S1)|Basis elastisch zwembad,</br>Standaard (S2-S12), </br>Hyperschaal, </br>Single Database of elastic pool voor algemeen gebruik|Premium of Business Critical enkele database of elastische pool|
|:---|:---|:---|:---|
|**Basisdatabase,</br> Standaard (S0-S1)**|&bull;&nbsp;Constante tijdlatentie onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Meestal minder dan 5 minuten|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|
|**Basiselastische </br>pool, Standaard (S2-S12), </br>Hyperscale, </br>Algemene Doel enkele database of elastische pool**|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Constante tijdlatentie onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Meestal minder dan 5 minuten|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|
|**Premium of Business Critical enkele database of elastische pool**|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|&bull;&nbsp;Latentie evenredig aan databaseruimte die wordt gebruikt vanwege het kopiëren van gegevens</br>&bull;&nbsp;Meestal minder dan 1 minuut per GB aan gebruikte ruimte|

> [!NOTE]
>
> - In het geval van het wijzigen van de servicelaag of het opnieuw schalen van gegevens voor een elastische groep, moet de optelling van ruimte die wordt gebruikt voor alle databases in de groep worden gebruikt om de schatting te berekenen.
> - In het geval van het verplaatsen van een database van/naar een elastische groep, heeft alleen de ruimte die door de database wordt gebruikt, invloed op de latentie, niet op de ruimte die wordt gebruikt door de elastische pool.
>
> [!TIP]
> Zie Voor het monitoren van lopende bewerkingen: [Bewerkingen beheren met de SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), Operations beheren met [CLI,](/cli/azure/sql/db/op) [Monitor operations met Behulp van T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) en deze twee PowerShell-opdrachten: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) en [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Aanvullende overwegingen bij het wijzigen van de servicelaag of het opnieuw schalen van de rekengrootte

- Bij het verkleinen van vCores of eDTU's voor een elastische pool moet de gebruikte ruimte kleiner zijn dan de maximaal toegestane grootte van de doelservicelaag en eDTU's.
- Bij het opnieuw schalen van eDTU's voor een elastische groep gelden extra opslagkosten als (1) de maximale opslaggrootte van de groep wordt ondersteund door de doelgroep en (2) de maximale opslaggrootte groter is dan de meegeleverde opslaghoeveelheid van de doelgroep. Als een 100 eDTU-standaardpool met een maximale grootte van 100 GB bijvoorbeeld is ingekrompen tot een 50 eDTU-standaardgroep, zijn er extra opslagkosten van toepassing omdat doelgroep een maximale grootte van 100 GB ondersteunt en de meegeleverde opslagruimte slechts 50 GB bedraagt. De extra opslagruimte is dus 100 GB – 50 GB = 50 GB. Zie [SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor de prijzen van extra opslag. Als de werkelijke hoeveelheid gebruikte ruimte lager is dan het meegeleverde opslagbedrag, kunnen deze extra kosten worden vermeden door de maximale grootte van de database te verkleinen tot het inbegrepen bedrag.

### <a name="billing-during-rescaling"></a>Facturering tijdens herschalen

Er worden kosten in rekening gebracht voor elk uur dat een database bestaat met behulp van de hoogste servicelaag + rekengrootte die tijdens dat uur is toegepast, ongeacht het gebruik of of de database minder dan een uur actief was. Als u bijvoorbeeld één database maakt en deze vijf minuten later verwijdert, wordt in uw factuur een toeslag voor één databaseuur weergegeven.

## <a name="change-elastic-pool-storage-size"></a>De opslaggrootte van elastische groep wijzigen

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

### <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

- Opslag kan worden ingericht tot de maximale groottelimiet:

  - Voor opslag in de servicelagen voor standaard of algemeen gebruik u de grootte in stappen van 10 GB vergroten of verkleinen
  - Voor opslag in de premium- of bedrijfskritieke servicelagen u de grootte in stappen van 250 GB vergroten of verkleinen
- Opslag voor een elastische pool kan worden ingericht door het verhogen of verkleinen van de maximale grootte.
- De prijs van opslag voor een elastische groep is de opslaghoeveelheid vermenigvuldigd met de prijs van de opslageenheid van de servicelaag. Zie [SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag.

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

### <a name="dtu-based-purchasing-model"></a>DTU-gebaseerd inkoopmodel

- De eDTU-prijs voor een elastische pool omvat een bepaalde hoeveelheid opslagruimte zonder extra kosten. Extra opslag buiten het meegeleverde bedrag kan worden ingericht voor extra kosten tot de maximale groottelimiet in stappen van 250 GB tot 1 TB, en vervolgens in stappen van 256 GB na 1 TB. Zie [Elastische groep: opslaggroottes en rekengroottes](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)voor inbegrepen opslagbedragen en maximale groottes.
- Extra opslag voor een elastische groep kan worden ingericht door de maximale grootte te vergroten met behulp van de [Azure-portal,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)de [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)of de [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- De prijs van extra opslag voor een elastischzwembad is de extra opslagruimte vermenigvuldigd met de extra opslageenheidsprijs van de servicelaag. Zie [SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag.

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie SQL Database [vCore-gebaseerde resourcelimieten voor](sql-database-vcore-resource-limits-elastic-pools.md) algemene resourcelimieten - elastische pools en [SQL Database DTU-gebaseerde resourcelimieten - elastische pools](sql-database-dtu-resource-limits-elastic-pools.md).
