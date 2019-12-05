---
title: 'Snelstartgids: een Data Warehouse maken en er een query op uitvoeren Azure Portal'
description: Maak en zoek een query op een Data Warehouse met Azure SQL Data Warehouse in het Azure Portal.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4ae1d9ce8a4683f8d55962843fb1070ef24b3a87
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815797"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Snelstartgids: een Azure SQL Data Warehouse in het Azure Portal maken en doorzoeken

U kunt snel een Azure SQL Data Warehouse maken en er query's op uitvoeren met behulp van de Azure Portal.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!NOTE]
> Het maken van een SQL Data Warehouse kan leiden tot een nieuwe factureerbare service. Zie [Prijzen van SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Download en installeer de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-data-warehouse"></a>Een datawarehouse maken

Een Azure SQL Data Warehouse wordt gemaakt met een gedefinieerde set [reken resources](memory-concurrency-limits.md). De database wordt gemaakt in een [Azure-resourcegroep](../azure-resource-manager/resource-group-overview.md) en in een [logische Azure SQL-server](../sql-database/sql-database-logical-servers.md). 

Volg deze stappen om een SQL Data Warehouse te maken die de voorbeeld gegevens van AdventureWorksDW bevat. 

1. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.

2. Selecteer op de pagina **Nieuw** de optie **Databases**, en selecteer onder **Aanbevolen** op de pagina **Nieuw** de optie **SQL Data Warehouse**.

    ![leeg datawarehouse maken](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Vul het SQL Data Warehouse-formulier in met de volgende gegevens:

    | Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Abonnement** | Uw abonnement | Zie [Abonnementen](https://account.windowsazure.com/Subscriptions) voor meer informatie over uw abonnementen. |
    | **Resourcegroep** | myResourceGroup | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen van resourcegroepen. |
    | **Naam van Data Warehouse** | mySampleDataWarehouse | Zie [Database-id's](/sql/relational-databases/databases/database-identifiers) voor geldige databasenamen. Opmerking: een datawarehouse is een type database.|
    ||||

    ![datawarehouse maken](media/create-data-warehouse-portal/select-sample.png)

4. Selecteer een bestaande **Server** of klik op **Nieuw maken** om een nieuwe server voor uw nieuwe Data Base te maken en te configureren. Vul het **nieuwe serverformulier** in met de volgende gegevens: 

    | Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Servernaam** | Een wereldwijd unieke naam | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige servernamen. |
    | **Aanmeldgegevens van serverbeheerder** | Een geldige naam | Zie [Database-id's](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) voor geldige aanmeldingsnamen.|
    | **Wachtwoord** | Een geldig wachtwoord | Uw wachtwoord moet uit minstens acht tekens bestaan en moet tekens bevatten uit drie van de volgende categorieën: hoofdletters, kleine letters, cijfers en niet-alfanumerieke tekens. |
    | **Locatie** | Een geldige locatie | Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's. |
    ||||

    ![databaseserver maken](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Klik op **Selecteren**.

6. Klik op **prestatie niveau** om de prestatie configuratie voor het Data Warehouse op te geven.

7. Voor deze zelf studie selecteert u **Gen2**. De schuif regelaar wordt standaard ingesteld op **DW1000c**. Verplaats de regelaar omhoog en omlaag om te zien hoe dit werkt. 

    ![prestaties configureren](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Klik op **Apply** (Toepassen).

9. Nu u het tabblad basis principes van het SQL Data Warehouse formulier hebt voltooid, klikt u op **controleren + maken** om de data base in te richten. De inrichting duurt een paar minuten.

    ![klik op Maken](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Klik op de werkbalk op **Meldingen** om het implementatieproces te bewaken.
    
     ![melding](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Een serverfirewallregel maken

De SQL Data Warehouse-service maakt een firewall op server niveau. Deze firewall voor komt dat externe toepassingen en hulpprogram ma's verbinding maken met de server of data bases op de server. Als u de connectiviteit wilt inschakelen, kunt u firewallregels toevoegen waarmee connectiviteit voor bepaalde IP-adressen wordt ingeschakeld. Volg deze stappen om een [firewallregel op serverniveau](../sql-database/sql-database-firewall-configure.md) te maken voor het IP-adres van uw client.

> [!NOTE]
> SQL Database Warehouse communiceert via poort 1433. Als u verbinding wilt maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 1433 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 1433 openstelt.

1. Nadat de implementatie is voltooid, selecteert u **alle services** in het linkermenu. Selecteer **data bases**, selecteer de ster naast **SQL data warehouses** om SQL data warehouses toe te voegen aan uw favorieten.
1. Selecteer **SQL data warehouses** in het menu aan de linkerkant en klik vervolgens op **mySampleDataWarehouse** op de pagina **SQL data warehouses** . De overzichts pagina voor de data base wordt geopend, met de volledig gekwalificeerde server naam (zoals **mynewserver-20180430.database.Windows.net**) en biedt opties voor verdere configuratie.
1. Kopieer deze volledig gekwalificeerde server naam om te gebruiken om verbinding te maken met uw server en de bijbehorende data bases in deze en andere Quick starts. Klik op de servernaam om de serverinstellingen te openen.

   ![servernaam zoeken](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

1. Klik op **Firewallinstellingen weergeven**.

   ![serverinstellingen](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

1. De pagina **Firewallinstellingen** voor de SQL Database-server wordt geopend.

   ![serverfirewallregel](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

1. Klik op **IP-adres van client toevoegen** op de werkbalk om uw huidige IP-adres toe te voegen aan een nieuwe firewallregel. Een firewallregel kan poort 1433 openen voor een afzonderlijk IP-adres of voor een aantal IP-adressen.

1. Klik op **Opslaan**. Er wordt een firewallregel op serverniveau gemaakt voor uw huidige IP-adres waarbij poort 1433 op de logische server wordt geopend.

1. Klik op **OK** en sluit de pagina **Firewallinstellingen**.

U kunt nu via dit IP-adres verbinding maken met de SQL-server en de bijbehorende datawarehouses. De verbinding werkt met SQL Server Management Studio of een ander hulpprogramma van uw keuze. Wanneer u verbinding maakt, gebruikt u het ServerAdmin-account dat u eerder hebt gemaakt.

> [!IMPORTANT]
> Voor alle Azure-services is toegang via de SQL Database-firewall standaard ingeschakeld. Klik op **UIT** op deze pagina en klik vervolgens op **Opslaan** om de firewall uit te schakelen voor alle Azure-services.

## <a name="get-the-fully-qualified-server-name"></a>De volledig gekwalificeerde servernaam ophalen

Haal de volledig gekwalificeerde servernaam van uw SQL-server op uit Azure Portal. Later gebruikt u de volledig gekwalificeerde servernaam bij het verbinding maken met de server.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **SQL data warehouses** in het menu links en klik op uw data warehouse op de pagina **SQL data warehouses** .
3. In het deelvenster **Essentials** van de Azure Portal-pagina van uw database kopieert u de **servernaam**. In dit voor beeld is de volledig gekwalificeerde naam mynewserver-20180430.database.windows.net.

    ![verbindingsgegevens](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Als serverbeheerder verbinding maken met de server

In deze sectie wordt gebruikgemaakt van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio) om een verbinding tot stand te brengen met de Azure SQL-server.

1. Open SQL Server Management Studio.

2. Voer in het dialoogvenster **Verbinding maken met server** de volgende informatie in:

   | Instelling | Voorgestelde waarde | Beschrijving |
   | :------ | :-------------- | :---------- |
   | Servertype | Database-engine | Deze waarde is verplicht |
   | Servernaam | De volledig gekwalificeerde servernaam | Hier volgt een voor beeld: **mynewserver-20180430.database.Windows.net**. |
   | Verificatie | SQL Server-verificatie | SQL-verificatie is het enige verificatietype dat in deze zelfstudie is geconfigureerd. |
   | Aanmelden | Het beheerdersaccount voor de server | Account dat u hebt opgegeven tijdens het maken van de server. |
   | Wachtwoord | Het wachtwoord voor het beheerdersaccount voor de server | Het wacht woord dat u hebt opgegeven tijdens het maken van de server. |
   ||||

    ![verbinding maken met server](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Klik op **Verbinden**. Het venster Objectverkenner wordt geopend in SQL Server Management Studio. 

4. Vouw **Databases** uit in Objectverkenner. Vouw vervolgens **mySampleDatabase** uit om de objecten in uw nieuwe database weer te geven.

    ![databaseobjecten](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Een aantal query's uitvoeren

SQL Data Warehouse maakt gebruik van T-SQL als querytaal. Gebruik de volgende stappen om een queryvenster te openen en een aantal T-SQL-query’s uit te voeren:

1. Klik met de rechtermuisknop op **mySampleDataWarehouse** en selecteer **Nieuwe query**. Een nieuwe queryvenster wordt geopend.
2. Voer in het queryvenster de volgende opdracht in om een lijst met databases te zien.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Klik op **Execute** (Uitvoeren). De queryresultaten bevatten twee databases: **hoofd** en **mySampleDataWarehouse**.

    ![Querydatabases](media/create-data-warehouse-portal/query-databases.png)

4. Als u wat gegevens wilt bekijken, gebruikt u de volgende opdracht om het aantal klanten te zien met de achternaam Adams en met drie kinderen. De resultaten bestaan uit zes klanten. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![Voer een query uit voor dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en gegevens die zijn opgeslagen in uw data warehouse. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u het datawarehouse niet gebruikt. Door Compute te onderbreken, worden er alleen kosten in rekening gebracht voor gegevens opslag. U kunt de berekening hervatten wanneer u klaar bent om met de gegevens te werken.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om resources op te schonen die u niet meer nodig hebt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op uw datawarehouse.

    ![Resources opschonen](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. Wanneer het Data Warehouse is onderbroken, ziet u een knop **hervatten** . Als u de berekening wilt hervatten, klikt u op **hervatten**.

3. Als u het Data Warehouse wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **verwijderen**.

4. Als u de door u gemaakte SQL-Server wilt verwijderen, klikt u op **mynewserver-20180430.database.Windows.net** in de vorige afbeelding en klikt u vervolgens op **verwijderen**. Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een Data Warehouse gemaakt, een firewall regel gemaakt die is verbonden met uw data warehouse en enkele query's uitgevoerd. Voor meer informatie over Azure SQL Data Warehouse gaat u verder met de zelfstudie voor het laden van gegevens.

> [!div class="nextstepaction"]
> [Gegevens in een SQL Data Warehouse laden](load-data-from-azure-blob-storage-using-polybase.md)
