---
title: 'Quick Start: Compute & Resume-Power shell '
description: Gebruik Power shell om de reken kracht in Azure SQL Data Warehouse te onderbreken om kosten op te slaan. Hervat de compute wanneer u klaar bent om het Data Warehouse te gebruiken.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 148f3f99aac5ce01bd88f1efaecb5821ae94f50a
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539170"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-azure-powershell"></a>Quick Start: de berekening onderbreken en hervatten in Azure SQL Data Warehouse met Azure PowerShell

Gebruik Azure PowerShell om Compute in Azure SQL Data Warehouse te onderbreken om kosten te besparen. [Hervat de compute](sql-data-warehouse-manage-compute-overview.md) wanneer u klaar bent om het Data Warehouse te gebruiken.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze Snelstartgids wordt ervan uitgegaan dat u al een SQL Data Warehouse hebt dat u kunt onderbreken en hervatten. Als u er een wilt maken, kunt u [maken en verbinden-Portal](create-data-warehouse-portal.md) gebruiken om een Data Warehouse met de naam **mySampleDataWarehouse**te maken.

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
2. Klik op de linkerpagina in Azure Portal op **SQL-databases**.
3. Selecteer **mySampleDataWarehouse** op de pagina **SQL-databases**. De datawarehouse wordt geopend.

    ![Servernaam en resourcegroep](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Noteer de naam van het Data Warehouse, de naam van de data base. Noteer ook de naam van de server en de resourcegroep.
6. Als uw server foo.database.windows.net is, gebruikt u alleen het eerste deel als de servernaam in de PowerShell-cmdlets. Op de voorgaande afbeelding is de volledige servernaam newserver 20171113.database.windows.net. Verwijder het achtervoegsel en gebruik **newserver-20171113** als server naam in de Power shell-cmdlet.

## <a name="pause-compute"></a>Compute onderbreken

Als u kosten wilt besparen, kunt u de reken resources op aanvraag onderbreken en hervatten. Als u de data base bijvoorbeeld niet gebruikt in de nacht en in het weekend, kunt u deze onderbreken tijdens deze tijden en deze op de dag hervatten. Er worden geen kosten in rekening gebracht voor reken resources terwijl de data base wordt onderbroken. Er worden echter nog steeds kosten in rekening gebracht voor opslag.

Gebruik de cmdlet [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) om een Data Base te onderbreken. In het volgende voor beeld wordt een Data Warehouse met de naam **mySampleDataWarehouse** gehost op een server met de naam **newserver-20171113**. De server bevindt zich in een Azure-resource groep met de naam **myResourceGroup**.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

In het volgende voor beeld wordt de data base in het $database-object opgehaald. Vervolgens wordt het object door sluizen naar [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). De resultaten worden opgeslagen in het object resultDatabase. Met de laatste opdracht worden de resultaten weer gegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Compute hervatten

Als u een Data Base wilt starten, gebruikt u de cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) . In het volgende voor beeld wordt een Data Base met de naam mySampleDataWarehouse gehost op een server met de naam newserver-20171113. De server bevindt zich in een Azure-resource groep met de naam myResourceGroup.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

In het volgende voor beeld wordt de data base in het $database-object opgehaald. Vervolgens wordt het object door sluizen om [AzSqlDatabase te hervatten](/powershell/module/az.sql/resume-azsqldatabase) en worden de resultaten opgeslagen in $resultDatabase. Met de laatste opdracht worden de resultaten weer gegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Controleer de status van uw data warehouse-bewerking

Als u de status van uw data warehouse wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) .

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor datawarehouse-eenheden en gegevens die zijn opgeslagen in uw datawarehouse. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag ruimte wilt bewaren, moet u de compute onderbreken.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en klik op uw data warehouse.

    ![Resources opschonen](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. Als het datawarehouse is onderbroken, ziet u een knop **Start**.  Als u het berekenen wilt hervatten, klikt u op **Start**.

3. Als u de datawarehouse wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **Verwijderen**.

4. Als u de door u gemaakte SQL-Server wilt verwijderen, klikt u op **mynewserver-20171113.database.Windows.net**en vervolgens op **verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.


## <a name="next-steps"></a>Volgende stappen

U hebt nu de reken kracht voor uw data warehouse onderbroken en hervat. Voor meer informatie over Azure SQL Data Warehouse gaat u verder met de zelfstudie voor het laden van gegevens.

> [!div class="nextstepaction"]
> [Gegevens in een SQL Data Warehouse laden](load-data-from-azure-blob-storage-using-polybase.md)
