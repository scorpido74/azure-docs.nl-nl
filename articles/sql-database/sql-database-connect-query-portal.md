---
title: Een SQL-database opvragen met de queryeditor in de Azure-portal
description: Meer informatie over het gebruik van de Queryeditor om Transact-SQL-query's (T-SQL) uit te voeren tegen een Azure SQL-database.
keywords: verbinding maken met sql-database, sql-database, azure portal, portal, queryeditor
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
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985674"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Snelstart: de queryeditor van de Azure-portal gebruiken om een SQL-database op te vragen

De queryeditor is een hulpmiddel in de Azure-portal voor het uitvoeren van SQL-query's op uw Azure SQL-database of Azure SQL Data Warehouse. 

In deze quickstart gebruikt u de queryeditor om T-SQL-query's (Transact-SQL) uit te voeren tegen een Azure SQL-database.


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze quickstart is de AdventureWorksLT-voorbeelddatabase vereist. Als u geen werkende kopie van de AdventureWorksLT SQL-database hebt, wordt er met de volgende quickstart snel een gemaakt:

- [Snelstart: maak één Azure SQL-database met de Azure-portal, PowerShell of Azure CLI](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>Netwerkinstellingen configureren

Als u een van de volgende fouten in de queryeditor krijgt: *uw lokale netwerkinstellingen verhinderen mogelijk dat de queryeditor query's uitgeeft. Klik hier voor instructies over het configureren van uw netwerkinstellingen*of *een verbinding met de server kan niet worden gemaakt. Dit kan duiden op een probleem met uw lokale firewallconfiguratie of uw netwerkproxy-instellingen,* de volgende belangrijke informatie moet helpen oplossen:

> [!IMPORTANT]
> De queryeditor gebruikt poorten 443 en 1443 om te communiceren. Zorg ervoor dat u uitgaand HTTPS-verkeer op deze poorten hebt ingeschakeld. U moet ook [uw uitgaande IP-adres toevoegen aan de toegestane firewallregels](sql-database-server-level-firewall-rule.md) van de server om toegang te krijgen tot uw databases en gegevensmagazijnen.


## <a name="open-the-sql-database-query-editor"></a>De SQL Database Query Editor openen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en selecteer de SQL-database die u wilt opvragen.

2. Selecteer **queryeditor (voorbeeld)** in het menu **SQL-database** .

    ![queryeditor zoeken](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Een verbinding met de database tot stand brengen

Hoewel u bent aangemeld bij de portal, moet u nog steeds referenties verstrekken om toegang te krijgen tot de SQL-database. U verbinding maken met SQL-verificatie of Azure Active Directory om verbinding te maken met uw database.

### <a name="connect-using-sql-authentication"></a>Verbinding maken met behulp van SQL-verificatie

1. Voer op de **pagina Aanmelding** onder **SQL-serververificatie**een **aanmelding** en **wachtwoord** in voor een gebruiker die toegang heeft tot de database. Als u het niet zeker weet, gebruikt u de aanmelding en het wachtwoord voor de serverbeheerder van de server van de database.

    ![aanmelden](./media/sql-database-connect-query-portal/login-menu.png)

2. Selecteer **OK**.


### <a name="connect-using-azure-active-directory"></a>Verbinding maken met Azure Active Directory

Als u een Azure Active Directory-beheerder (Azure AD) configureert, u één identiteit gebruiken om u aan te melden bij de Azure-portal en uw SQL-database. Als u verbinding wilt maken met uw database via Azure AD, voert u de onderstaande stappen uit om een Azure AD-beheerder voor uw SQL-server te configureren.

> [!NOTE]
> * E-mailaccounts (bijvoorbeeld outlook.com, gmail.com, yahoo.com, enzovoort) worden nog niet ondersteund als Azure AD-beheerders. Kies een gebruiker die ofwel systeemeigen is gemaakt in de Azure AD ofwel federatief in Azure AD.
> * Azure AD-beheerdersaanmelding werkt niet met accounts waarvoor tweeledige verificatie is ingeschakeld.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Een Active Directory-beheerder instellen voor de databaseserver

1. Selecteer in de Azure-portal uw SQL-server.

2. Selecteer **Active Directory-beheerder**in het menu **SQL-server** .

3. Selecteer op de werkbalk van de **SQL-server Active Directory-beheerpagina** de optie **Beheerder instellen** en kies de gebruiker of groep als uw Azure AD-beheerder.

    ![active directory selecteren](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Voer op de pagina **Beheerder toevoegen** in het zoekvak een gebruiker of groep in die u wilt zoeken, selecteer deze als beheerder en kies de knop **Selecteren.**

5. Selecteer Opslaan op de werkbalk van de **ACTIVE Directory-beheerpagina** van DE **SQL-server**.

### <a name="connect-to-the-database"></a>Verbinding maken met de database

6. Selecteer **SQL-databases**in het **sql-servermenu** en selecteer vervolgens uw SQL-database.

7. Selecteer **queryeditor (voorbeeld)** in het menu **SQL-database** . Op de **pagina Aanmelding** wordt onder het **active directory-verificatielabel** een bericht weergegeven waarin staat dat u bent aangemeld als u een Azure AD-beheerder bent. Selecteer vervolgens de knop **Doorgaan als** * \<uw gebruikersnaam of groeps-id>.* Als de pagina aangeeft dat u niet bent ingelogd, moet u de pagina mogelijk vernieuwen.

## <a name="query-a-sql-database"></a>Een SQL-database opvragen

De volgende voorbeeldquery's moeten worden uitgevoerd tegen de AdventureWorksLT-voorbeelddatabase.

### <a name="run-a-select-query"></a>Een SELECT-query uitvoeren

1. Plak de volgende query in de queryeditor:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Selecteer **Uitvoeren** en controleer de uitvoer in het deelvenster **Resultaten.**

   ![resultaten queryeditor](./media/sql-database-connect-query-portal/query-editor-results.png)

3. Optioneel u de query opslaan als een .sql-bestand of de geretourneerde gegevens exporteren als een .json-, CSV- of .xml-bestand.

### <a name="run-an-insert-query"></a>Een inVOEGquery uitvoeren

Voer de volgende T-SQL-instructie [INVOEGEN](/sql/t-sql/statements/insert-transact-sql/) `SalesLT.Product` uit om een nieuw product in de tabel toe te voegen.

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


2. Selecteer **Uitvoeren** om een nieuwe rij in te voegen in de tabel `Product`. In het deelvenster **Berichten** wordt **query geslaagd: De aangetaste rijen: 1**.


### <a name="run-an-update-query"></a>Een update-query uitvoeren

Voer de [volgende](/sql/t-sql/queries/update-transact-sql/) Update T-SQL-instructie uit om uw nieuwe product te wijzigen.

1. Vervang de vorige query door deze.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` bij te werken. In het deelvenster **Berichten** wordt **query geslaagd: De aangetaste rijen: 1**.

### <a name="run-a-delete-query"></a>Een verwijderquery uitvoeren

Voer de volgende [Delete](/sql/t-sql/statements/delete-transact-sql/) T-SQL-instructie uit om uw nieuwe product te verwijderen.

1. Vervang de vorige query door deze:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` te verwijderen. In het deelvenster **Berichten** wordt **query geslaagd: De aangetaste rijen: 1**.


## <a name="query-editor-considerations"></a>Overwegingen met betrekking tot de queryeditor

U moet enkele dingen weten voordat u met de queryeditor gaat werken.

* De queryeditor gebruikt poorten 443 en 1443 om te communiceren. Zorg ervoor dat u uitgaand HTTPS-verkeer op deze poorten hebt ingeschakeld. U moet ook uw uitgaande IP-adres toevoegen aan de toegestane firewallregels van de server om toegang te krijgen tot uw databases en gegevensmagazijnen.

* Als u een Private Link-verbinding hebt, werkt de queryeditor zonder dat u het ip-adres client hoeft toe te voegen aan de SQL Database-firewall

* Als u **op F5** drukt, wordt de pagina met query-editor vernieuwd en gaat elke query die wordt uitgevoerd verloren.

* De query-editor biedt geen ondersteuning voor het maken van verbinding met de `master`-database.

* Er is een time-out van vijf minuten voor uitvoering van de query.

* De queryeditor ondersteunt alleen cilindrische projectie voor geografiegegevenstypen.

* Er is geen ondersteuning voor IntelliSense voor databasetabellen en -weergaven, maar de editor ondersteunt wel automatisch aanvullen op namen die al zijn getypt.




## <a name="next-steps"></a>Volgende stappen

Zie [Transact-SQL-verschillen oplossen tijdens de migratie naar SQL](sql-database-transact-sql-information.md)Database voor meer informatie over de Transact-SQL (T-SQL) die wordt ondersteund in Azure SQL-databases.
