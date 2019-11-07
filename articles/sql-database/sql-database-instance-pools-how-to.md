---
title: Instructies voor de Azure SQL Database-instantie groepen (preview-versie)
description: In dit artikel wordt beschreven hoe u Azure SQL Database-instantie groepen maakt en beheert (preview).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 461de4da35905ed860fb2ed9d2c655b55b777683
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689733"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Instructies voor Azure SQL Database-instantie groepen (preview)

In dit artikel vindt u informatie over het maken en beheren van [instantie groepen](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Bewerkingen voor exemplaar groepen

In de volgende tabel ziet u de beschik bare bewerkingen met betrekking tot exemplaar groepen en hun Beschik baarheid in de Azure Portal en Power shell.

|Opdracht|Azure Portal|PowerShell|
|:---|:---|:---|
|Instantie-pool maken|Nee|Ja|
|Exemplaar groep bijwerken (beperkt aantal eigenschappen)|Nee |Ja |
|Gebruik en eigenschappen van exemplaar groep controleren|Nee|Ja |
|Exemplaar groep verwijderen|Nee|Ja|
|Een beheerd exemplaar in een exemplaar groep maken|Nee|Ja|
|Resource gebruik van beheerd exemplaar bijwerken|Ja |Ja|
|Gebruik en eigenschappen van beheerd exemplaar controleren|Ja|Ja|
|Een beheerd exemplaar uit de groep verwijderen|Ja|Ja|
|Een Data Base maken in een beheerd exemplaar dat in de groep is geplaatst|Ja|Ja|
|Een Data Base verwijderen uit een beheerd exemplaar|Ja|Ja|

Beschik bare [Power shell-opdrachten](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |Beschrijving |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Hiermee maakt u een Azure SQL Database exemplaar groep. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Retourneert informatie over de Azure SQL-exemplaar groep. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Hiermee stelt u eigenschappen in voor een Azure SQL Database exemplaar groep. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Hiermee verwijdert u een Azure SQL Database exemplaar groep. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Retourneert informatie over het gebruik van Azure SQL-exemplaar groepen. |


Als u Power shell wilt gebruiken, [installeert u de nieuwste versie van Power shell core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)en volgt u de instructies om [de Azure PowerShell-module te installeren](https://docs.microsoft.com/powershell/azure/install-az-ps).

Voor bewerkingen met betrekking tot instanties in Pools en afzonderlijke instanties gebruikt u de standaard [Managed instance-opdrachten](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), maar de eigenschap name van de *instantie groep* moet worden ingevuld wanneer u deze opdrachten gebruikt voor een instantie in een groep.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Beheerde instanties in Pools implementeren

Het proces voor het implementeren van een exemplaar in een groep bestaat uit de volgende twee stappen:

1. Eenmalige implementatie van exemplaar groep. Dit is een langlopende bewerking waarbij de duur hetzelfde is als de implementatie [van één exemplaar dat is gemaakt in een leeg subnet](sql-database-managed-instance.md#managed-instance-management-operations).

2. Herhaalde implementatie van een exemplaar in een exemplaar groep. De para meter voor de exemplaar groep moet expliciet worden opgegeven als onderdeel van deze bewerking. Dit is een relatief snelle bewerking die doorgaans vijf minuten in beslag neemt.

In de open bare preview worden beide stappen alleen ondersteund met Power shell en Resource Manager-sjablonen. De Azure Portal-ervaring is momenteel niet beschikbaar.

Nadat een beheerd exemplaar is geïmplementeerd in een pool, *kunt* u de Azure Portal gebruiken om de eigenschappen ervan te wijzigen op de pagina prijs categorie.


## <a name="create-an-instance-pool"></a>Een exemplaar groep maken

Een exemplaar groep maken:

1. [Maak een virtueel netwerk met een subnet](#create-a-virtual-network-with-a-subnet).
2. [Maak een exemplaar groep](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Een virtueel netwerk met een subnet maken 

Als u meerdere instantie groepen binnen hetzelfde virtuele netwerk wilt plaatsen, raadpleegt u de volgende artikelen:

- [De grootte van het VNet-subnet bepalen voor een beheerde Azure SQL database-instantie](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Maak een nieuw virtueel netwerk en subnet met behulp van de [Azure Portal sjabloon](sql-database-managed-instance-create-vnet-subnet.md) of volg de instructies voor [het voorbereiden van een bestaand virtueel netwerk](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Een exemplaar groep maken 

Nadat u de voor gaande stappen hebt voltooid, bent u klaar om een exemplaar groep te maken.

De volgende beperkingen zijn van toepassing op exemplaar groepen:

- Alleen Algemeen en GEN5 zijn beschikbaar als open bare preview.
- De groeps naam mag alleen kleine letters, cijfers en afbreek streepjes bevatten en mag niet beginnen met een koppel teken.
- Gebruik `Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork`om de subnet-ID op te halen.
- Als u AHB (Azure Hybrid Benefit) wilt gebruiken, wordt dit toegepast op het niveau van de instantie groep. U kunt het licentie type instellen tijdens het maken van een groep of deze op elk gewenst moment bijwerken na het maken.

> [!IMPORTANT]
> Het implementeren van een exemplaar groep is een langlopende bewerking die ongeveer 4,5 uur in beslag neemt.

Een exemplaar groep maken:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId "/subscriptions/subscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/miPoolVirtualNetwork/subnets/miPoolSubnet" `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Omdat het implementeren van een exemplaar groep een langlopende bewerking is, moet u wachten totdat deze is voltooid voordat u een van de volgende stappen in dit artikel uitvoert.

## <a name="create-a-managed-instance-inside-the-pool"></a>Een beheerd exemplaar maken in de groep 

Na de geslaagde implementatie van de exemplaar groep is het tijd om een instantie te maken.

Als u een beheerd exemplaar wilt maken, voert u de volgende opdracht uit:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Het implementeren van een exemplaar binnen een groep duurt enkele minuten. Nadat de eerste instantie is gemaakt, kunnen er extra instanties worden gemaakt:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Een Data Base binnen een exemplaar maken 

Voor het maken en beheren van data bases in een beheerd exemplaar dat zich in een groep bevindt, gebruikt u de opdrachten voor één exemplaar.

Een Data Base maken in een beheerd exemplaar:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Gebruik van exemplaar groep ophalen 
 
Een lijst met exemplaren in een groep ophalen:

```powershell
$instancePool | Get-AzSqlInstance
```


Resource gebruik van de groep ophalen:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Voor een gedetailleerd gebruiks overzicht van de groep en instanties hierin:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Een lijst met de data bases in een exemplaar:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Er geldt een limiet van 100 data bases per pool (niet per instantie).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Een beheerd exemplaar binnen een groep schalen 


Nadat u een beheerd exemplaar hebt gevuld met data bases, kunt u de instantie limieten voor opslag of prestaties aanraken. Als het gebruik van de groep niet is overschreden, kunt u in dat geval uw exemplaar schalen.
Het schalen van een beheerd exemplaar binnen een pool is een bewerking die een paar minuten in beslag neemt. De vereiste voor schalen is beschikbaar vCores en opslag op het niveau van de instantie groep.

Het aantal vCores en de opslag grootte bijwerken:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Alleen opslag grootte bijwerken:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Verbinding maken met een beheerd exemplaar binnen een groep

De volgende twee stappen zijn vereist om verbinding te maken met een beheerd exemplaar in een groep:

1. [Schakel het open bare eind punt in voor het exemplaar](#enable-the-public-endpoint-for-the-instance).
2. [Voeg een regel voor binnenkomende verbindingen toe aan de netwerk beveiligings groep (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Nadat beide stappen zijn voltooid, kunt u verbinding maken met het exemplaar met behulp van een openbaar eindpunt adres, poort en referenties die zijn gegeven tijdens het maken van het exemplaar. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Het open bare eind punt inschakelen voor het exemplaar

Het inschakelen van het open bare eind punt voor een exemplaar kan worden uitgevoerd via de Azure Portal of met behulp van de volgende Power shell-opdracht:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Deze para meter kan ook worden ingesteld tijdens het maken van een exemplaar.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Een regel voor binnenkomende verbindingen toevoegen aan de netwerk beveiligings groep 

Deze stap kan worden uitgevoerd met behulp van de Azure Portal of met Power shell-opdrachten en kan op elk gewenst moment worden uitgevoerd nadat het subnet voor het beheerde exemplaar is voor bereid.

Zie [verkeer van open bare eind punten toestaan voor de netwerk beveiligings groep](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)voor meer informatie.


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Een bestaande enkele instantie binnen een exemplaar groep verplaatsen 
 
Het verplaatsen van exemplaren in en uit een pool is een van de beperkingen voor de open bare preview. Een tijdelijke oplossing die kan worden gebruikt, is afhankelijk van het tijdstip van het herstellen van data bases van een exemplaar buiten een groep naar een exemplaar dat zich al in een groep bevindt. 

Beide exemplaren moeten zich in hetzelfde abonnement en dezelfde regio bevinden. Het terugzetten van meerdere regio's en meerdere abonnementen wordt momenteel niet ondersteund.

Voor dit proces geldt een periode van uitval tijd.

Bestaande data bases verplaatsen:

1. Werk belastingen voor het beheerde exemplaar dat u migreert, te onderbreken.
2. Genereer scripts om systeem databases te maken en voer deze uit op het exemplaar dat zich in de exemplaar groep bevindt.
3. Een herstel bewerking van elke Data Base naar het exemplaar in de groep uitvoeren.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Ga naar het nieuwe exemplaar van uw toepassing en hervat de workloads.

Als er meerdere data bases zijn, herhaalt u het proces voor elke Data Base.


## <a name="next-steps"></a>Volgende stappen

- Zie [algemene SQL-functies](sql-database-features.md)voor een lijst met functies en vergelijkingen.
- Zie [vnet-configuratie voor beheerde exemplaren](sql-database-managed-instance-connectivity-architecture.md)voor meer informatie over vnet-configuratie.
- Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md)voor een Snelstartgids die een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-upbestand.
- Zie [migratie van beheerde exemplaren met](../dms/tutorial-sql-server-to-managed-instance.md)behulp van DMS voor een zelf studie met behulp van de Azure database MIGRATION service (DMS) voor migratie.
- Zie [Azure SQL database bewaken met behulp van Azure SQL-analyse](../azure-monitor/insights/azure-sql.md)voor geavanceerde bewaking van beheerde exemplaar database prestaties met ingebouwde probleemoplossings informatie.
- Zie [prijzen van beheerde exemplaren SQL database](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.
