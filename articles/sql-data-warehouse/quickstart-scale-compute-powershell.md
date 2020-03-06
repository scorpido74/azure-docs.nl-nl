---
title: 'Snelstartgids: scale Compute-Power shell '
description: Schaal berekenen in SQL-groep in Power shell. Schaal de rekenkracht uit voor betere prestaties of verklein de schaal om kosten te besparen.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5952f17c83b778e8713488b5c53c9f210c84a146
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390500"
---
# <a name="quickstart-scale-compute-in-in-azure-synapse-analytics-sql-pool-using-azure-powershell"></a>Snelstartgids: Compute scale in in azure Synapse Analytics SQL-groep met behulp van Azure PowerShell

Schaal Compute in SQL pool met behulp van Azure PowerShell. [Schaal rekenkracht uit](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal om kosten te besparen.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze Quick Start wordt ervan uitgegaan dat u al een SQL-groep hebt die u kunt schalen. Als u er een wilt maken, gebruikt u [maken en verbinden-Portal](create-data-warehouse-portal.md) om een SQL-groep met de naam **mySampleDataWarehouse**te maken.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de aanwijzingen op het scherm.

```powershell
Connect-AzAccount
```

Als u wilt zien welk abonnement u gebruikt, voert u [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)uit.

```powershell
Get-AzSubscription
```

Als u een ander abonnement wilt gebruiken dan de standaard instelling, voert u [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Datawarehousegegevens opzoeken

Zoek de databasenaam, de servernaam en de resourcegroep op voor het datawarehouse dat u wilt onderbreken en hervatten.

Volg deze stappen om de locatiegegevens voor uw datawarehouse op te zoeken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linker navigatie pagina van het Azure Portal.
3. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om het Data Warehouse te openen.

    ![Servernaam en resourcegroep](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Noteer de naam van het datawarehouse. Deze wordt gebruikt als de databasenaam. Een datawarehouse is een type database. Noteer ook de naam van de server en de resourcegroep. U gebruikt de server naam en de naam van de resource groep in de opdrachten Pause en resume.
5. Gebruik alleen het eerste deel van de server naam in de Power shell-cmdlets. In de voor gaande afbeelding is de volledige server naam sqlpoolservername.database.windows.net. We gebruiken **sqlpoolservername** als de server naam in de Power shell-cmdlet.

## <a name="scale-compute"></a>De schaal van Compute aanpassen

In SQL-pool kunt u de reken resources verg Roten of verkleinen door Data Warehouse-eenheden aan te passen. Met behulp van [Maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU’s voor **mySampleDataWarehouse** aangepast.

Als u Data Warehouse-eenheden wilt wijzigen, gebruikt u de Power shell [-cmdlet Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . In het volgende voor beeld worden de Data Warehouse-eenheden ingesteld op DW300c voor de Data Base- **mySampleDataWarehouse**, die wordt gehost in de resource groep **resourcegroupname** op server **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Status van datawarehouse controleren

Als u de huidige status van het Data Warehouse wilt zien, gebruikt u de Power shell [-cmdlet Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) . Met deze cmdlet wordt de status van de **mySampleDataWarehouse** -data base in ResourceGroup **resourcegroupname** en server **sqlpoolservername.database.Windows.net**weer gegeven.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Dit resulteert in ongeveer het volgende:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

U ziet de **Status** van de database in de uitvoer. In dit geval kunt u zien dat deze database online is.  Wanneer u deze opdracht uitvoert, ontvangt u de statuswaarde Online, Onderbreken, Hervatten, Schalen of Onderbroken.

Voer de volgende opdracht uit om de status op zichzelf te bekijken:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Volgende stappen
U hebt nu geleerd hoe u de reken kracht voor de SQL-groep kunt schalen. Voor meer informatie over SQL-pool gaat u verder met de zelf studie voor het laden van gegevens.

> [!div class="nextstepaction"]
>[Gegevens laden in een SQL-groep](load-data-from-azure-blob-storage-using-polybase.md)
