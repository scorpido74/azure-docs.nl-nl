---
title: 'Quickstart: De schaal van rekenkracht vergroten in Azure SQL Data Warehouse - PowerShell | Microsoft Docs'
description: De schaal van rekenkracht vergroten in Azure SQL Data Warehouse in PowerShell. De schaal van rekenkracht vergroten voor betere prestaties of de schaal juist verkleinen om kosten te besparen.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 193aec8de2a6ee34da666a013dcb0d49d85c08b0
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574855"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Quickstart: De schaal van rekenkracht vergroten in Azure SQL Data Warehouse in PowerShell

De schaal van rekenkracht vergroten in Azure SQL Data Warehouse in PowerShell. [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal juist om kosten te besparen.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze Quick Start wordt ervan uitgegaan dat u al een SQL Data Warehouse hebt dat u kunt schalen. Gebruik [Maken en verbinden - portal](create-data-warehouse-portal.md) om een datawarehouse met de naam **mySampleDataWarehouse** te maken.

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

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op **SQL Data Warehouses** op de linkerpagina in de Azure Portal.
3. Selecteer **mySampleDataWarehouse** op de pagina **SQL Data Warehouses**. De datawarehouse wordt geopend.

    ![Servernaam en resourcegroep](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Noteer de naam van het datawarehouse. Deze wordt gebruikt als de databasenaam. Een datawarehouse is een type database. Noteer ook de naam van de server en de resourcegroep. U gebruikt deze in de opdrachten voor het onderbreken en hervatten.
5. Als uw server foo.database.windows.net is, gebruikt u alleen het eerste deel als de servernaam in de PowerShell-cmdlets. Op de voorgaande afbeelding is de volledige servernaam newserver 20171113.database.windows.net. We gebruiken **newserver-20180430** als de servernaam in de PowerShell-cmdlet.

## <a name="scale-compute"></a>De schaal van Compute aanpassen

In SQL Data Warehouse kunt u het aantal rekenresources verhogen of verlagen door de DWU's aan te passen. Met behulp van [Maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU's voor **mySampleDataWarehouse** aangepast.

Als u Data Warehouse-eenheden wilt wijzigen, gebruikt u de Power shell [-cmdlet Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . In het volgende voor beeld worden de Data Warehouse-eenheden ingesteld op DW300c voor de Data Base- **mySampleDataWarehouse** die wordt gehost in de **MyResourceGroup** van de resource groep op de server **mynewserver-20180430**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Status van datawarehouse controleren

Als u de huidige status van het Data Warehouse wilt zien, gebruikt u de Power shell [-cmdlet Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) . Hiermee wordt de status van de **mySampleDataWarehouse**-database in resourceGroup **myResourceGroup** en server **mynewserver-20180430.database.windows.net**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

Dit resulteert in ongeveer het volgende:

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
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
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
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
U hebt nu geleerd hoe u de rekenkracht voor uw datawarehouse wijzigt. Voor meer informatie over Azure SQL Data Warehouse gaat u verder met de zelfstudie voor het laden van gegevens.

> [!div class="nextstepaction"]
>[Gegevens in een SQL Data Warehouse laden](load-data-from-azure-blob-storage-using-polybase.md)
