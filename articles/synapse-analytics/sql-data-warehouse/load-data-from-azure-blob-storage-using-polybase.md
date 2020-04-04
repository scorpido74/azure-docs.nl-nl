---
title: 'Zelfstudie: Gegevens over New York Taxicab laden'
description: Zelfstudie maakt gebruik van Azure portal en SQL Server Management Studio om New York Taxicab-gegevens te laden vanuit een wereldwijde Azure-blob voor Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 741779e8328c38e544b1ad297e59155dab4e8c0d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633908"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Zelfstudie: Laad de gegevensset Van New York Taxicab

Deze zelfstudie gebruikt PolyBase om New York Taxicab-gegevens te laden van een wereldwijd Azure blob-opslagaccount. De zelfstudie gebruikt [Azure Portal](https://portal.azure.com) en [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) voor het volgende:

> [!div class="checklist"]
>
> * Een SQL-groep maken in de Azure-portal
> * Een serverfirewallregel ingesteld in Azure Portal
> * Verbinding maken met het datawarehouse met SMMS
> * Een gebruiker maken die wordt aangewezen om gegevens te laden
> * Externe tabellen maken voor gegevens in Azure Blob Storage
> * De instructie CTAS T-SQL gebruiken om gegevens in uw datawarehouse te laden
> * De voortgang van de gegevens weergeven terwijl deze worden geladen
> * Statistieken maken voor de nieuw geladen gegevens

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Download en installeer voordat u met deze zelfstudie begint de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio).

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Log in bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Een lege database maken

