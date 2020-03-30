---
title: Gegevens over de berekening onderbreken en hervatten in de Synapse SQL-groep met Azure PowerShell
description: U Azure PowerShell gebruiken om de Synapse SQL-groep (datawarehouse) te pauzeren en te hervatten. compute resources.
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
ms.openlocfilehash: 336b5a65c7a23a060e422b69f8ad3216bee6ad19
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350985"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Snelstart: gegevens in Synapse SQL-groep onderbreken en hervatten met Azure PowerShell

U Azure PowerShell gebruiken om de compute resources (Synapse SQL Pool) (datawarehouse) te pauzeren en te hervatten. Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Deze quickstart gaat ervan uit dat u al een SQL-pool hebt die u pauzeren en hervatten. Als u er een wilt maken, u [Create and Connect - portal](create-data-warehouse-portal.md) gebruiken om een SQL-groep te maken met de naam **mySampleDataWarehouse.**

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

## <a name="look-up-sql-pool-information"></a>SQL-poolinformatie opzoeken

Zoek de databasenaam, servernaam en brongroep voor de SQL-groep die u wilt onderbreken en hervatten.

Volg de volgende stappen om locatiegegevens voor uw SQL-groep te vinden:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkerpagina van de Azure-portal.
1. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW).** De SQL-pool wordt geopend.

    ![Servernaam en resourcegroep](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Noteer de naam van de SQL-groep, de naam van de database. Noteer ook de naam van de server en de resourcegroep.
1. Gebruik alleen het eerste deel van de servernaam in de PowerShell-cmdlets. In de voorgaande afbeelding wordt de volledige servernaam sqlpoolservername.database.windows.net. We gebruiken **sqlpoolservernaam** als servernaam in de PowerShell-cmdlet.

## <a name="pause-compute"></a>Compute onderbreken

Om kosten te besparen, u op aanvraag gegevensbronnen onderbreken en hervatten. Als u bijvoorbeeld 's nachts en in het weekend de database niet gebruikt, u deze tijdens die tijden pauzeren en overdag hervatten. 

>[!NOTE]
>Er zijn geen kosten verbonden aan rekenbronnen terwijl de database wordt onderbroken. Er worden echter nog steeds kosten in rekening gebracht voor opslag.

Als u een database wilt pauzeren, gebruikt u de cmdlet [Suspend-AzSqlDatabase.](/powershell/module/az.sql/suspend-azsqldatabase) In het volgende voorbeeld wordt een SQL-groep met de naam **mySampleDataWarehouse** gepauzeerd op een server met de naam **sqlpoolserver**. De server bevindt zich in een Azure-brongroep met de naam **myResourceGroup**.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

In het volgende voorbeeld wordt de database opgehaald in het $database object. Vervolgens wordt het object naar [Suspend-AzSqlDatabase getransporteerd.](/powershell/module/az.sql/suspend-azsqldatabase) De resultaten worden opgeslagen in het object resultDatabase. De laatste opdracht toont de resultaten.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Compute hervatten

Als u een database wilt starten, gebruikt u de cmdlet [Cv-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase) In het volgende voorbeeld wordt een database gestart met de naam **mySampleDataWarehouse** die wordt gehost op een server met de naam **sqlpoolserver.** De server bevindt zich in een Azure-brongroep met de naam **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

In het volgende voorbeeld wordt de database opgehaald in het $database object. Vervolgens wordt het object naar [Cv-AzSqlDatabase geleid](/powershell/module/az.sql/resume-azsqldatabase) en worden de resultaten in $resultDatabase opgeslagen. De laatste opdracht toont de resultaten.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Status van uw SQL-poolbewerking controleren

Als u de status van uw SQL-groep wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseActivity.](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description)

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor gegevensmagazijnen en gegevens die uw SQL-groep zijn opgeslagen. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in opslag wilt bewaren, pauzeert u de gegevens.
- Als u toekomstige kosten wilt verwijderen, u de SQL-groep verwijderen.

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en klik op uw SQL-groep.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. Wanneer de SQL-pool is onderbroken, ziet u een knop **Start.**  Als u het berekenen wilt hervatten, klikt u op **Start**.

3. Als u de SQL-groep wilt verwijderen zodat er geen kosten in rekening worden gebracht voor compute of opslag, klikt u op **Verwijderen**.

4. Als u de SQL-server wilt verwijderen die u hebt gemaakt, klikt u op **sqlpoolservername.database.windows.net**en klikt u vervolgens op **Verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.


## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over SQL-groep verder naar het artikel [Gegevens laden in SQL-groep.](load-data-from-azure-blob-storage-using-polybase.md) Zie het artikel [Compute overview beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over het beheren van rekenmogelijkheden. 