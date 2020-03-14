---
title: Een individuele database maken
description: Maak een Azure SQL Database afzonderlijke Data Base met behulp van de Azure Portal, Power shell of Azure CLI. Query's uitvoeren op de data base met query-editor in het Azure Portal.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 03/10/2020
ms.openlocfilehash: 638adaac699bb7aa2774f5cbd37dc8394a2baee3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240518"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Snelstartgids: een Azure SQL Database afzonderlijke data base maken

In deze Quick Start gebruikt u de Azure Portal, een Power shell-script of een Azure CLI-script voor het maken van een Azure SQL Database enkele data base. Vervolgens voert u een query uit op de data base met behulp van **query-editor** in de Azure Portal. 

[Eén data base](sql-database-single-database.md) is de snelste en eenvoudigste implementatie optie voor Azure SQL database. U beheert één data base binnen een [SQL database-server](sql-database-servers.md), die zich binnen een [Azure-resource groep](../azure-resource-manager/management/overview.md) in een opgegeven Azure-regio bevindt. In deze Quick Start maakt u een nieuwe resource groep en SQL-Server voor de nieuwe data base.

U kunt één data base maken in de *ingerichte* of *serverloze* Compute-laag. Een ingerichte data base is een vooraf toegewezen hoeveelheid reken resources, inclusief CPU en geheugen, en maakt gebruik van een van de twee [aankoop modellen](sql-database-purchase-models.md). In deze Snelstartgids wordt een ingerichte data base gemaakt [op basis van het op vCore gebaseerde](sql-database-service-tiers-vcore.md) aankoop model, maar u kunt ook een [op DTU gebaseerd](sql-database-service-tiers-DTU.md) model kiezen. 

De compute-laag zonder server is alleen beschikbaar in het vCore-inkoop model en heeft een automatisch schaalbaar bereik van Compute-resources, met inbegrip van CPU en geheugen. Zie [een serverloze data base maken](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)als u een afzonderlijke Data Base wilt maken in de serverloze Compute-laag.

## <a name="prerequisite"></a>Vereiste

- Een actief Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Een individuele database maken

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Een query uitvoeren op de database

Zodra de data base is gemaakt, kunt u de ingebouwde query- **Editor** in het Azure Portal gebruiken om verbinding te maken met de data base en de gegevens op te vragen.

1. Zoek in de portal naar en selecteer **SQL-data bases**en selecteer vervolgens uw data base in de lijst.
1. Selecteer op de **SQL Database**-pagina voor uw database in het linkermenu de optie **Query-editor (preview)** .
1. Voer de aanmeldings gegevens voor de server beheerder in en selecteer **OK**.
   
   ![Aanmelden bij query-editor](./media/sql-database-single-database-get-started/query-editor-login.png)

1. Voer de volgende query in het deelvenster **Query-editor** in.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Klik op **Uitvoeren** en bekijk de resultaten van de query in het deelvenster **Resultaten**.

   ![Resultaten query-editor](./media/sql-database-single-database-get-started/query-editor-results.png)

1. Sluit de pagina **Query-editor** en selecteer **OK** wanneer u wordt gevraagd om uw niet-opgeslagen bewerkingen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Behoud de resource groep, de server en de afzonderlijke Data Base om door te gaan met de volgende stappen en lees hoe u verbinding maakt met uw data base en hoe u er een query op kunt uitvoeren met verschillende methoden.

Wanneer u klaar bent met het gebruik van deze resources, kunt u de resource groep die u hebt gemaakt, verwijderen, waardoor ook de server en de afzonderlijke data base erin worden verwijderd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

**MyResourceGroup** en alle bijbehorende resources verwijderen met behulp van de Azure portal:

1. In de portal zoekt en selecteert u **resource groepen**en selecteert u vervolgens **myResourceGroup** in de lijst.
1. Selecteer **resource groep verwijderen**op de pagina resource groep.
1. Voer onder **Typ de naam van de resource groep** *myResourceGroup*in en selecteer vervolgens **verwijderen**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u de resource groep en alle bijbehorende resources wilt verwijderen, voert u de volgende Azure CLI-opdracht uit met behulp van de naam van uw resource groep:

```azurecli-interactive
az group delete --name <your resource group>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de resource groep en alle bijbehorende resources wilt verwijderen, voert u de volgende Power shell-cmdlet uit met behulp van de naam van uw resource groep:

 ```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---
## <a name="next-steps"></a>Volgende stappen

Verbinding maken met uw data base [en query's uitvoeren](sql-database-connect-query.md) met verschillende hulpprogram ma's en talen:
> [!div class="nextstepaction"]
> [Verbinding maken en query's uitvoeren met behulp van SQL Server Management Studio](sql-database-connect-query-ssms.md)
> 
> [Verbinding maken en query's uitvoeren met behulp van Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
