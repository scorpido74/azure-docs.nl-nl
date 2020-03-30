---
title: R gebruiken met Machine Learning Services om een database op te vragen (voorbeeld)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In dit artikel ziet u hoe u een R-script gebruiken met Azure SQL Database Machine Learning Services om verbinding te maken met een Azure SQL-database en deze op te vragen met transact-SQL-instructies.
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
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768515"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Snelstart: R gebruiken met Machine Learning Services om een Azure SQL-database op te vragen (voorbeeld)

In deze quickstart gebruikt u R met Machine Learning Services om verbinding te maken met een Azure SQL-database en T-SQL-instructies te gebruiken om gegevens op te vragen.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een [Azure SQL-database](sql-database-single-database-get-started.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) met R ingeschakeld. [Registreer u voor de preview](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven om gebruik te maken van de **Adventure Works** database.

> [!NOTE]
> Tijdens de openbare preview zal Microsoft u aan boord nemen en machine learning inschakelen voor uw bestaande of nieuwe database, maar de beheerde optie voor het implementeren van instanties wordt momenteel niet ondersteund.

Machine Learning Services with R is een functie van Azure SQL-database die wordt gebruikt voor het uitvoeren van in-database R-scripts. Zie het [R-project](https://www.r-project.org/)voor meer informatie.

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

## <a name="create-code-to-query-your-sql-database"></a>Code maken om query's uit te voeren op uw SQL-database

1. Open **SQL Server Management Studio** en maak verbinding met uw SQL-database.

   Zie [Quickstart: SQL Server Management Studio gebruiken om verbinding te maken en een Azure SQL-database op te vragen](sql-database-connect-query-ssms.md)als u hulp nodig hebt bij het maken van verbinding.

1. Geef het volledige R-script door aan de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) opgeslagen procedure.

   Het script wordt `@script` doorgegeven door het argument. Alles in `@script` het argument moet een geldige R-code zijn.
   
   >[!IMPORTANT]
   >Voor de code in dit voorbeeld worden de voorbeeldgegevens gebruikt van AdventureWorksLT, die u als bron kunt kiezen bij het maken van uw database. Als in uw database andere gegevens staan, kunt u tabellen uit uw eigen database gebruiken in de SELECT-query. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Als er fouten optreden, kan dit zijn omdat de openbare preview van Machine Learning Services (met R) niet is ingeschakeld voor uw SQL-database. Zie [Bovenstaande voorwaarden.](#prerequisites)

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) opgeslagen procedure uit.

1. Controleer of de bovenste 20 rubrieken/productrijen worden geretourneerd in het venster **Berichten.**

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure SQL-database ontwerpen](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md)
- [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-quickstart-r-create-script.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-functions.md)
