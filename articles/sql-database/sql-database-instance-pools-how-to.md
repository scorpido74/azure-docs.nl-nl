---
title: Handleiding en handleiding van instantiegroepen (voorbeeld)
description: In dit artikel wordt beschreven hoe u Azure SQL Database-instantiegroepen (voorbeeld) maken en beheren.
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
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299359"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>How-to-guide voor Azure SQL Database-instantiegroepen (voorbeeld)

In dit artikel vindt u informatie over het maken en beheren van [instantiegroepen.](sql-database-instance-pools.md)

## <a name="instance-pool-operations"></a>Instantiegroepbewerkingen

In de volgende tabel worden de beschikbare bewerkingen weergegeven met betrekking tot instantiegroepen en de beschikbaarheid ervan in de Azure-portal en PowerShell.

|Opdracht|Azure Portal|PowerShell|
|:---|:---|:---|
|Instantie-pool maken|Nee|Ja|
|Instantiegroep bijwerken (beperkt aantal eigenschappen)|Nee |Ja |
|Gebruik en eigenschappen van instantiesgroep controleren|Nee|Ja |
|Instantiegroep verwijderen|Nee|Ja|
|Beheerde instantie maken in instantiegroep|Nee|Ja|
|Beheerde instantieresourcegebruik bijwerken|Ja |Ja|
|Beheerde instantiegebruik en eigenschappen controleren|Ja|Ja|
|Beheerde instantie uit de groep verwijderen|Ja|Ja|
|Een database maken in beheerde instantie die in de groep is geplaatst|Ja|Ja|
|Een database verwijderen uit beheerde instantie|Ja|Ja|

