---
title: 'SSMS: Verbindings- en querygegevens'
description: Ontdek hoe u verbinding maakt met SQL Database in Azure met behulp van SQL Server Management Studio (SSMS). Voer vervolgens T-SQL-instructies (Transact-SQL) uit om query's uit te voeren voor gegevens en om gegevens te bewerken.
keywords: verbinding maken met SQL-database,Sql Server Management Studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299291"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Snelstart: SQL Server Management Studio gebruiken om verbinding te maken en een Azure SQL-database op te vragen

In deze quickstart leert u hoe u SQL Server Management Studio (SSMS) gebruiken om verbinding te maken met een Azure SQL-database en enkele query's uit te voeren.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze snelstart zijn de volgende items nodig:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- De AdventureWorksLT voorbeelddatabase. Als u een werkende kopie van de AdventureWorksLT-database nodig hebt, maakt u er een door de [snelstart van de Azure SQL-database maken](sql-database-single-database-get-started.md) in te vullen.
    - De scripts in dit artikel zijn geschreven om de AdventureWorksLT database te gebruiken. Als u een beheerde instantie gebruikt, moet u de AdventureWorks-database importeren in een instantiedatabase of de scripts in dit artikel wijzigen om de database wide world-importeurs te gebruiken.

Zie [Quickstart: Gebruik de queryeditor van de Azure-portal om een SQL-database op te vragen](sql-database-connect-query-portal.md)als u gewoon ad-hocquery's wilt uitvoeren zonder SSMS te installeren.

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Krijg de verbindingsgegevens die u nodig hebt om verbinding te maken met uw database. Je hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig om deze quickstart te voltooien.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de **SQL-database** of **SQL-beheerde instantie die** u wilt opvragen.

3. Kopieer op de pagina **Overzicht** de volledig gekwalificeerde servernaam. Het is naast **servernaam** voor één database of de volledig gekwalificeerde servernaam naast **Host** voor een beheerde instantie. De volledig gekwalificeerde naam ziet eruit als: *servername.database.windows.net,* behalve dat het uw werkelijke servernaam heeft.

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

Maak in SSMS verbinding met uw Azure SQL Database-server.

> [!IMPORTANT]
> Een Azure SQL Database-server luistert naar poort 1433. Om verbinding te maken met een SQL Database-server achter een firewall van het bedrijf, moet de firewall voor deze poort zijn geopend.

1. Open SQL Server Management Studio.

2. Het dialoogvenster **Verbinding maken met server** wordt geopend. Voer de volgende informatie in:

   | Instelling      | Voorgestelde waarde    | Beschrijving |
   | ------------ | ------------------ | ----------- |
   | **Servertype** | Database-engine | Vereiste waarde. |
   | **Servernaam** | De volledig gekwalificeerde servernaam | Zoiets als: **servername.database.windows.net**. |
   | **Verificatie** | SQL Server-verificatie | In deze zelfstudie wordt gebruik gemaakt van SQL-verificatie. |
   | **Aanmelding** | Gebruikers-id voor het beheerdersaccount voor de server | De gebruikers-id van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   | **Wachtwoord** | Het wachtwoord voor het serverbeheerdersaccount | Het wachtwoord van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   ||||

   ![verbinding maken met server](./media/sql-database-connect-query-ssms/connect.png)  

3. Selecteer **Opties** in het dialoogvenster **Verbinding maken met server**. In de vervolgkeuzelijst **Verbinding maken met database** selecteert u **mySampleDatabase**. Als u de quickstart in de [sectie Voorwaarden voltooit,](#prerequisites) wordt een AdventureWorksLT-database met de naam mySampleDatabase gemaakt. Als uw werkkopie van de AdventureWorks-database een andere naam heeft dan mySampleDatabase, selecteert u deze in plaats daarvan.

   ![verbinding maken met database op server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selecteer **Verbinden**. Het venster Objectverkenner wordt geopend.

5. Als u de objecten van de database wilt weergeven, vouwt u **Databases** uit en vouwt u vervolgens het databaseknooppunt uit.

   ![mySampleDatabase-objecten](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Querygegevens

Voer deze Transact-SQL \[SELECT](https://msdn.microsoft.com/library/ms189499.aspx)-code uit om op categorie een query uit te voeren voor de twintig populairste producten.

1. Klik in Objectverkenner met de rechtermuisknop op **mySampleDatabase** en selecteer vervolgens **Nieuwe query**. Er wordt een nieuw queryvenster geopend dat is verbonden met uw database.

2. Plak in het queryvenster de volgende SQL-query:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Selecteer op de werkbalk **Uitvoeren** om de query `Product` `ProductCategory` uit te voeren en haal gegevens op uit de tabellen en tabellen.

    ![query om gegevens op te halen uit tabel Product en ProductCategorie](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Gegevens invoegen

Voer deze Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-code uit om een nieuw product te maken in de tabel `SalesLT.Product`.

1. Vervang de vorige query door deze.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Selecteer **Uitvoeren** om een nieuwe rij in te voegen in de tabel `Product`. In het deelvenster **Berichten** wordt **(1 rij beïnvloedt)** weergegeven.

#### <a name="view-the-result"></a>Het resultaat weergeven

1. Vervang de vorige query door deze.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecteer **Uitvoeren**. Het volgende resultaat wordt weergegeven.

   ![resultaat van producttabelquery](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Gegevens bijwerken

Voer deze [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-code uit om uw nieuwe product te wijzigen.

1. Vervang de vorige query door deze query die de eerder gemaakte nieuwe record retourneert:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` bij te werken. In het deelvenster **Berichten** wordt **(1 rij beïnvloedt)** weergegeven.

### <a name="delete-data"></a>Gegevens verwijderen

Voer deze Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)-code uit om uw nieuwe product te verwijderen.

1. Vervang de vorige query door deze.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` te verwijderen. In het deelvenster **Berichten** wordt **(1 rij beïnvloedt)** weergegeven.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Server Management Studio ](https://msdn.microsoft.com/library/ms174173.aspx) voor meer informatie over SSMS.
- Als u verbinding wilt maken en query's wilt uitvoeren met Azure Portal, raadpleegt u [Connect and query with the Azure Portal SQL query editor](sql-database-connect-query-portal.md) (Verbinding maken en query's uitvoeren met de SQL-query-editor in Azure Portal).
- Zie [Verbinding maken en query's uitvoeren met Visual Studio Code](sql-database-connect-query-vscode.md) als u verbinding wilt maken en query’s wilt uitvoeren met Visual Studio Code.
- Zie [Verbinding maken en query’s uitvoeren met .NET](sql-database-connect-query-dotnet.md) als u verbinding wilt maken en query’s wilt uitvoeren met .NET.
- Zie [Verbinding maken en query's uitvoeren met PHP](sql-database-connect-query-php.md) als u verbinding wilt maken en query's wilt uitvoeren met PHP.
- Zie [Verbinding maken en query's uitvoeren met Node.js](sql-database-connect-query-nodejs.md) als u verbinding wilt maken en query's wilt uitvoeren met Node.js.
- Zie [Verbinding maken en query's uitvoeren met Java](sql-database-connect-query-java.md) als u verbinding wilt maken en query's wilt uitvoeren met Java.
- Zie [Verbinding maken en query's uitvoeren met Python](sql-database-connect-query-python.md) als u verbinding wilt maken en query's wilt uitvoeren met Python.
- Zie [Verbinding maken en query's uitvoeren met Ruby](sql-database-connect-query-ruby.md) als u verbinding wilt maken en query's wilt uitvoeren met Ruby.
