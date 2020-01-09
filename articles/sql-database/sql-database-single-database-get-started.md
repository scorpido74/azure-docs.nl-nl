---
title: Een individuele database maken
description: U kunt één data base in Azure SQL Database maken en er query's op uitvoeren met behulp van de Azure Portal, Power shell en Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: 5cfccd6e79b7289b6fd59abcabba05abab6ab09b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443844"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Snelstartgids: een enkele data base in Azure SQL Database maken met behulp van de Azure Portal, Power shell en Azure CLI

Het maken van [één database](sql-database-single-database.md) is de snelste een eenvoudigste implementatieoptie om een database te maken in Azure SQL Database. Aan de hand van deze quickstart kunt u een individuele database maken via de Azure-portal.

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/). 

Voor alle stappen in deze snelstartgids moet u zich aanmelden bij [Azure Portal](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Een individuele database maken

U kunt één data base maken in de ingerichte of serverloze Compute-laag.

- Een enkele data base in de ingerichte Compute-laag is vooraf toegewezen een vaste hoeveelheid reken resources, inclusief CPU en geheugen, met behulp van een van twee [aankoop modellen](sql-database-purchase-models.md).
- Eén data base in de compute-laag zonder server heeft een bereik van reken resources, inclusief CPU en geheugen die automatisch worden geschaald en alleen beschikbaar is in de [vCore-gebaseerde aankoop modellen](sql-database-service-tiers-vcore.md).

Wanneer u een individuele database maakt, definieert u ook een [SQL Database-server](sql-database-servers.md) om die te beheren en in een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) in een opgegeven regio te plaatsen.

> [!NOTE]
> Deze Quick Start maakt gebruik [van het op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md), maar het [op DTU gebaseerde aankoop model](sql-database-service-tiers-DTU.md) is ook beschikbaar.

U maakt als volgt een individuele database met de voorbeeldgegevens van Adventure Works LT:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Een query uitvoeren op de database

Nu u de database hebt gemaakt, gebruikt u het ingebouwde hulpprogramma voor query's in de Azure-portal om verbinding te maken met de database en query's uit te voeren op de gegevens.

1. Selecteer op de **SQL Database**-pagina voor uw database in het linkermenu de optie **Query-editor (preview)** .

   ![Aanmelden bij query-editor](./media/sql-database-get-started-portal/query-editor-login.png)

2. Voer uw aanmeldgegevens in en selecteer **OK**.
3. Voer de volgende query in het deelvenster **Query-editor** in.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Klik op **Uitvoeren** en bekijk de resultaten van de query in het deelvenster **Resultaten**.

   ![Resultaten query-editor](./media/sql-database-get-started-portal/query-editor-results.png)

5. Sluit de pagina **Query-editor** en selecteer **OK** wanneer u wordt gevraagd om uw niet-opgeslagen bewerkingen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Behoud deze resourcegroep, databaseserver en individuele database als u naar de [Volgende stappen](#next-steps) wilt gaan. De volgende stappen laten zien hoe u verbinding maakt met en een query uitvoert op uw database met behulp van verschillende methoden.

Wanneer u klaar bent met deze resources, kunt u ze als volgt verwijderen:

1. Selecteer in het linkermenu in Azure Portal **Resourcegroepen** en selecteer vervolgens **myResourceGroup**.
2. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep.
3. Voer *myResourceGroup* in het tekstvak in en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Maak een firewallregel op serverniveau om via on-premises of externe hulpprogramma's verbinding met de individuele database te maken. Zie [Een serverfirewallregel maken](sql-database-server-level-firewall-rule.md) voor meer informatie.
- Nadat u een serverfirewallregel hebt gemaakt, kunt u met verschillende hulpprogramma's en programmeertalen [verbinding maken met uw database en query's uitvoeren](sql-database-connect-query.md) op uw database.
  - [Verbinding maken en query's uitvoeren met behulp van SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Verbinding maken en query's uitvoeren met behulp van Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Zie [Azure cli](sql-database-cli-samples.md)-voor beelden voor het maken van één data base in de ingerichte Compute-laag met behulp van Azure cli.
- Zie Azure PowerShell-voor [beelden](sql-database-powershell-samples.md)voor het maken van één data base in de ingerichte Compute-laag met behulp van Azure PowerShell.
- Zie [serverloze data base maken](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)om één Data Base te maken in de compute-laag zonder server met behulp van Azure Power shell.