Beschikbare [PowerShell-opdrachten](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |Beschrijving |
|:---|:---|
|[Nieuw-AzsqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Hiermee maakt u een instantiegroep azure SQL Database. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Retourneert informatie over Azure SQL-instantiegroep. |
|[Set-azsqlinstancepool](/powershell/module/az.sql/set-azsqlinstancepool/) | Hiermee stelt u eigenschappen in voor een Azure SQL Database-instantiegroep. |
|[Groep azsqlinstanceverwijderen](/powershell/module/az.sql/remove-azsqlinstancepool/) | Hiermee verwijdert u een azure SQL Database-instantiegroep. |
|[Get-azsqlinstancepoolusage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Retourneert informatie over het gebruik van Azure SQL-instantiegroep. |


Als u PowerShell wilt gebruiken, [installeert u de nieuwste versie van PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)en volgt u instructies om [de Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/install-az-ps)te installeren.

Voor bewerkingen met betrekking tot instanties, zowel binnen pools als in afzonderlijke instanties, gebruikt u de [standaardbeheerde instantieopdrachten,](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)maar de eigenschap *instantiegroepnaam* moet worden ingevuld wanneer deze opdrachten worden gebruikt voor een instantie in een groep.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Beheerde exemplaren implementeren in pools

Het proces waarbij een instantie in een groep wordt geïmplementeerd, bestaat uit de volgende twee stappen:

1. Eenmalige instantiepoolimplementatie. Dit is een langlopende bewerking, waarbij de duur gelijk is aan het implementeren van [één instantie die is gemaakt in een leeg subnet.](sql-database-managed-instance.md#managed-instance-management-operations)

2. Herhaalde instantie-implementatie in een instantiegroep. De parameter instantiegroep moet expliciet worden opgegeven als onderdeel van deze bewerking. Dit is een relatief snelle bewerking die meestal tot 5 minuten duurt.

In openbare preview worden beide stappen alleen ondersteund met PowerShell- en Resource Manager-sjablonen. De Azure-portalervaring is momenteel niet beschikbaar.

Nadat een beheerde instantie is geïmplementeerd in een groep, *kunt* u de Azure-portal gebruiken om de eigenschappen ervan op de pagina met de prijslaag te wijzigen.


## <a name="create-an-instance-pool"></a>Een instantiegroep maken

Ga als u een instantiegroep wilt maken:

1. [Maak een virtueel netwerk met een subnet.](#create-a-virtual-network-with-a-subnet)
2. [Een instantiegroep maken](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Een virtueel netwerk maken met een subnet 

Zie de volgende artikelen om meerdere instantiepools binnen hetzelfde virtuele netwerk te plaatsen:

- [Bepaal de subnetgrootte van VNet voor een azure SQL Database-beheerde instantie](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Maak een nieuw virtueel netwerk en subnet met behulp van de [Azure-portalsjabloon](sql-database-managed-instance-create-vnet-subnet.md) of volg de instructies voor [het voorbereiden van een bestaand virtueel netwerk.](sql-database-managed-instance-configure-vnet-subnet.md)
 


### <a name="create-an-instance-pool"></a>Een instantiegroep maken 

Nadat u de vorige stappen hebt voltooid, bent u klaar om een instantiegroep te maken.

De volgende beperkingen zijn van toepassing op instantiegroepen:

- Alleen Algemeen Doel en Gen5 zijn beschikbaar in openbare preview.
- De naam van de groep kan alleen kleine letters, getallen en koppeltekens bevatten en kan niet beginnen met een koppelteken.
- Als u AHB (Azure Hybrid Benefit) wilt gebruiken, wordt dit toegepast op instantiepoolniveau. U het licentietype instellen tijdens het maken van een groep of het op elk gewenst moment bijwerken na het maken.

> [!IMPORTANT]
> Het implementeren van een instantiegroep is een langdurige bewerking die ongeveer 4,5 uur duurt.

Ga als u op zoek gaat naar netwerkparameters:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Ga als u een instantiegroep wilt maken:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Omdat het implementeren van een instantiegroep een langdurige bewerking is, moet u wachten tot deze is voltooid voordat een van de volgende stappen in dit artikel wordt uitgevoerd.

## <a name="create-a-managed-instance-inside-the-pool"></a>Een beheerde instantie in het zwembad maken 

Na de succesvolle implementatie van de instantiegroep is het tijd om een instantie erin te maken.

Als u een beheerde instantie wilt maken, voert u de volgende opdracht uit:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Het implementeren van een instantie in een pool duurt een paar minuten. Nadat de eerste instantie is gemaakt, kunnen extra exemplaren worden gemaakt:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Een database maken in een instantie 

Als u databases wilt maken en beheren in een beheerde instantie die zich in een groep bevindt, gebruikt u de opdrachten voor één instantie.

Ga als lid van het werk om een database in een beheerde instantie te maken:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Gebruik van instantiepool 
 
Ga als lid van het nieuwe bedrijf naar een lijst met instanties in een groep:

```powershell
$instancePool | Get-AzSqlInstance
```


Ga als een opgaalse brongebruik van de groep:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Ga als u op de website naar een gedetailleerd gebruiksoverzicht van de groep en instanties:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Ga als bedoeld als u de databases in een instantie wilt weergeven:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Er is een limiet van 100 databases per groep (niet per exemplaar).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Een beheerde instantie in een pool schalen 


Nadat u een beheerde instantie hebt bevolken met databases, u instantielimieten voor opslag of prestaties bereiken. Als het gebruik van de groep niet is overschreden, u uw instantie schalen.
Het schalen van een beheerde instantie in een groep is een bewerking die een paar minuten duurt. De voorwaarde voor schalen is beschikbaar vCores en opslag op instantiepoolniveau.

Ga als het gaat om het aantal vCores en de opslaggrootte:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Ga als u alleen de opslaggrootte bijwerkt:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Verbinding maken met een beheerde instantie in een pool

Als u verbinding wilt maken met een beheerde instantie in een groep, zijn de volgende twee stappen vereist:

1. [Schakel het openbare eindpunt voor de instantie in](#enable-the-public-endpoint-for-the-instance).
2. [Voeg een binnenkomende regel toe aan de netwerkbeveiligingsgroep (NSG).](#add-an-inbound-rule-to-the-network-security-group)

Nadat beide stappen zijn voltooid, u verbinding maken met de instantie met behulp van een openbaar eindpuntadres, poort en referenties die zijn verstrekt tijdens het maken van instanties. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Het openbare eindpunt voor de instantie inschakelen

Het inschakelen van het openbare eindpunt voor een instantie kan via de Azure-portal of via de volgende PowerShell-opdracht:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Deze parameter kan ook worden ingesteld tijdens het maken van bijvoorbeelden.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Een binnenkomende regel toevoegen aan de netwerkbeveiligingsgroep 

Deze stap kan worden uitgevoerd via de Azure-portal of met PowerShell-opdrachten en kan op elk gewenst moment worden uitgevoerd nadat het subnet is voorbereid op de beheerde instantie.

Zie Openbaar [eindpuntverkeer toestaan in de netwerkbeveiligingsgroep](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)voor meer informatie .


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Een bestaande afzonderlijke instantie verplaatsen in een instantiegroep 
 
Het verplaatsen van instanties in en uit een groep is een van de beperkingen voor openbare preview. Een tijdelijke oplossing die kan worden gebruikt, is afhankelijk van point-in-time herstel van databases van een instantie buiten een groep naar een instantie die al in een groep is. 

Beide exemplaren moeten zich in hetzelfde abonnement en dezelfde regio bevinden. Herstel van verschillende regio's en kruisabonnementen wordt momenteel niet ondersteund.

Dit proces heeft een periode van downtime.

Ga als u bestaande databases verplaatsen:

1. Werkbelastingen onderbreken op het beheerde exemplaar waaruit u migreert.
2. Scripts genereren om systeemdatabases te maken en deze uit te voeren op de instantie die zich in de instantiegroep bevindt.
3. Doe een point-in-time-herstel van elke database van de afzonderlijke instantie naar de instantie in de groep.

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

4. Wijs uw toepassing naar het nieuwe exemplaar en hervat de werkbelasting.

Als er meerdere databases zijn, herhaalt u het proces voor elke database.


## <a name="next-steps"></a>Volgende stappen

- Zie [ALGEMENE SQL-functies voor](sql-database-features.md)een lijst met functies en vergelijkingsfuncties .
- Zie [VNet-configuratie beheerde instantie voor](sql-database-managed-instance-connectivity-architecture.md)meer informatie over vNet-configuratie.
- Zie [een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor een snellestart waarmee een beheerde instantie wordt gemaakt en een database wordt hersteld uit een back-upbestand.
- Zie [Beheerde instantiemigratie met DMS](../dms/tutorial-sql-server-to-managed-instance.md)voor een zelfstudie met de Azure Database Migration Service (DMS) voor migratie.
- Zie Azure SQL Database met [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)controleren voor geavanceerde bewaking van beheerde instantiedatabaseprestaties met ingebouwde informatie over probleemoplossing.
- Zie [SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijsinformatie voor prijsinformatie .
