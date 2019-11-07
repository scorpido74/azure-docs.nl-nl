---
title: 'Azure Portal: query-Azure SQL Database met query-editor '
description: Leer hoe u verbinding maakt met SQL Database in Azure Portal door gebruik te maken van de SQL-queryeditor. Voer daarna Transact-SQL-instructies (T-SQL) uit om query's uit te voeren voor gegevens en om gegevens te bewerken.
keywords: verbinding maken met sql database, azure portal, portal, queryeditor
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: 75882b13341c7fc373de141ba12f8806cb106906
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690922"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Quick Start: de SQL-query-editor van Azure Portal gebruiken om verbinding te maken en gegevens op te vragen

De SQL-queryeditor is een browsertool in de Azure-portal die een eenvoudige manier biedt om SQL-query's uit te voeren op uw Azure SQL Database of Azure SQL Data Warehouse. In deze snelstart gebruikt u de query-editor om verbinding te maken met een SQL-database en vervolgens Transact-SQL-instructies uit te voeren om gegevens te zoeken, in te voegen, bij te werken en te verwijderen.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure SQL-database. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren in Azure SQL Database:

  || Individuele database |
  |:--- |:--- |
  | Maken| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Configureren | [IP-firewallregel op serverniveau](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> De query-editor gebruikt de poorten 443 en 1443 om te communiceren.  Controleer of u het uitgaande HTTPS-verkeer op deze poorten hebt ingeschakeld. U moet ook uw uitgaande IP-adres toevoegen aan de toegestane firewall regels van de server om toegang te krijgen tot uw data bases en data warehouses.

## <a name="sign-in-the-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Verbinding maken met behulp van SQL-verificatie

1. Ga naar de Azure Portal om verbinding te maken met een SQL database. Zoek en selecteer **SQL-data bases**.

    ![Ga naar SQL database lijst Azure Portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. Selecteer uw SQL database.

    ![Selecteer een SQL database, Azure Portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. Selecteer in het menu **SQL database** de optie **query-editor (preview)** .

    ![queryeditor zoeken](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. Op de **aanmeldings** pagina, onder het label **SQL Server-verificatie** , voert u de **aanmeldings** -id en het **wacht woord** in van het Server beheerders account dat is gebruikt voor het maken van de data base. Selecteer vervolgens **OK**.

    ![aanmelden](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Verbinding maken met Azure Active Directory

Als u een Azure Active Directory-beheerder (Azure AD) configureert, kunt u een enkele identiteit gebruiken om u aan te melden bij de Azure Portal en uw SQL database. Volg de onderstaande stappen om een Azure AD-beheerder voor uw SQL-Server te configureren.

> [!NOTE]
> * E-mail accounts (bijvoorbeeld outlook.com, gmail.com, yahoo.com enzovoort) worden nog niet ondersteund als Azure AD-Administrators. Kies een gebruiker die ofwel systeemeigen is gemaakt in de Azure AD ofwel federatief in Azure AD.
> * Azure AD-beheerdersaanmelding werkt niet met accounts waarvoor tweeledige verificatie is ingeschakeld.

1. Selecteer **alle resources**in het menu Azure portal of op de **Start** pagina.

2. Selecteer uw SQL-Server.

3. Selecteer in het menu **SQL Server** onder **instellingen**de optie **Active Directory beheerder**.

4. Selecteer in de werk balk SQL Server **Active Directory-beheer** pagina de optie **beheerder instellen** en kies de gebruiker of groep als uw Azure AD-beheerder.

    ![active directory selecteren](./media/sql-database-connect-query-portal/select-active-directory.png)

5. Voer op de pagina **beheer toevoegen** in het zoekvak een gebruiker of groep in die u wilt zoeken, Selecteer deze als beheerder en klik vervolgens op de knop **selecteren** .

6. Selecteer in de werk balk van de SQL Server **Active Directory-beheer** pagina de optie **Opslaan**.

7. Selecteer **SQL-data bases**in het menu **SQL Server** en selecteer vervolgens uw SQL database.

8. Selecteer in het menu **SQL database** de optie **query-editor (preview)** . Op de **aanmeldings** pagina onder het label **Active Directory verificatie** wordt een bericht weer gegeven met de melding dat u bent aangemeld als u een Azure AD-beheerder bent. Selecteer vervolgens de knop **door gaan als** *\<uw gebruiker of groeps-id >* .

## <a name="view-data"></a>Gegevens weergeven

1. Nadat u bent geverifieerd, plakt u de volgende SQL in de queryeditor om de belangrijkste twintig producten op categorie op te halen.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Selecteer op de werkbalk de optie **Uitvoeren** en bekijk de uitvoer in het deelvenster **Resultaten**.

   ![resultaten queryeditor](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Gegevens invoegen

Voer de volgende Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-instructie uit om een nieuw product toe te voegen in de tabel `SalesLT.Product`.

1. Vervang de vorige query door de volgende.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Selecteer **Uitvoeren** om een nieuwe rij in te voegen in de tabel `Product`. In het deel venster **berichten** worden de **query geslaagd weer gegeven: betrokken rijen: 1**.


## <a name="update-data"></a>Gegevens bijwerken

Voer de volgende Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)-instructie uit om uw nieuwe product te wijzigen.

1. Vervang de vorige query door de volgende.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` bij te werken. In het deel venster **berichten** worden de **query geslaagd weer gegeven: betrokken rijen: 1**.

## <a name="delete-data"></a>Gegevens verwijderen

Gebruik de volgende Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)-instructie om uw nieuwe product te verwijderen.

1. Vervang de vorige query door de volgende:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` te verwijderen. In het deel venster **berichten** worden de **query geslaagd weer gegeven: betrokken rijen: 1**.


## <a name="query-editor-considerations"></a>Overwegingen met betrekking tot de queryeditor

U moet enkele dingen weten voordat u met de queryeditor gaat werken.

* De query-editor gebruikt de poorten 443 en 1443 om te communiceren.  Controleer of u het uitgaande HTTPS-verkeer op deze poorten hebt ingeschakeld. U moet ook uw uitgaande IP-adres toevoegen aan de toegestane firewall regels van de server om toegang te krijgen tot uw data bases en data warehouses.

* Wanneer u op F5 drukt, wordt de pagina van de queryeditor vernieuwd en gaan query's waaraan wordt gewerkt, verloren.

* De query-editor biedt geen ondersteuning voor het maken van verbinding met de `master`-database.

* Er is een time-out van vijf minuten voor uitvoering van de query.

* De queryeditor ondersteunt alleen cilindrische projectie voor geografiegegevenstypen.

* Er is geen ondersteuning voor IntelliSense voor databasetabellen en -weergaven. Het automatisch aanvullen van namen die al eerder zijn getypt, wordt wel ondersteund.


## <a name="next-steps"></a>Volgende stappen

Zie [Transact-SQL-verschillen oplossen tijdens migratie naar SQL Database](sql-database-transact-sql-information.md) voor informatie over de Transact-SQL die in Azure SQL-databases wordt ondersteund.
