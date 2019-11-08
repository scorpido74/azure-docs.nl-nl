---
title: R met Machine Learning Services gebruiken om een query uit te zoeken
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In dit artikel leest u hoe u een R-script gebruikt met Azure SQL Database Machine Learning Services om verbinding te maken met een Azure-SQL database en query's uit te voeren met behulp van Transact-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: a54b538247f81ea3bb0ea70a2af374158bd9e2ff
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826978"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Snelstartgids: met R met Machine Learning Services een query uitvoeren op een Azure SQL database (preview-versie)

In deze Quick start ziet u hoe u met behulp van [R](https://www.r-project.org/) met Machine Learning Services verbinding maakt met een Azure SQL database en hoe u Transact-SQL-instructies gebruikt om gegevens op te vragen. Machine Learning Services is een functie van Azure SQL Database die wordt gebruikt voor het uitvoeren van een Data Base-R-script. Zie [Azure SQL Database Machine Learning Services met R (preview)](sql-database-machine-learning-services-overview.md)voor meer informatie.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een Azure SQL-database. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren in Azure SQL Database:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Individuele database |
  |:--- |:--- |
  | Maken| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Configureren | [IP-firewallregel op serverniveau](sql-database-server-level-firewall-rule.md) |
  | Gegevens laden | Adventure Works geladen volgens de quickstart |
  |||

  > [!NOTE]
  > Tijdens de preview-versie van Azure SQL Database Machine Learning Services met R, wordt de implementatie optie Managed instance niet ondersteund.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (met R) ingeschakeld. Na de onboarding voor de openbare preview wordt Machine Learning voor u ingeschakeld voor uw bestaande of nieuwe database. Volg de stappen in [Meld u aan voor de preview-versie](sql-database-machine-learning-services-overview.md#signup).

- De nieuwste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). U kunt R-scripts uitvoeren met andere database beheer-of query hulpprogramma's, maar in deze Snelstartgids gebruikt u SSMS.

## <a name="get-sql-server-connection-information"></a>Verbindingsgegevens voor SQL Server ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erop en selecteert u het pictogram **Kopiëren**.

## <a name="create-code-to-query-your-sql-database"></a>Code maken om query's uit te voeren op uw SQL-database

1. Open **SQL Server Management Studio** en maak verbinding met de SQL-database.

   Als u hulp nodig hebt bij het verbinding maken, raadpleegt u [Quick Start: SQL Server Management Studio gebruiken om verbinding te maken met een Azure-SQL database](sql-database-connect-query-ssms.md).

1. Geef het volledige R-script door aan de opgeslagen [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) -procedure.

   Het script wordt door gegeven via het argument `@script`. Alles binnen het `@script` argument moet een geldige R-code zijn.
   
   >[!IMPORTANT]
   >Voor de code in dit voorbeeld worden de voorbeeldgegevens gebruikt van AdventureWorksLT, die u als bron kunt kiezen bij het maken van uw database. Als in uw database andere gegevens staan, kunt u tabellen uit uw eigen database gebruiken in de SELECT-query. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Als er fouten optreden, kan dit zijn omdat de openbare preview van Machine Learning Services (met R) niet is ingeschakeld voor uw SQL-database. Zie bovenstaande [vereisten](#prerequisites) .

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de opgeslagen [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) -procedure uit.

1. Controleer of de bovenste 20 categorie/product rijen worden geretourneerd in het venster **berichten** .

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure SQL-database ontwerpen](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md)
- [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview-versie)](sql-database-quickstart-r-create-script.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