Er wordt een SQL-groep gemaakt met een gedefinieerde set [compute resources.](memory-concurrency-limits.md) De database wordt gemaakt in een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en in een [logische Azure SQL-server](../../sql-database/sql-database-features.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Volg deze stappen om een lege database te maken.

1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.

2. Selecteer **Databases** op de pagina **Nieuw** en selecteer **Azure Synapse Analytics** onder **Aanbevolen** op de pagina **Nieuw.**

    ![datawarehouse maken](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Vul het formulier in met de volgende gegevens:

   | Instelling            | Voorgestelde waarde       | Beschrijving                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Naam**            | mySampleDataWarehouse | Zie [Database-id's voor](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)geldige databasenamen . |
   | **Abonnement**   | Uw abonnement     | Zie [Abonnementen](https://account.windowsazure.com/Subscriptions) voor meer informatie over uw abonnementen. |
   | **Resourcegroep** | myResourceGroup       | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Naamgevingsconventies) voor geldige namen van resourcegroepen. |
   | **Bron selecteren**  | Lege database        | Geeft aan dat er een lege database wordt gemaakt. Opmerking: een datawarehouse is een type database. |

    ![datawarehouse maken](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Selecteer **Server** als u een nieuwe server voor de nieuwe database wilt maken en configureren. Vul het **nieuwe serverformulier** in met de volgende gegevens:

    | Instelling                | Voorgestelde waarde          | Beschrijving                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Servernaam**        | Een wereldwijd unieke naam | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Naamgevingsconventies) voor geldige servernamen. |
    | **Inloggen voor serverbeheerder** | Een geldige naam           | Zie [Database-id's voor](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)geldige aanmeldingsnamen . |
    | **Wachtwoord**           | Een geldig wachtwoord       | Uw wachtwoord moet uit minstens acht tekens bestaan en moet tekens bevatten uit drie van de volgende categorieën: hoofdletters, kleine letters, cijfers en niet-alfanumerieke tekens. |
    | **Locatie**           | Een geldige locatie       | Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor informatie over regio's . |

    ![databaseserver maken](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Kies **Selecteren**.

6. Selecteer **Prestatieniveau** om op te geven of het gegevensmagazijn Gen1 of Gen2 is en het aantal gegevensmagazijneenheden.

7. Selecteer VOOR deze zelfstudie SQL-pool **Gen2**. De schuifregelaar is standaard ingesteld op **DW1000c.**  Verplaats de regelaar omhoog en omlaag om te zien hoe dit werkt.

    ![prestaties configureren](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Selecteer **Toepassen**.
9. Selecteer in het inrichtingsblad een **collatie** voor de lege database. Gebruik voor deze zelfstudie de standaardwaarde. Zie [Collations](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (Sorteringen) voor meer informatie over sorteringen

10. Nu u het formulier hebt ingevuld, selecteert u **Maken** om de database in te richten. De inrichting duurt een paar minuten.

11. Selecteer op de werkbalk **Meldingen** om het implementatieproces te controleren.
  
     ![melding](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Een serverfirewallregel maken

Een firewall op serverniveau die voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server. Als u de connectiviteit wilt inschakelen, kunt u firewallregels toevoegen waarmee connectiviteit voor bepaalde IP-adressen wordt ingeschakeld.  Volg deze stappen om een [firewallregel op serverniveau](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) te maken voor het IP-adres van uw client.

> [!NOTE]
> SQL Database Warehouse communiceert via poort 1433. Als u verbinding wilt maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 1433 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 1433 openstelt.

1. Nadat de implementatie is voltooid, selecteert u **SQL-databases** in het linkermenu en selecteert u **mySampleDatabase** op de pagina **SQL-databases.** De overzichtspagina voor uw database wordt geopend, waarin u de volledig gekwalificeerde servernaam (zoals **mynewserver-20180430.database.windows.net)** toont en opties biedt voor verdere configuratie.

2. Kopieer deze volledig gekwalificeerde servernaam om in volgende Quick Starts verbinding te maken met de server en de bijbehorende databases. Selecteer vervolgens op de servernaam om serverinstellingen te openen.

    ![servernaam zoeken](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. Selecteer de servernaam om serverinstellingen te openen.

    ![serverinstellingen](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. Selecteer **Firewall-instellingen weergeven**. De pagina **Firewall-instellingen** voor de SQL Database-server wordt geopend.

    ![serverfirewallregel](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. Selecteer **IP van client toevoegen** op de werkbalk om uw huidige IP-adres aan een nieuwe firewallregel toe te voegen. Een firewallregel kan poort 1433 openen voor een afzonderlijk IP-adres of voor een aantal IP-adressen.

6. Selecteer **Opslaan**. Er wordt een firewallregel op serverniveau gemaakt voor uw huidige IP-adres waarbij poort 1433 op de logische server wordt geopend.

7. Selecteer **OK** en sluit de pagina **Firewallinstellingen**.

U kunt nu via dit IP-adres verbinding maken met de SQL-server en de bijbehorende datawarehouses. De verbinding werkt met SQL Server Management Studio of een ander hulpprogramma van uw keuze. Wanneer u verbinding maakt, gebruikt u het ServerAdmin-account dat u eerder hebt gemaakt.  

> [!IMPORTANT]
> Voor alle Azure-services is toegang via de SQL Database-firewall standaard ingeschakeld. Selecteer **UIT** op deze pagina en selecteer **Opslaan** om de firewall voor alle Azure-services uit te schakelen.

## <a name="get-the-fully-qualified-server-name"></a>De volledig gekwalificeerde servernaam ophalen

Haal de volledig gekwalificeerde servernaam van uw SQL-server op uit Azure Portal. Later gebruikt u de volledig gekwalificeerde servernaam bij het maken van verbinding met de server.

1. Log in bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Azure Synapse Analytics** in het linkermenu en selecteer uw database op de pagina Azure **Synapse Analytics.**
3. In het deelvenster **Essentials** van de Azure Portal-pagina van uw database kopieert u de **servernaam**. In dit voorbeeld wordt de volledig gekwalificeerde naam mynewserver-20180430.database.windows.net.

    ![verbindingsgegevens](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Als serverbeheerder verbinding maken met de server

In deze sectie wordt gebruikgemaakt van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio) om een verbinding tot stand te brengen met de Azure SQL-server.

1. Open SQL Server Management Studio.

2. Voer in het dialoogvenster **Verbinding maken met server** de volgende informatie in:

    | Instelling        | Voorgestelde waarde                            | Beschrijving                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Servertype    | Database-engine                            | Deze waarde is verplicht                                       |
    | Servernaam    | De volledig gekwalificeerde servernaam            | De naam moet zoiets als dit: **mynewserver-20180430.database.windows.net**. |
    | Verificatie | SQL Server-verificatie                  | SQL-verificatie is het enige verificatietype dat we in deze zelfstudie hebben geconfigureerd. |
    | Aanmelden          | Het beheerdersaccount voor de server                   | Dit is het account dat u hebt opgegeven tijdens het maken van de server. |
    | Wachtwoord       | Het wachtwoord voor het beheerdersaccount voor de server | Dit is het wachtwoord dat u hebt opgegeven tijdens het maken van de server. |

    ![verbinding maken met server](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Selecteer **Verbinden**. Het venster Objectverkenner wordt geopend in SQL Server Management Studio.

4. Vouw **Databases** uit in Objectverkenner. Vouw **Systeemdatabases** en **Hoofd** uit om de objecten in de hoofddatabase weer te geven.  Vouw **mySampleDatabase** uit om de objecten in uw nieuwe database weer te geven.

    ![databaseobjecten](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>Een gebruiker maken voor het laden van gegevens

De serverbeheerdersaccount is bedoeld voor het uitvoeren van beheerbewerkingen en is niet geschikt voor het uitvoeren van query's op gebruikersgegevens. Het laden van gegevens is een geheugenintensieve bewerking. Geheugenmaxima worden gedefinieerd op basis van de [geconfigureerde gegevensmagazijneenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) en [resourceklasse.](resource-classes-for-workload-management.md)

Het is raadzaam een aanmelding en gebruiker te maken die speciaal wordt toegewezen voor het laden van gegevens. Voeg vervolgens de ladende gebruiker toe aan een [bronklasse](resource-classes-for-workload-management.md). Hiermee wordt een maximale hoeveelheid geheugen ingesteld.

Omdat u momenteel bent aangemeld als serverbeheerder, kunt u aanmeldingen en gebruikers maken. Gebruik deze stappen om een aanmelding en gebruiker te maken met de naam **LoaderRC20**. Wijs de gebruiker vervolgens toe aan de bronklasse **staticrc20**.

1. Selecteer in SSMS het **volgende model** om een vervolgkeuzemenu weer te geven en kies **Nieuwe query**. Een nieuwe queryvenster wordt geopend.

    ![Nieuwe query in Hoofd](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Voer in het queryvenster deze T-SQL-opdrachten in om een aanmelding en een gebruiker te maken met de naam LoaderRC20, waarbij u uw eigen wachtwoord vervangt door 'een123STERKwachtwoord!'.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Selecteer **Uitvoeren**.

4. Klik met de rechtermuisknop op **mySampleDataWarehouse** en kies **Nieuwe query**. Er wordt een nieuw queryvenster geopend.  

    ![Nieuwe query op voorbeeld van datawarehouse](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Voer de volgende T-SQL-opdrachten in om een databasegebruiker met de naam LoaderRC20 te maken voor de aanmelding LoaderRC20. De tweede regel verleent de nieuwe gebruiker beheermachtigingen voor het nieuwe datawarehouse.  Deze machtigingen zijn vergelijkbaar met de machtigingen als u de gebruiker de eigenaar van de database maakt. De derde regel voegt de nieuwe gebruiker toe als lid van de [bronklasse](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Selecteer **Uitvoeren**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Verbinding maken met de server als de ladende gebruiker

De eerste stap voor het laden van gegevens bestaat uit aanmelding als LoaderRC20.  

1. Selecteer in Object Explorer het vervolgkeuzemenu **Verbinden** en selecteer **Databaseengine**. Het dialoogvenster **Verbinding maken met server** wordt geopend.

    ![Verbinding maken met nieuwe aanmelding](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Voer de volledig gekwalificeerde servernaam in en voer **LoaderRC20** als de aanmelding in.  Voer uw wachtwoord in voor LoaderRC20.

3. Selecteer **Verbinden**.

4. Wanneer de verbinding gereed is, ziet u twee serververbindingen in Objectverkenner. Eén verbinding als de serverbeheerder en één verbinding als MedRCLogin.

    ![Verbinding geslaagd](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Externe tabellen voor de voorbeeldgegevens maken

U bent klaar om te beginnen met het laden van gegevens in uw nieuwe datawarehouse. In deze zelfstudie ziet u hoe u externe tabellen gebruiken om taxigegevens van New York City te laden vanuit een Azure Storage-blob. Zie het [laadoverzicht](design-elt-data-loading.md)voor toekomstige informatie over hoe u uw gegevens naar Azure blob-opslag krijgen of rechtstreeks vanuit uw bron laden.

Voer de volgende SQL-scripts uit en geef informatie op over de gegevens die u wilt laden. Deze informatie omvat de locatie waar de gegevens zich bevinden, de indeling van de inhoud van de gegevens en de tabeldefinitie voor de gegevens.

1. In de vorige sectie hebt u zich bij uw datawarehouse aangemeld als LoaderRC20. Klik in SMMS met de rechtermuisknop op uw LoaderRC20-verbinding en selecteer **Nieuwe query**.  Er wordt een nieuw queryvenster geopend.

    ![Nieuw venster voor het laden van een query](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Vergelijk uw queryvenster met de vorige afbeelding.  Controleer of uw nieuwe queryvenster wordt uitgevoerd als LoaderRC20 en query's uitvoert op uw MySampleDataWarehouse-database. Gebruik dit queryvenster om alle laadstappen uit te voeren.

3. Maak een hoofdsleutel voor de MySampleDataWarehouse-database. U hoeft maar één hoofdsleutel per database te maken.

    ```sql
    CREATE MASTER KEY;
    ```

4. Voer de volgende instructie [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uit om de locatie van de Azure-blob te definiëren. Dit is de locatie van de externe taxigegevens.  Als u een opdracht wilt uitvoeren die u aan het queryvenster hebt toegevoegd, markeert u de opdrachten die u wilt uitvoeren en selecteert u **Uitvoeren**.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Voer de volgende T-SQL-instructie [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uit om opmaakeigenschappen en -opties voor het externe gegevensbestand op te geven. Deze instructie geeft aan dat de externe gegevens zijn opgeslagen als tekst en de waarden worden gescheiden door het pipe-teken ('| '). Het externe bestand wordt gecomprimeerd met Gzip.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS (
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );
    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

6. Voer de volgende instructie [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uit om een schema te maken voor de externe bestandsindeling. Het schema biedt een manier om de externe tabellen die u gaat maken te organiseren.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Maak de externe tabellen. De tabeldefinities worden opgeslagen in het gegevensmagazijn, maar de referentiegegevens van tabellen die zijn opgeslagen in Azure blob-opslag. Voer de volgende T-SQL-opdrachten uit om verschillende externe tabellen te maken die allemaal verwijzen naar de Azure-blob die eerder is gedefinieerd in de externe gegevensbron.

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;  
    CREATE EXTERNAL TABLE [ext].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. Vouw in Objectverkenner mySampleDataWarehouse uit om de lijst met externe tabellen die u zojuist hebt gemaakt te bekijken.

    ![Externe tabellen weergeven](./media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>De gegevens in uw datawarehouse laden

In deze sectie worden de externe tabellen gebruikt die u zojuist hebt gedefinieerd om de voorbeeldgegevens uit Azure Storage Blob te laden.  

> [!NOTE]
> In deze zelfstudie worden de gegevens rechtstreeks in de definitieve tabel geladen. In een productieomgeving gebruikt u meestal CREATE TABLE AS SELECT om naar een faseringstabel te laden. U kunt alle benodigde transformaties uitvoeren wanneer de gegevens zich in de faseringstabel bevinden. Als u de gegevens in de faseringstabel wilt toevoegen aan een productietabel, kunt u de instructie INSERT... SELECT gebruiken. Zie [Gegevens in een productietabel invoegen](guidance-for-loading-data.md#inserting-data-into-a-production-table) voor meer informatie.

Het script gebruikt de T-SQL-instructie [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om de gegevens uit Azure Storage Blob naar de nieuwe tabellen in het datawarehouse te laden. CTAS maakt een nieuwe tabel op basis van de resultaten van een SELECT-instructie. De nieuwe tabel heeft dezelfde gegevenstypen en kolommen als de resultaten van de selecteerinstructie. Wanneer de selecte instructie uit een externe tabel wordt geselecteerd, worden gegevens geïmporteerd in een relationele tabel in het gegevensmagazijn.

1. Voer het volgende script uit om de gegevens in de nieuwe tabellen in uw datawarehouse te laden.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. Bekijk uw gegevens tijdens het laden. U laadt verschillende GB's aan gegevens en comprimeert deze in sterk performante geclusterde kolomarchiefindexen. Voer de volgende query uit, die gebruikmaakt van dynamische beheerweergaven (DMV's) om de status van de belasting weer te geven.

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
    FROM
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc,
        gb_processed desc;
    ```

3. Bekijk alle systeemquery's.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Al uw gegevens zijn netjes geladen in uw datawarehouse.

    ![Geladen tabellen weergeven](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="authenticate-using-managed-identities-to-load-optional"></a>Verifiëren met beheerde identiteiten om te laden (optioneel)

Laden met PolyBase en authenticeren via beheerde identiteiten is het veiligste mechanisme en stelt u in staat om gebruik te maken van eindpunten van virtuele netwerkservices met Azure Storage.

### <a name="prerequisites"></a>Vereisten

1. Installeer Azure PowerShell met behulp van deze [handleiding](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Als u een v1- of blobopslagaccount voor algemene doeleinden hebt, moet u eerst upgraden naar v2 voor algemene doeleinden met behulp van deze [handleiding.](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
3. U moet **vertrouwde Microsoft-services toegang** hebben tot dit opslagaccount dat is ingeschakeld onder het instellingenmenu firewalls voor Azure Storage-account **Firewalls en Virtuele netwerken.** Raadpleeg deze [gids](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) voor meer informatie.

#### <a name="steps"></a>Stappen

1. Registreer in PowerShell **uw SQL-server** met Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Maak een **v2-opslagaccount voor algemene doeleinden** met deze [handleiding](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

   > [!NOTE]
   > Als u een v1- of blobopslagaccount voor algemene doeleinden hebt, moet u **eerst upgraden naar v2** met behulp van deze [handleiding.](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

3. Navigeer onder uw opslagaccount naar **Toegangsbeheer (IAM)** en selecteer **Roltoewijzing toevoegen**. Wijs **de RBAC-rol Storage Blob Data Contributor** toe aan uw SQL Database-server.

   > [!NOTE]
   > Alleen leden met eigenaarbevoegdheid kunnen deze stap uitvoeren. Raadpleeg deze [handleiding](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor verschillende ingebouwde rollen voor Azure-resources.
  
**Polybase-connectiviteit met het Azure Storage-account:**

1. Uw databasescoped referentie met **ID = 'Managed Service Identity':**

   ```SQL
   CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
   ```

   > [!NOTE]
   >
   > * Het is niet nodig om SECRET op te geven met azure storage-toegangssleutel, omdat dit mechanisme [Managed Identity](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) onder de covers gebruikt.
   > * De naam van de identiteit moet **'Managed Service Identity'** zijn voor PolyBase-connectiviteit om te werken met Azure Storage-account.

2. Maak de externe gegevensbron die de databasescopedreferentie met de beheerde service-identiteit opgeeft.

3. Query als normaal met behulp van [externe tabellen](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Raadpleeg de volgende [documentatie](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) als u eindpunten voor virtuele netwerkservices wilt instellen voor Azure Synapse Analytics.

## <a name="clean-up-resources"></a>Resources opschonen

Er kunnen kosten in rekening worden gebracht voor rekenresources en gegevens die in uw datawarehouse worden geladen. Deze worden afzonderlijk gefactureerd.

* Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u het datawarehouse niet gebruikt. Door de berekening te onderbreken, worden alleen kosten voor gegevensopslag in rekening gebracht en kunt u de berekening hervatten wanneer u weer met de gegevens wilt gaan werken.
* Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer uw gegevensmagazijn.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u de berekening wilt onderbreken, selecteert u de knop **Onderbreken.** Als het datawarehouse is onderbroken, ziet u de knop **Start**.  Als u de gegevens wilt hervatten, selecteert u **Start**.

3. Als u het gegevensmagazijn wilt verwijderen zodat er geen kosten in rekening worden gebracht voor rekenkracht of opslag, selecteert u **Verwijderen**.

4. Als u de SQL-server wilt verwijderen die u hebt gemaakt, selecteert u **mynewserver-20180430.database.windows.net** in de vorige afbeelding en selecteert u **Verwijderen**.  Wees hiermee voorzichtig. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de brongroep wilt verwijderen, selecteert u **myResourceGroup**en selecteert u **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een datawarehouse en een gebruiker voor het laden van gegevens maakt. U hebt externe tabellen gemaakt om de structuur te definiëren voor gegevens die zijn opgeslagen in Azure Storage Blob en vervolgens de PolyBase-instructie CREATE TABLE AS SELECT gebruikt voor het laden van gegevens in uw datawarehouse.

U hebt het volgende gedaan:
> [!div class="checklist"]
>
> * Een datawarehouse gemaakt in Azure Portal
> * Een serverfirewallregel ingesteld in Azure Portal
> * Verbinding gemaakt met het datawarehouse met SMMS
> * Een gebruiker gemaakt die wordt aangewezen om gegevens te laden
> * Externe tabellen gemaakt voor gegevens in Azure Storage Blob
> * De instructie CTAS T-SQL gebruikt om gegevens in uw datawarehouse te laden
> * De voortgang van de gegevens weergegeven terwijl deze werden geladen
> * Statistieken gemaakt voor de nieuw geladen gegevens

Ga verder naar het ontwikkelingsoverzicht voor meer informatie over het migreren van een bestaande database naar Azure Synapse Analytics.

> [!div class="nextstepaction"]
> [Ontwerpbeslissingen om een bestaande database te migreren naar Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)
