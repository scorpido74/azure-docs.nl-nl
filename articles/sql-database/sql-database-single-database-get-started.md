---
title: Een individuele database maken
description: Maak een enkele database van Azure SQL Database met de Azure-portal, PowerShell of Azure CLI. Query de database met Query Editor in de Azure-portal.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240518"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Snelstart: een enkele database van Azure SQL Database maken

In deze quickstart gebruikt u de Azure-portal, een PowerShell-script of een Azure CLI-script om een enkele azure-database van Azure SQL Database te maken. Vervolgens bevraagt u de database met **queryeditor** in de Azure-portal. 

Eén [database](sql-database-single-database.md) is de snelste en eenvoudigste implementatieoptie voor Azure SQL Database. U beheert één database binnen een [SQL Database-server,](sql-database-servers.md)die zich in een [Azure-brongroep](../azure-resource-manager/management/overview.md) in een opgegeven Azure-gebied bevindt. In deze quickstart maakt u een nieuwe brongroep en SQL-server voor de nieuwe database.

U één database maken in de *ingerichte* of *serverloze* compute-laag. Een ingerichte database is vooraf toegewezen een vaste hoeveelheid compute resources, met inbegrip van CPU en geheugen, en maakt gebruik van een van de twee [inkoopmodellen](sql-database-purchase-models.md). Deze quickstart maakt een ingerichte database met behulp van het [op vCore gebaseerde](sql-database-service-tiers-vcore.md) inkoopmodel, maar u ook een [DTU-model](sql-database-service-tiers-DTU.md) kiezen. 

De serverless compute-laag is alleen beschikbaar in het vCore-gebaseerde inkoopmodel en heeft een automatisch geschaald bereik van rekenbronnen, waaronder CPU en geheugen. Zie [Een serverloze database maken](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)als u één database in de serverloze compute-laag wilt maken.

## <a name="prerequisite"></a>Vereiste

- Een actief Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Een individuele database maken

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Een query uitvoeren op de database

Zodra uw database is gemaakt, u de ingebouwde **Query-editor** in de Azure-portal gebruiken om verbinding te maken met de database en de gegevens op te vragen.

1. Zoek en selecteer **SQL-databases**in de portal en selecteer vervolgens uw database in de lijst.
1. Selecteer op de **SQL Database**-pagina voor uw database in het linkermenu de optie **Query-editor (preview)**.
1. Voer de aanmeldingsgegevens van uw serverbeheerder in en selecteer **OK**.
   
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

Houd de brongroep, server en afzonderlijke database om door te gaan naar de volgende stappen en leer hoe u uw database verbinden en opvragen met verschillende methoden.

Wanneer u klaar bent met het gebruik van deze bronnen, u de door u gemaakte brongroep verwijderen, waardoor ook de server en de afzonderlijke database erin worden verwijderd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als het gaat om het verwijderen van **myResourceGroup** en al zijn resources via de Azure-portal:

1. Zoek en selecteer **resourcegroepen**in de portal en selecteer **vervolgens myResourceGroup** in de lijst.
1. Selecteer resourcegroep **verwijderen**op de pagina Resourcegroep verwijderen .
1. Voer **onder De naam van de brongroep**de naam van *mijnResourcegroep*in en selecteer **Verwijderen**.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de brongroep en al zijn resources wilt verwijderen, voert u de volgende opdracht Azure CLI uit met de naam van uw resourcegroep:

```azurecli-interactive
az group delete --name <your resource group>
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Als u de brongroep en al zijn resources wilt verwijderen, voert u de volgende PowerShell-cmdlet uit met de naam van uw resourcegroep:

 ```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---
## <a name="next-steps"></a>Volgende stappen

[Maak verbinding en query](sql-database-connect-query.md) uw database met behulp van verschillende tools en talen:
> [!div class="nextstepaction"]
> [Verbinding maken en query's uitvoeren met behulp van SQL Server Management Studio](sql-database-connect-query-ssms.md)
> 
> [Verbinding maken en query's uitvoeren met behulp van Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
