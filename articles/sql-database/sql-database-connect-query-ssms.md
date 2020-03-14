---
title: 'SSMS: verbinding maken en gegevens opvragen'
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
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299291"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Snelstartgids: SQL Server Management Studio gebruiken om verbinding te maken met een Azure-SQL database

In deze Quick Start leert u hoe u SQL Server Management Studio (SSMS) kunt gebruiken om verbinding te maken met een Azure SQL database en enkele query's uit te voeren.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Quick start zijn de volgende items vereist:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- De voorbeeld database AdventureWorksLT. Als u een werkende kopie van de AdventureWorksLT-data base nodig hebt, maakt u er een door de Snelstartgids voor het [maken van Azure SQL database](sql-database-single-database-get-started.md) te volt ooien.
    - De scripts in dit artikel zijn geschreven om de AdventureWorksLT-data base te gebruiken. Als u een beheerd exemplaar gebruikt, moet u de AdventureWorks-data base importeren in een exemplaar database of de scripts in dit artikel aanpassen om de Wide World Importers-data base te gebruiken.

Als u ad-hoc-query's alleen wilt uitvoeren zonder SSMS te installeren, raadpleegt u [Quick Start: de query-editor van Azure Portal gebruiken om een SQL database op te vragen](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Verbindingsgegevens voor SQL Server ophalen

Haal de verbindings gegevens op die u nodig hebt om verbinding te maken met uw data base. U hebt de volledig gekwalificeerde server naam of hostnaam, database naam en aanmeldings gegevens nodig om deze Quick Start te volt ooien.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Navigeer naar het **SQL database** of **SQL Managed instance** dat u wilt doorzoeken.

3. Kopieer op de pagina **overzicht** de volledig gekwalificeerde server naam. Het is naast **Server naam** voor één data base of de volledig gekwalificeerde server naam naast **host** voor een beheerd exemplaar. De volledig gekwalificeerde naam ziet er als volgt uit: *servername.database.Windows.net*, met uitzonde ring van de werkelijke server naam.

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

Maak in SSMS verbinding met uw Azure SQL Database-Server.

> [!IMPORTANT]
> Een Azure SQL Database-server luistert naar poort 1433. Om verbinding te maken met een SQL Database-server achter een firewall van het bedrijf, moet de firewall voor deze poort zijn geopend.

1. Open SQL Server Management Studio.

2. Het dialoogvenster **Verbinding maken met server** wordt geopend. Voer de volgende informatie in:

   | Instelling      | Voorgestelde waarde    | Beschrijving |
   | ------------ | ------------------ | ----------- |
   | **Servertype** | Database-engine | Vereiste waarde. |
   | **Servernaam** | De volledig gekwalificeerde servernaam | Wat lijkt: **servername.database.Windows.net**. |
   | **Verificatie** | SQL Server-verificatie | In deze zelfstudie wordt gebruik gemaakt van SQL-verificatie. |
   | **Aanmelding** | Gebruikers-id voor het beheerdersaccount voor de server | De gebruikers-id van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   | **Wachtwoord** | Het wachtwoord voor het serverbeheerdersaccount | Het wachtwoord van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   ||||

   ![verbinding maken met server](./media/sql-database-connect-query-ssms/connect.png)  

3. Selecteer **Opties** in het dialoogvenster **Verbinding maken met server**. In de vervolgkeuzelijst **Verbinding maken met database** selecteert u **mySampleDatabase**. Als u de Snelstartgids in het [gedeelte vereisten](#prerequisites) uitvoert, maakt u een AdventureWorksLT-data base met de naam mySampleDatabase. Als uw werkende kopie van de AdventureWorks-Data Base een andere naam heeft dan mySampleDatabase, selecteert u deze in plaats daarvan.

   ![verbinding maken met database op server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selecteer **Verbinden**. Het venster Objectverkenner wordt geopend.

5. Vouw **Data** bases uit en vouw vervolgens het knoop punt data base uit om de objecten in de Data Base weer te geven.

   ![mySampleDatabase-objecten](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Querygegevens

Voer deze Transact-SQL \[SELECT](https://msdn.microsoft.com/library/ms189499.aspx)-code uit om op categorie een query uit te voeren voor de twintig populairste producten.

1. Klik in Objectverkenner met de rechtermuisknop op **mySampleDatabase** en selecteer vervolgens **Nieuwe query**. Er wordt een nieuw queryvenster geopend dat is verbonden met uw database.

2. Plak in het query venster de volgende SQL-query:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Selecteer **uitvoeren** op de werk balk om de query uit te voeren en gegevens op te halen uit de tabellen `Product` en `ProductCategory`.

    ![query om gegevens op te halen uit tabel product en ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Gegevens invoegen

Voer deze Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-code uit om een nieuw product te maken in de tabel `SalesLT.Product`.

1. Vervang de vorige query door de volgende.

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

1. Vervang de vorige query door de volgende.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecteer **Uitvoeren**. Het volgende resultaat wordt weer gegeven.

   ![resultaat van query van product tabel](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Gegevens bijwerken

Voer deze [Update](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-code uit om het nieuwe product te wijzigen.

1. Vervang de vorige query door deze die de nieuwe record retourneert die u eerder hebt gemaakt:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` bij te werken. In het deelvenster **Berichten** wordt **(1 rij beïnvloedt)** weergegeven.

### <a name="delete-data"></a>Gegevens verwijderen

Voer deze Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)-code uit om uw nieuwe product te verwijderen.

1. Vervang de vorige query door de volgende.

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
