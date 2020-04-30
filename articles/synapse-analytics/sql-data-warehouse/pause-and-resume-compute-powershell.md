---
title: De compute in een Synapse SQL-groep onderbreken en hervatten met Azure PowerShell
description: U kunt Azure PowerShell gebruiken om de SQL-groep Synapse (Data Warehouse) te onderbreken en weer te hervatten. Reken resources.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 44ce56238140de90145f69e966c94be8572c7749
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81252299"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Quick Start: Compute onderbreken en hervatten in Synapse SQL pool met Azure PowerShell

U kunt Azure PowerShell gebruiken om de reken resources van de Synapse SQL-pool (Data Warehouse) te onderbreken en weer te hervatten.
Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In deze Snelstartgids wordt ervan uitgegaan dat u al een SQL-groep hebt die u kunt onderbreken en hervatten. Als u er een wilt maken, kunt u [maken en verbinden-Portal](create-data-warehouse-portal.md) gebruiken om een SQL-groep met de naam **mySampleDataWarehouse**te maken.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en volg de aanwijzingen op het scherm.

```powershell
Connect-AzAccount
```

Als u wilt zien welk abonnement u gebruikt, voert u [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)uit.

```powershell
Get-AzSubscription
```

Als u een ander abonnement wilt gebruiken dan de standaard instelling, voert u [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>Gegevens van SQL-groep opzoeken

Zoek de naam van de data base, de server naam en de resource groep voor de SQL-groep die u wilt onderbreken en hervatten.

Volg deze stappen om locatie gegevens voor uw SQL-groep te vinden:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linker pagina van de Azure Portal.
1. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** . De SQL-groep wordt geopend.

    ![Servernaam en resourcegroep](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Noteer de naam van de SQL-groep. Dit is de naam van de data base. Noteer ook de naam van de server en de resourcegroep.
1. Gebruik alleen het eerste deel van de server naam in de Power shell-cmdlets. In de voor gaande afbeelding is de volledige server naam sqlpoolservername.database.windows.net. We gebruiken **sqlpoolservername** als de server naam in de Power shell-cmdlet.

## <a name="pause-compute"></a>Compute onderbreken

Als u kosten wilt besparen, kunt u de reken resources op aanvraag onderbreken en hervatten. Als u de data base bijvoorbeeld niet gebruikt in de nacht en in het weekend, kunt u deze onderbreken tijdens deze tijden en deze op de dag hervatten.

>[!NOTE]
>Er worden geen kosten in rekening gebracht voor reken resources terwijl de data base wordt onderbroken. Er worden echter nog steeds kosten in rekening gebracht voor opslag.

Gebruik de cmdlet [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om een Data Base te onderbreken. In het volgende voor beeld wordt een SQL-groep met de naam **mySampleDataWarehouse** die wordt gehost op een server met de naam **sqlpoolservername**, onderbroken. De server bevindt zich in een Azure-resource groep met de naam **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

In het volgende voor beeld wordt de data base opgehaald in het $database-object. Vervolgens wordt het object door sluizen naar [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). De resultaten worden opgeslagen in het object resultDatabase. Met de laatste opdracht worden de resultaten weer gegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Compute hervatten

Als u een Data Base wilt starten, gebruikt u de cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . In het volgende voor beeld wordt een Data Base met de naam **mySampleDataWarehouse** gehost op een server met de naam **sqlpoolservername**. De server bevindt zich in een Azure-resource groep met de naam **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

In het volgende voor beeld wordt de data base opgehaald in het $database-object. Vervolgens wordt het object door sluizen om [AzSqlDatabase te hervatten](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en worden de resultaten opgeslagen in $resultDatabase. Met de laatste opdracht worden de resultaten weer gegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Controleer de status van de SQL-groeps bewerking

Als u de status van de SQL-groep wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en gegevens die zijn opgeslagen in de SQL-groep. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag ruimte wilt bewaren, moet u de compute onderbreken.
- Als u toekomstige kosten wilt verwijderen, kunt u de SQL-groep verwijderen.

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en klik op uw SQL-groep.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. Wanneer de SQL-groep is onderbroken, ziet u een **Start** -knop.  Als u het berekenen wilt hervatten, klikt u op **Start**.

3. Als u de SQL-groep wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **verwijderen**.

4. Als u de door u gemaakte SQL-Server wilt verwijderen, klikt u op **sqlpoolservername.database.Windows.net**en vervolgens op **verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over SQL-pool gaat u naar het artikel [gegevens laden in SQL-groep](load-data-from-azure-blob-storage-using-polybase.md) . Zie het artikel [Compute-overzicht beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over het beheren van reken mogelijkheden.
