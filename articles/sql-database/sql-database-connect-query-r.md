---
title: R gebruiken met Machine Learning Services voor het uitvoeren van een query op een Data Base (preview)
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
ROBOTS: NOINDEX
ms.openlocfilehash: 23dc784586e85b8cbdc816ac5f4d54556fc1ffb7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81456988"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Snelstartgids: met R met Machine Learning Services een query uitvoeren op een Azure SQL database (preview-versie)

In deze Quick Start gebruikt u R met Machine Learning Services om verbinding te maken met een Azure SQL database en T-SQL-instructies te gebruiken om gegevens op te vragen.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een [Azure-SQL database](sql-database-single-database-get-started.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) met R ingeschakeld.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven om de **Adventure Works** -data base te gebruiken.

Machine Learning Services met R is een functie van Azure SQL Database die wordt gebruikt voor het uitvoeren van een Data Base-R-script. Zie voor meer informatie het [R-project](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

## <a name="create-code-to-query-your-sql-database"></a>Code maken om query's uit te voeren op uw SQL-database

1. Open **SQL Server Management Studio** en maak verbinding met uw SQL database.

   Als u hulp nodig hebt bij het verbinding maken, raadpleegt u [Quick Start: SQL Server Management Studio gebruiken om verbinding te maken met een Azure-SQL database](sql-database-connect-query-ssms.md).

1. Geef het volledige R-script door aan de opgeslagen [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) -procedure.

   Het script wordt door gegeven via `@script` het argument. Alles binnen het `@script` argument moet een geldige R-code zijn.
   
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

- [Uw eerste Azure-SQL database ontwerpen](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md)
- [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview-versie)](sql-database-quickstart-r-create-script.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
