---
title: Een Synapse SQL-groep maken en opvragen (Azure-portal)
description: Een Synapsische SQL-groep maken en opvragen met de Azure-portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/28/2019
ms.author: Kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9605d20fa6a1480b24d7b64963aa9579ed3b5a11
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115183"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>Snelstart: een Synapsische SQL-groep maken en opvragen met behulp van de Azure-portal

Maak en query er snel een Synapse SQL-groep (datawarehouse) in Azure Synapse Analytics (voorheen SQL DW) met behulp van de Azure-portal.

## <a name="prerequisites"></a>Vereisten

1. Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

   > [!NOTE]
   > Het maken van een SQL-pool in Azure Synapse kan resulteren in een nieuwe factureerbare service. Zie [Azure Synapse Analytics-prijzen voor](https://azure.microsoft.com/pricing/details/synapse-analytics/)meer informatie.

2. Download en installeer de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Een SQL-groep maken

Gegevensmagazijnen worden gemaakt met SQL-pool in Azure Synapse Analytics. Er wordt een SQL-groep gemaakt met een gedefinieerde set [compute resources.](memory-concurrency-limits.md) De database wordt gemaakt in een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en in een [logische Azure SQL-server](../../sql-database/sql-database-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Volg deze stappen om een SQL-groep te maken die de **voorbeeldgegevens van AdventureWorksDW** bevat.

1. selecteer **Een resource maken** in de linkerbovenhoek van de Azure-portal.

   ![een resource maken in Azure-portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. Selecteer **Databases** op de pagina **Nieuw** en selecteer **Azure Synapse Analytics (voorheen SQL DW)** in de lijst **Aanbevolen.**

   ![leeg datawarehouse maken](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Geef **in Basics**uw abonnement, resourcegroep, SQL-groepnaam en servernaam op:

   | Instelling | Voorgestelde waarde | Beschrijving |
   | :------ | :-------------- | :---------- |
   | **Abonnement** | Uw abonnement | Zie [Abonnementen](https://account.windowsazure.com/Subscriptions) voor meer informatie over uw abonnementen. |
   | **Resourcegroep** | myResourceGroup | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Naamgevingsconventies) voor geldige namen van resourcegroepen. |
   | **SQL-poolnaam** | Elke wereldwijd unieke naam (een voorbeeld is *mySampleDataWarehouse)* | Zie [Database-id's voor](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)geldige databasenamen . Let op, een SQL-groep is één type database. |
   | **Server** | Een wereldwijd unieke naam | Selecteer bestaande server of maak een nieuwe servernaam en selecteer **Nieuw maken**. Zie [Naming conventions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Naamgevingsconventies) voor geldige servernamen. |

   ![basisgegevens van een gegevensmagazijn maken](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. Selecteer **Onder Prestatieniveau**selecteer **Prestatieniveau selecteren** om uw configuratie optioneel te wijzigen met een schuifregelaar.

   ![prestatieniveau voor gegevensmagazijnen wijzigen](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Zie Compute beheren in [Azure SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md)voor meer informatie over prestatieniveaus.

5. Nu u het tabblad Basisbeginselen van het Azure Synapse Analytics-formulier hebt voltooid, selecteert u **Controleren + Maken** en vervolgens **maken** om de SQL-groep te maken. De inrichting duurt een paar minuten.

   ![selecteren Controleren + Maken](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![Maken selecteren](./media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Selecteer op de werkbalk **Meldingen** om het implementatieproces te controleren.

   ![melding](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Een serverfirewallregel maken

De Azure Synapse-service maakt een firewall op serverniveau. Deze firewall voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server. Als u de connectiviteit wilt inschakelen, kunt u firewallregels toevoegen waarmee connectiviteit voor bepaalde IP-adressen wordt ingeschakeld. Volg deze stappen om een [firewallregel op serverniveau](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) te maken voor het IP-adres van uw client.

> [!NOTE]
> Azure Synapse communiceert via poort 1433. Als u verbinding wilt maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 1433 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 1433 openstelt.

1. Nadat de implementatie is voltooid, selecteert u **Alle services** in het linkermenu. Selecteer **Databases**, selecteer de ster naast **Azure Synapse Analytics** om Azure Synapse Analytics toe te voegen aan uw favorieten.

2. Selecteer **Azure Synapse Analytics** in het linkermenu en selecteer vervolgens **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics.** De overzichtspagina voor uw database wordt geopend, waarin u de volledig gekwalificeerde servernaam (zoals **sqlpoolservername.database.windows.net)** toont en opties biedt voor verdere configuratie.

3. Kopieer deze volledig gekwalificeerde servernaam voor gebruik om verbinding te maken met uw server en de databases in deze en andere snelle starts. Als u serverinstellingen wilt openen, selecteert u de servernaam.

   ![servernaam zoeken](./media/create-data-warehouse-portal/find-server-name.png)

4. Selecteer **Firewall-instellingen weergeven**.

   ![serverinstellingen](./media/create-data-warehouse-portal/server-settings.png)

5. De pagina **Firewall-instellingen** voor de SQL Database-server wordt geopend.

   ![serverfirewallregel](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Als u uw huidige IP-adres wilt toevoegen aan een nieuwe firewallregel, selecteert u **Client-IP toevoegen** op de werkbalk. Een firewallregel kan poort 1433 openen voor een afzonderlijk IP-adres of voor een aantal IP-adressen.

7. selecteer **Opslaan**. Er wordt een firewallregel op serverniveau gemaakt voor uw huidige IP-adres waarbij poort 1433 op de logische server wordt geopend.

8. selecteer **OK** en sluit vervolgens de pagina **Firewall-instellingen.**

U nu verbinding maken met de SQL-server en de SQL-pools met behulp van dit IP-adres. De verbinding werkt met SQL Server Management Studio of een ander hulpprogramma van uw keuze. Wanneer u verbinding maakt, gebruikt u het ServerAdmin-account dat u eerder hebt gemaakt.

> [!IMPORTANT]
> Voor alle Azure-services is toegang via de SQL Database-firewall standaard ingeschakeld. selecteer **UIT** op deze pagina en selecteer **Opslaan** om de firewall voor alle Azure-services uit te schakelen.

## <a name="get-the-fully-qualified-server-name"></a>De volledig gekwalificeerde servernaam ophalen

Haal de volledig gekwalificeerde servernaam van uw SQL-server op uit Azure Portal. Later gebruikt u de volledig gekwalificeerde servernaam bij het verbinding maken met de server.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Azure Synapse Analytics** in het linkermenu en selecteer uw op de pagina Azure **Synapse Analytics.**

3. In het deelvenster **Essentials** van de Azure Portal-pagina van uw database kopieert u de **servernaam**. In dit voorbeeld wordt de volledig gekwalificeerde naam sqlpoolservername.database.windows.net.

    ![verbindingsgegevens](./media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Als serverbeheerder verbinding maken met de server

In deze sectie wordt gebruikgemaakt van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio) om een verbinding tot stand te brengen met de Azure SQL-server.

1. Open SQL Server Management Studio.

2. Voer in het dialoogvenster **Verbinding maken met server** de volgende informatie in:

   | Instelling | Voorgestelde waarde | Beschrijving |
   | :------ | :-------------- | :---------- |
   | Servertype | Database-engine | Deze waarde is verplicht |
   | Servernaam | De volledig gekwalificeerde servernaam | Hier is een voorbeeld: **sqlpoolservername.database.windows.net**. |
   | Verificatie | SQL Server-verificatie | SQL-verificatie is het enige verificatietype dat in deze zelfstudie is geconfigureerd. |
   | Aanmelden | Het beheerdersaccount voor de server | Account dat u hebt opgegeven toen u de server maakte. |
   | Wachtwoord | Het wachtwoord voor het beheerdersaccount voor de server | Wachtwoord dat u hebt opgegeven toen u de server maakte. |
   ||||

   ![verbinding maken met server](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. selecteer **Verbinding maken**. Het venster Objectverkenner wordt geopend in SQL Server Management Studio.

4. Vouw **Databases** uit in Objectverkenner. Vouw vervolgens **mySampleDatabase** uit om de objecten in uw nieuwe database weer te geven.

   ![databaseobjecten](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Een aantal query's uitvoeren

SQL Data Warehouse maakt gebruik van T-SQL als querytaal. Gebruik de volgende stappen om een queryvenster te openen en een aantal T-SQL-query’s uit te voeren:

1. Selecteer **mySampleDataWarehouse** met de rechtermuisknop en selecteer **Nieuwe query**. Een nieuwe queryvenster wordt geopend.

2. Voer in het queryvenster de volgende opdracht in om een lijst met databases te zien.

    ```sql
    SELECT * FROM sys.databases
    ```

3. selecteer **Uitvoeren**. De queryresultaten bevatten twee databases: **hoofd** en **mySampleDataWarehouse**.

   ![Querydatabases](./media/create-data-warehouse-portal/query-databases.png)

4. Als u wat gegevens wilt bekijken, gebruikt u de volgende opdracht om het aantal klanten te zien met de achternaam Adams en met drie kinderen. De resultaten bestaan uit zes klanten.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Voer een query uit voor dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor gegevensmagazijnen en gegevens die uw SQL-groep zijn opgeslagen. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in opslag wilt houden, u de gegevensberekening onderbreken wanneer u de SQL-groep niet gebruikt. Door compute te pauzeren, worden alleen kosten in rekening gebracht voor gegevensopslag. U de gegevens hervatten wanneer u klaar bent om met de gegevens te werken.

- Als u toekomstige kosten wilt verwijderen, u de SQL-groep verwijderen.

Volg deze stappen om resources op te schonen die u niet meer nodig hebt.

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer uw SQL-groep.

   ![Resources opschonen](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Als u de berekening wilt onderbreken, selecteert u de knop **Onderbreken.** Wanneer de SQL-groep is onderbroken, ziet u een knop **Hervatten.** Als u de gegevens wilt hervatten, selecteert u **Hervatten**.

3. Als u de SQL-groep wilt verwijderen zodat er geen kosten in rekening worden gebracht voor compute of opslag, selecteert u **Verwijderen**.

4. Als u de SQL-server wilt verwijderen die u hebt gemaakt, selecteert u **sqlpoolservername.database.windows.net** in de vorige afbeelding en selecteert u **Verwijderen**. Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de brongroep wilt verwijderen, selecteert u **myResourceGroup**en selecteert u **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het laden van gegevens in uw SQL-groep verder naar het artikel [Gegevens laden in SQL-groep.](load-data-from-azure-blob-storage-using-polybase.md)
