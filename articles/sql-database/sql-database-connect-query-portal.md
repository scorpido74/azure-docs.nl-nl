---
title: Query's uitvoeren op een SQL Database met behulp van de query editor in het Azure Portal
description: Meer informatie over het gebruik van de query editor om Transact-SQL-query's (T-SQL) uit te voeren op een Azure SQL Database.
keywords: verbinding maken met SQL data base, query's uitvoeren op SQL data base, Azure Portal, portal, query-editor
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: c072161db7a477b7973571a18d4f686b1b9c6202
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80985674"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Snelstartgids: de query-editor van de Azure Portal gebruiken om een SQL database

De query-editor is een hulp programma in de Azure Portal voor het uitvoeren van SQL-query's voor uw Azure SQL database of Azure SQL Data Warehouse. 

In deze Snelstartgids gebruikt u de query-editor om Transact-SQL-query's (T-SQL) uit te voeren op een Azure-SQL database.


## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids is de voorbeeld database AdventureWorksLT vereist. Als u geen werkende kopie van de AdventureWorksLT-SQL database hebt, maakt de volgende Quick start er snel een:

- [Snelstartgids: een enkele Azure-SQL database maken met behulp van de Azure Portal, Power shell of Azure CLI](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>Netwerkinstellingen configureren

Als u een van de volgende fouten in de query-editor krijgt: de *instellingen van uw lokale netwerk kunnen verhinderen dat de query-editor query's kan uitgeven. Klik hier voor instructies over het configureren van de netwerk instellingen*, of *om een verbinding met de server tot stand te brengen. Dit kan duiden op een probleem met uw lokale firewall configuratie of met de instellingen van uw netwerk proxy*, de volgende belang rijke informatie kan helpen bij het oplossen van:

> [!IMPORTANT]
> De query-editor gebruikt de poorten 443 en 1443 om te communiceren. Zorg ervoor dat u het uitgaande HTTPS-verkeer op deze poorten hebt ingeschakeld. U moet ook [uw uitgaande IP-adres toevoegen aan de toegestane firewall regels van de server](sql-database-server-level-firewall-rule.md) om toegang te krijgen tot uw data bases en data warehouses.


## <a name="open-the-sql-database-query-editor"></a>De SQL Database query-editor openen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en selecteer de SQL database die u wilt doorzoeken.

2. Selecteer in het menu **SQL database** de optie **query-editor (preview)**.

    ![queryeditor zoeken](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Verbinding maken met de data base

Hoewel u bent aangemeld bij de portal, moet u nog steeds referenties opgeven om toegang te krijgen tot de SQL database. U kunt verbinding maken met behulp van SQL-verificatie of Azure Active Directory om verbinding te maken met uw data base.

### <a name="connect-using-sql-authentication"></a>Verbinding maken met behulp van SQL-verificatie

1. Op de **aanmeldings** pagina, onder **SQL Server-verificatie**, voert u een **aanmeldings naam** en **wacht woord** in voor een gebruiker die toegang heeft tot de data base. Als u het niet zeker weet, gebruikt u de aanmelding en het wacht woord voor de server beheerder van de server van de data base.

    ![aanmelden](./media/sql-database-connect-query-portal/login-menu.png)

2. Selecteer **OK**.


### <a name="connect-using-azure-active-directory"></a>Verbinding maken met Azure Active Directory

Als u een Azure Active Directory-beheerder (Azure AD) configureert, kunt u een enkele identiteit gebruiken om u aan te melden bij de Azure Portal en uw SQL database. Als u verbinding wilt maken met uw data base met behulp van Azure AD, volgt u de onderstaande stappen om een Azure AD-beheerder voor uw SQL-Server te configureren.

> [!NOTE]
> * E-mail accounts (bijvoorbeeld outlook.com, gmail.com, yahoo.com enzovoort) worden nog niet ondersteund als Azure AD-Administrators. Kies een gebruiker die ofwel systeemeigen is gemaakt in de Azure AD ofwel federatief in Azure AD.
> * Azure AD-beheerdersaanmelding werkt niet met accounts waarvoor tweeledige verificatie is ingeschakeld.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Een Active Directory beheerder voor de database server instellen

1. Selecteer uw SQL-Server in het Azure Portal.

2. Selecteer in het menu **SQL Server** de optie **Active Directory beheerder**.

3. Selecteer op de werk balk SQL Server **Active Directory-beheer** pagina de optie **beheerder instellen** en kies de gebruiker of groep als uw Azure AD-beheerder.

    ![active directory selecteren](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Voer op de pagina **beheer toevoegen** in het zoekvak een gebruiker of groep in die u wilt zoeken, Selecteer deze als beheerder en klik vervolgens op de knop **selecteren** .

5. Selecteer in de werk balk van de SQL Server **Active Directory-beheer** pagina de optie **Opslaan**.

### <a name="connect-to-the-database"></a>Verbinding maken met de database

6. Selecteer **SQL-data bases**in het menu **SQL Server** en selecteer vervolgens uw SQL database.

7. Selecteer in het menu **SQL database** de optie **query-editor (preview)**. Op de **aanmeldings** pagina onder het label **Active Directory verificatie** wordt een bericht weer gegeven met de melding dat u bent aangemeld als u een Azure AD-beheerder bent. Selecteer vervolgens de knop **door gaan als** * \<gebruiker of groeps-id>* . Als op de pagina wordt aangegeven dat u niet bent aangemeld, moet u de pagina mogelijk vernieuwen.

## <a name="query-a-sql-database"></a>Query's uitvoeren op een SQL database

De volgende voorbeeld query's moeten worden uitgevoerd op basis van de voorbeeld database AdventureWorksLT.

### <a name="run-a-select-query"></a>Een selectie query uitvoeren

1. Plak de volgende query in de query-editor:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Selecteer **uitvoeren** en controleer de uitvoer in het **resultaten** venster.

   ![resultaten queryeditor](./media/sql-database-connect-query-portal/query-editor-results.png)

3. U kunt de query ook opslaan als een. SQL-bestand of de geretourneerde gegevens exporteren als een JSON-, CSV-of XML-bestand.

### <a name="run-an-insert-query"></a>Een INSERT-query uitvoeren

Voer de volgende [Insert](/sql/t-sql/statements/insert-transact-sql/) T-SQL-instructie uit om een nieuw product in `SalesLT.Product` de tabel toe te voegen.

1. Vervang de vorige query door deze.

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


### <a name="run-an-update-query"></a>Een BIJWERK query uitvoeren

Voer de volgende [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL-instructie uit om het nieuwe product te wijzigen.

1. Vervang de vorige query door deze.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` bij te werken. In het deel venster **berichten** worden de **query geslaagd weer gegeven: betrokken rijen: 1**.

### <a name="run-a-delete-query"></a>Een Verwijder query uitvoeren

Voer de volgende T-SQL-instructie [verwijderen](/sql/t-sql/statements/delete-transact-sql/) uit om het nieuwe product te verwijderen.

1. Vervang de vorige query door deze:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` te verwijderen. In het deel venster **berichten** worden de **query geslaagd weer gegeven: betrokken rijen: 1**.


## <a name="query-editor-considerations"></a>Overwegingen met betrekking tot de queryeditor

U moet enkele dingen weten voordat u met de queryeditor gaat werken.

* De query-editor gebruikt de poorten 443 en 1443 om te communiceren. Zorg ervoor dat u het uitgaande HTTPS-verkeer op deze poorten hebt ingeschakeld. U moet ook uw uitgaande IP-adres toevoegen aan de toegestane firewall regels van de server om toegang te krijgen tot uw data bases en data warehouses.

* Als u verbinding met een persoonlijke koppeling hebt, werkt de query-editor zonder dat u het IP-adres van de client hoeft toe te voegen aan de firewall van SQL Database

* Als u op **F5** drukt, wordt de pagina Query-Editor vernieuwd en wordt een query die wordt bewerkt, verloren gegaan.

* De query-editor biedt geen ondersteuning voor het maken van verbinding met de `master`-database.

* Er is een time-out van vijf minuten voor uitvoering van de query.

* De queryeditor ondersteunt alleen cilindrische projectie voor geografiegegevenstypen.

* Er is geen ondersteuning voor IntelliSense voor database tabellen en-weer gaven, maar de editor ondersteunt automatisch aanvullen op namen die al zijn getypt.




## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de Transact-SQL (T-SQL) die wordt ondersteund in Azure SQL-data bases, raadpleegt [u Transact-SQL-verschillen oplossen tijdens de migratie naar SQL database](sql-database-transact-sql-information.md).
