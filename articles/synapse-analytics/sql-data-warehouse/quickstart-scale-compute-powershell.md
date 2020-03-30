---
title: Schaalgegevens voor Synapse SQL-pool (Azure PowerShell)
description: U compute schalen voor Synapse SQL-pool (datawarehouse) met Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c7ec8db212a24f1f23f393e4cb0e7f4150605a56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350799"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Snelstart: Schaalcompute voor Synapse SQL-pool met Azure PowerShell

U compute schalen voor Synapse SQL-pool (datawarehouse) met Azure PowerShell. [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal juist om kosten te besparen. 

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Deze quickstart gaat ervan uit dat u al een SQL-pool hebt die u schalen. Als u er een wilt maken, gebruikt u [Create and Connect - portal](create-data-warehouse-portal.md) om een SQL-groep te maken met de naam **mySampleDataWarehouse.**

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de aanwijzingen op het scherm.

```powershell
Connect-AzAccount
```

Voer [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)uit om te zien welk abonnement u gebruikt.

```powershell
Get-AzSubscription
```

Als u een ander abonnement moet gebruiken dan de standaardinstelling, voert u [Set-AzContext](/powershell/module/az.accounts/set-azcontext)uit.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Datawarehousegegevens opzoeken

Zoek de databasenaam, de servernaam en de resourcegroep op voor het datawarehouse dat u wilt onderbreken en hervatten.

Volg deze stappen om de locatiegegevens voor uw datawarehouse op te zoeken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkernavigatiepagina van de Azure-portal.
3. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om het gegevensmagazijn te openen.

    ![Servernaam en resourcegroep](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Noteer de naam van het datawarehouse. Deze wordt gebruikt als de databasenaam. Een datawarehouse is een type database. Noteer ook de naam van de server en de resourcegroep. U gebruikt de servernaam en de naam van de brongroep in de opdrachten voor pauzeren en hervatten.
5. Gebruik alleen het eerste deel van de servernaam in de PowerShell-cmdlets. In de voorgaande afbeelding wordt de volledige servernaam sqlpoolservername.database.windows.net. We gebruiken **sqlpoolservernaam** als servernaam in de PowerShell-cmdlet.

## <a name="scale-compute"></a>De schaal van Compute aanpassen

In SQL-groep u de rekenresources vergroten of verkleinen door gegevensmagazijnen aan te passen. Met behulp van [Maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU's voor **mySampleDataWarehouse** aangepast.

Als u gegevensmagazijnen wilt wijzigen, gebruikt u de cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell. In het volgende voorbeeld worden de gegevensmagazijnen ingesteld op DW300c voor de database **mySampleDataWarehouse**, die wordt gehost in de **resourcegroepnaam resourcegroep** resource op **serversqlpoolservernaam.**

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Status van datawarehouse controleren

Als u de huidige status van het gegevensmagazijn wilt bekijken, gebruikt u de [cmdlet Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) PowerShell. Deze cmdlet toont de status van de **mySampleDataWarehouse-database** in **resourcegroup-resourcegroepnaam** en **serversqlpoolservername.database.windows.net**.

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
U hebt nu geleerd hoe u compute schalen voor SQL-pool. Ga voor meer informatie over SQL-pool verder naar de zelfstudie voor het laden van gegevens.

> [!div class="nextstepaction"]
>[Gegevens in een SQL-groep laden](load-data-from-azure-blob-storage-using-polybase.md)
