---
title: 'Quick Start: Compute & Resume-Power shell '
description: Gebruik Power shell om de reken kracht in de SQL-groep voor Azure Synapse Analytics te onderbreken om kosten te besparen. Hervat de compute wanneer u klaar bent om het Data Warehouse te gebruiken.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ce183edef9e5895d7b3f702f5466c505956a869a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200563"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-with-azure-powershell"></a>Quick Start: Compute onderbreken en hervatten in azure Synapse Analytics SQL-groep met Azure PowerShell

Gebruik Azure PowerShell om Compute voor de SQL-groep te onderbreken om kosten te besparen. [Hervat de compute](sql-data-warehouse-manage-compute-overview.md) wanneer u klaar bent om het Data Warehouse te gebruiken.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze Snelstartgids wordt ervan uitgegaan dat u al een SQL-groep hebt die u kunt onderbreken en hervatten. Als u er een wilt maken, kunt u [maken en verbinden-Portal](create-data-warehouse-portal.md) gebruiken om een SQL-groep met de naam **mySampleDataWarehouse**te maken.

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

Zoek de naam van de data base, de server naam en de resource groep voor de SQL-groep die u wilt onderbreken en hervatten.

Volg deze stappen om locatie gegevens voor uw SQL-groep te vinden.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linker pagina van de Azure Portal.
1. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** . De datawarehouse wordt geopend.

    ![Servernaam en resourcegroep](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Noteer de naam van het Data Warehouse, de naam van de data base. Noteer ook de naam van de server en de resourcegroep.
1. Gebruik alleen het eerste deel van de server naam in de Power shell-cmdlets. In de voor gaande afbeelding is de volledige server naam sqlpoolservername.database.windows.net. We gebruiken **sqlpoolservername** als de server naam in de Power shell-cmdlet.

## <a name="pause-compute"></a>Compute onderbreken

Als u kosten wilt besparen, kunt u de reken resources op aanvraag onderbreken en hervatten. Als u de data base bijvoorbeeld niet gebruikt in de nacht en in het weekend, kunt u deze onderbreken tijdens deze tijden en deze op de dag hervatten. Er worden geen kosten in rekening gebracht voor reken resources terwijl de data base wordt onderbroken. Er worden echter nog steeds kosten in rekening gebracht voor opslag.

Gebruik de cmdlet [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) om een Data Base te onderbreken. In het volgende voor beeld wordt een Data Warehouse met de naam **mySampleDataWarehouse** gehost op een server met de naam **sqlpoolservername**. De server bevindt zich in een Azure-resource groep met de naam **myResourceGroup**.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

In het volgende voor beeld wordt de data base in het $database-object opgehaald. Vervolgens wordt het object door sluizen naar [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). De resultaten worden opgeslagen in het object resultDatabase. Met de laatste opdracht worden de resultaten weer gegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Compute hervatten

Als u een Data Base wilt starten, gebruikt u de cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) . In het volgende voor beeld wordt een Data Base met de naam **mySampleDataWarehouse** gehost op een server met de naam **sqlpoolservername**. De server bevindt zich in een Azure-resource groep met de naam **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

In het volgende voor beeld wordt de data base in het $database-object opgehaald. Vervolgens wordt het object door sluizen om [AzSqlDatabase te hervatten](/powershell/module/az.sql/resume-azsqldatabase) en worden de resultaten opgeslagen in $resultDatabase. Met de laatste opdracht worden de resultaten weer gegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Controleer de status van uw data warehouse-bewerking

Als u de status van uw data warehouse wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) .

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor datawarehouse-eenheden en gegevens die zijn opgeslagen in uw datawarehouse. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag ruimte wilt bewaren, moet u de compute onderbreken.
- Als u toekomstige kosten wilt verwijderen, kunt u de SQL-groep verwijderen.

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en klik op uw SQL-groep.

    ![Resources opschonen](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. Wanneer de SQL-groep is onderbroken, ziet u een **Start** -knop.  Als u het berekenen wilt hervatten, klikt u op **Start**.

3. Als u de SQL-groep wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **verwijderen**.

4. Als u de door u gemaakte SQL-Server wilt verwijderen, klikt u op **sqlpoolservername.database.Windows.net**en vervolgens op **verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.


## <a name="next-steps"></a>Volgende stappen

U hebt nu de reken kracht voor uw SQL-groep gepauzeerd en hervat. Voor meer informatie over SQL-pool gaat u verder met de zelf studie voor het laden van gegevens.

> [!div class="nextstepaction"]
> [Gegevens laden in SQL-groep](load-data-from-azure-blob-storage-using-polybase.md)
