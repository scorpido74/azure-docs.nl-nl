---
title: Verbinding maken met Azure Cosmos DB met behulp van BI-analysetools
description: Meer informatie over het gebruik van het Azure Cosmos DB ODBC-stuurprogramma om tabellen en weergaven te maken, zodat genormaliseerde gegevens kunnen worden weergegeven in BI- en data-analysesoftware.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721078"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Verbinding maken met Azure Cosmos DB met behulp van BI-analysetools met het ODBC-stuurprogramma

Met het Azure Cosmos DB ODBC-stuurprogramma u verbinding maken met Azure Cosmos DB met behulp van BI-analysetools zoals SQL Server Integration Services, Power BI Desktop en Tableau, zodat u in die oplossingen visualisaties van uw Azure DB Cosmos-gegevens analyseren en maken.

Het ODBC-stuurprogramma van Azure Cosmos DB is compatibel met ODBC 3.8 en ondersteunt ANSI SQL-92-syntaxis. Het stuurprogramma biedt uitgebreide functies waarmee u gegevens in Azure Cosmos DB opnieuw kunt normaliseren. Met behulp van het stuurprogramma kunt u gegevens in Azure Cosmos DB weergeven als tabellen en weergaven. Met het stuurprogramma kunt u SQL-bewerkingen uitvoeren op de tabellen en weergaven, zoals groeperen op query's, invoegingen, updates en verwijderingen.

> [!NOTE]
> Verbinding maken met Azure Cosmos DB met het ODBC-stuurprogramma wordt momenteel alleen ondersteund voor Azure Cosmos DB SQL API-accounts.

## <a name="why-do-i-need-to-normalize-my-data"></a>Waarom moet ik mijn gegevens normaliseren?
Azure Cosmos DB is een schemaloze database, die snelle applicatieontwikkeling en de mogelijkheid om te herhalen op gegevensmodellen mogelijk maakt zonder te worden beperkt tot een strikt schema. Eén Azure Cosmos-database kan JSON-documenten van verschillende structuren bevatten. Dit is ideaal voor snelle applicatieontwikkeling, maar wanneer u rapporten van uw gegevens wilt analyseren en maken met behulp van gegevensanalyse en BI-tools, moeten de gegevens vaak worden afgevlakt en moeten ze zich houden aan een specifiek schema.

Dit is waar de ODBC bestuurder komt in. Door het ODBC-stuurprogramma te gebruiken, u gegevens in Azure Cosmos DB nu opnieuw normaliseren in tabellen en weergaven die passen bij uw gegevensanalyse- en rapportagebehoeften. De gerenormaliseerde schema's hebben geen invloed op de onderliggende gegevens en beperken ontwikkelaars niet om zich eraan te houden. In plaats daarvan u odbc-compatibele tools gebruiken om toegang te krijgen tot de gegevens. Dus nu is uw Azure Cosmos-database niet alleen een favoriet voor uw ontwikkelingsteam, maar uw gegevensanalisten zullen het ook geweldig vinden.

Laten we beginnen met de ODBC driver.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Stap 1: Het Azure Cosmos DB ODBC-stuurprogramma installeren

1. Download de stuurprogramma's voor uw omgeving:

    | Installer | Ondersteunde besturingssystemen| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) voor 64-bits Windows| 64-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 en Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) voor 32-bits op 64-bits Windows| 64-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 en Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) voor 32-bits Windows|32-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows XP en Windows Vista.|

    Voer het msi-bestand lokaal uit, waarmee de **wizard Microsoft Azure Cosmos DB ODBC-stuurprogramma-installatie**wordt gestart. 

1. Voltooi de installatiewizard met de standaardinvoer om het ODBC-stuurprogramma te installeren.

1. Open de **ODBC Data source Administrator-app** op uw computer. U dit doen door **ODBC-gegevensbronnen** te typen in het zoekvak van Windows. 
    U bevestigen dat het stuurprogramma is geïnstalleerd door op het tabblad **Stuurprogramma's** te klikken en ervoor te zorgen dat **Microsoft Azure Cosmos DB ODBC-stuurprogramma** wordt weergegeven.

    ![Azure Cosmos DB ODBC-gegevensbronbeheerder](./media/odbc-driver/odbc-driver.png)

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Stap 2: Verbinding maken met uw Azure Cosmos-database

1. Nadat [u het Azure Cosmos DB ODBC-stuurprogramma](#install)hebt geïnstalleerd, klikt u in het venster **ODBC-gegevensbronbeheerder** op **Toevoegen**. U een gebruiker of systeemdsn maken. In dit voorbeeld maakt u een DSN voor gebruikers.

1. Selecteer **microsoft Azure Cosmos DB ODBC-stuurprogramma**in het venster Nieuwe **gegevensbron maken** en klik op **Voltooien**.

1. Vul in het venster **Azure Cosmos DB ODBC Driver SDN Setup** de volgende gegevens in: 

    ![Azure Cosmos DB ODBC Driver DSN-installatievenster](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Naam van de bron van**gegevens: uw eigen vriendelijke naam voor de ODBC DSN. Deze naam is uniek voor uw Azure Cosmos DB-account, dus geef deze de juiste naam als u meerdere accounts hebt.
    - **Beschrijving**: Een korte beschrijving van de gegevensbron.
    - **Host:** URI voor uw Azure Cosmos DB-account. U dit ophalen op de pagina Azure Cosmos DB Keys in de Azure-portal, zoals weergegeven in de volgende schermafbeelding. 
    - **Toegangssleutel:** de primaire of secundaire, lees-geschreven of alleen-lezen sleutel van de Azure Cosmos DB Keys-pagina in de Azure-portal, zoals weergegeven in de volgende schermafbeelding. We raden u aan de alleen-lezen sleutel te gebruiken als de DSN wordt gebruikt voor alleen-lezen gegevensverwerking en -rapportage.
    ![Pagina Azure Cosmos DB Keys](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Access-sleutel versleutelen voor**: Selecteer de beste keuze op basis van de gebruikers van deze machine. 
    
1. Klik **op** de knop Testen om te controleren of u verbinding maken met uw Azure Cosmos DB-account. 

1.  Klik **op Geavanceerde opties** en stel de volgende waarden in:
    *  **REST API-versie:** selecteer de [REST API-versie](https://docs.microsoft.com/rest/api/cosmos-db/) voor uw bewerkingen. De standaardinstelling 2015-12-16. Als u containers met [grote partitiesleutels hebt](large-partition-keys.md) en REST API-versie 2018-12-31 vereist:
        - Typ in **2018-12-31** voor REST API-versie
        - Typ 'regedit' in het menu **Start** om de **registereditor-toepassing** te zoeken en te openen.
        - Navigeer in registereditor naar het pad: **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC. INI**
        - Maak een nieuwe subsleutel met dezelfde naam als uw DSN, bijvoorbeeld "Contoso Account ODBC DSN".
        - Navigeer naar de subsleutel 'Contoso-account ODBC DSN'.
        - Klik met de rechtermuisknop om een nieuwe **tekenreekswaarde** toe te voegen:
            - Waardenaam: **IgnoreSessionToken**
            - Waardegegevens: **1**
            ![Instellingen voor registereditor](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Queryconsistentie:** selecteer het [consistentieniveau](consistency-levels.md) voor uw bewerkingen. De standaardinstelling is Sessie.
    - **Aantal nieuwe pogingen**: Voer het aantal keren in dat u een bewerking opnieuw wilt proberen als de oorspronkelijke aanvraag niet wordt voltooid vanwege de beperking van het servicetarief.
    - **Schemabestand**: U hebt hier een aantal opties.
        - Standaard scant de chauffeur de eerste pagina met gegevens voor alle containers om het schema van elke container te bepalen. Dit staat bekend als Container Mapping. Zonder een schemabestand gedefinieerd, moet het stuurprogramma de scan voor elke stuurprogrammasessie uitvoeren en kan dit resulteren in een hogere opstarttijd van een toepassing met behulp van de DSN. We raden u aan altijd een schemabestand voor een DSN te koppelen.
        - Als u al een schemabestand hebt (mogelijk een bestand dat u hebt gemaakt met behulp van de schema-editor), u op **Bladeren**klikken, naar het bestand navigeren, op **Opslaan**klikken en vervolgens op **OK**klikken.
        - Als u een nieuw schema wilt maken, klikt u op **OK**en klikt u vervolgens op **Schema-editor** in het hoofdvenster. Ga dan naar de schema-editor informatie. Nadat u het nieuwe schemabestand hebt gemaakt, moet u terug naar het venster **Geavanceerde opties** gaan om het nieuw gemaakte schemabestand op te nemen.

1. Zodra u het **DSN-installatievenster azure cosmos DB ODBC-stuurprogramma** hebt voltooid en gesloten, wordt de nieuwe DSN-gebruiker toegevoegd aan het tabblad DSN voor gebruiker.

    ![Nieuwe Azure Cosmos DB ODBC DSN op het tabblad DSN voor gebruikers](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Stap 3: Een schemadefinitie maken met de methode voor containertoewijzing

Er zijn twee soorten bemonsteringsmethoden die u gebruiken: **containertoewijzing** of **tabelscheidingen.** Een bemonsteringssessie kan beide bemonsteringsmethoden gebruiken, maar elke container kan alleen een specifieke bemonsteringsmethode gebruiken. In de onderstaande stappen wordt een schema gemaakt voor de gegevens in een of meer containers met behulp van de methode voor containertoewijzing. Met deze bemonsteringsmethode worden de gegevens op de pagina van een container opgehaald om de structuur van de gegevens te bepalen. Het zet een container om in een tabel aan de ODBC-kant. Deze bemonsteringsmethode is efficiënt en snel wanneer de gegevens in een container homogeen zijn. Als een container heterogene gegevens bevat, raden we u aan de kaartmethode voor [tabelbedelingen te](#table-mapping) gebruiken, omdat deze een robuustere bemonsteringsmethode biedt om de gegevensstructuren in de container te bepalen. 

1. Nadat u stappen 1-4 hebt voltooid in [Verbinding maken met uw Azure Cosmos-database,](#connect)klikt u op **Schema-editor** in het venster **Azure Cosmos DB ODBC-stuurprogramma DSN-installatie.**

    ![Knop Schema-editor in het venster Azure Cosmos DB ODBC Driver DSN Setup](./media/odbc-driver/odbc-driver-schema-editor.png)
1. Klik in het venster **Schema-editor** op **Nieuw maken**.
    In het venster **Schema genereren** worden alle containers weergegeven in het Azure Cosmos DB-account. 

1. Selecteer een of meer containers die u wilt samplen en klik op **Voorbeeld**. 

1. Op het tabblad **Ontwerpweergave** worden de database, het schema en de tabel weergegeven. In de tabelweergave wordt in de scan de set eigenschappen weergegeven die zijn gekoppeld aan de kolomnamen (SQL-naam, bronnaam, enz.).
    Voor elke kolom u de naam VAN DE KOLOM SQL, het SQL-type, SQL-lengte (indien van toepassing), Schaal (indien van toepassing), Precisie (indien van toepassing) en Nullable wijzigen.
    - U **Kolom verbergen** op **true** instellen als u die kolom wilt uitsluiten van queryresultaten. Kolommen met de tekst Kolom verbergen = waar worden niet geretourneerd voor selectie en projectie, hoewel ze nog steeds deel uitmaken van het schema. U bijvoorbeeld alle vereiste eigenschappen van het Azure Cosmos DB-systeem verbergen, te beginnen met _.
    - De **id-kolom** is het enige veld dat niet kan worden verborgen omdat het wordt gebruikt als de primaire sleutel in het genormaliseerde schema. 

1. Nadat u klaar bent met het definiëren van het schema, klikt u op **Bestand** | **opslaan,** navigeert u naar de map om het schema op te slaan en klikt u vervolgens op **Opslaan**.

1. Als u dit schema met een DSN wilt gebruiken, opent u het **azure cosmos DB ODBC-stuurprogramma dsn-installatievenster** (via de ODBC-gegevensbronbeheerder), klikt u op **Geavanceerde opties**en navigeert u vervolgens in het vak **Schemabestand** naar het opgeslagen schema. Als u een schemabestand opslaat in een bestaand DSN, wordt de DSN-verbinding met het bereik gewijzigd op de gegevens en structuur die door het schema zijn gedefinieerd.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Stap 4: Een schemadefinitie maken met de toewijzingsmethode voor tabelscheidingen

Er zijn twee soorten bemonsteringsmethoden die u gebruiken: **containertoewijzing** of **tabelscheidingen.** Een bemonsteringssessie kan beide bemonsteringsmethoden gebruiken, maar elke container kan alleen een specifieke bemonsteringsmethode gebruiken. 

Met de volgende stappen wordt een schema gemaakt voor de gegevens in een of meer containers met de **toewijzingsmethode voor tabelscheidingen.** We raden u aan deze bemonsteringsmethode te gebruiken wanneer uw containers heterogene gegevens bevatten. U deze methode gebruiken om de bemonstering te richten op een set kenmerken en de bijbehorende waarden. Als een document bijvoorbeeld een eigenschap 'Type' bevat, u de steekproef naar de waarden van deze eigenschap scopen. Het eindresultaat van de steekproef is een reeks tabellen voor elk van de waarden voor tekst die u hebt opgegeven. Type = auto produceert bijvoorbeeld een tabel Auto terwijl Type = Vlak een vliegtuigtabel zou produceren.

1. Nadat u stappen 1-4 hebt voltooid in [Verbinding maken met uw Azure Cosmos-database,](#connect)klikt u op **Schema-editor** in het venster Azure Cosmos DB ODBC-stuurprogramma DSN-installatie.

1. Klik in het venster **Schema-editor** op **Nieuw maken**.
    In het venster **Schema genereren** worden alle containers weergegeven in het Azure Cosmos DB-account. 

1. Selecteer een container op het tabblad **Voorbeeldweergave** in de kolom **Toewijzingsdefinitie** voor de container klik op **Bewerken**. Selecteer vervolgens in het venster **Toewijzingsdefinitie** de methode **Tabelscheidingen.** Ga daarna als volgt te werk:

    a. Typ in het vak **Kenmerken** de naam van een eigenschap van de scheidingsteken. Dit is een eigenschap in uw document die u wilt scope de bemonstering om, bijvoorbeeld, Stad en druk op enter. 

    b. Als u de steekproef alleen wilt scopen met bepaalde waarden voor het kenmerk dat u hierboven hebt ingevoerd, selecteert u het kenmerk in het selectievak, voert u een waarde in het vak **Waarde** in (bijvoorbeeld Seattle) en drukt u op Enter. U meerdere waarden voor kenmerken blijven toevoegen. Zorg ervoor dat het juiste kenmerk is geselecteerd wanneer u waarden invoert.

    Als u bijvoorbeeld een **waarde voor kenmerken** van City opneemt en u wilt dat uw tabel alleen rijen met een stadswaarde van New York en Dubai bevat, voert u City in het vak Kenmerken en New York en vervolgens Dubai in het vak **Waarden** in.

1. Klik op **OK**. 

1. Nadat u de toewijzingsdefinities voor de containers hebt voltooid die u wilt samplen, klikt u in het venster **Schemaeditor** op **Voorbeeld**.
     Voor elke kolom u de naam VAN DE KOLOM SQL, het SQL-type, SQL-lengte (indien van toepassing), Schaal (indien van toepassing), Precisie (indien van toepassing) en Nullable wijzigen.
    - U **Kolom verbergen** op **true** instellen als u die kolom wilt uitsluiten van queryresultaten. Kolommen met de tekst Kolom verbergen = waar worden niet geretourneerd voor selectie en projectie, hoewel ze nog steeds deel uitmaken van het schema. U bijvoorbeeld alle vereiste eigenschappen van het `_`Azure Cosmos DB-systeem verbergen, te beginnen met.
    - De **id-kolom** is het enige veld dat niet kan worden verborgen omdat het wordt gebruikt als de primaire sleutel in het genormaliseerde schema. 

1. Nadat u klaar bent met het definiëren van het schema, klikt u op **Bestand** | **opslaan,** navigeert u naar de map om het schema op te slaan en klikt u vervolgens op **Opslaan**.

1. Klik terug in het **dsn-installatievenster azure cosmos DB ODBC-stuurprogramma op** **Geavanceerde opties**. Navigeer vervolgens in het vak **Schemabestand** naar het opgeslagen schemabestand en klik op **OK**. Klik nogmaals op **OK** om de DSN op te slaan. Hiermee wordt het schema opgeslagen dat u hebt gemaakt op de DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Optioneel) Gekoppelde serververbinding instellen

U Azure Cosmos DB opvragen vanuit SQL Server Management Studio (SSMS) door een gekoppelde serververbinding in te stellen.

1. Maak een systeemgegevensbron zoals beschreven in `SDS Name` [stap 2,](#connect)bijvoorbeeld genoemd .

1. [Installeer SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en maak verbinding met de server. 

1. Maak in de SSMS-queryeditor `DEMOCOSMOS` een gekoppeld serverobject voor de gegevensbron met de volgende opdrachten. Vervang `DEMOCOSMOS` de naam voor uw `SDS Name` gekoppelde server en de naam van uw systeemgegevensbron.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Als u de nieuwe gekoppelde servernaam wilt zien, vernieuwt u de lijst Gekoppelde servers.

![Gekoppelde server in SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Gekoppelde database met query's

Als u de gekoppelde database wilt opvragen, voert u een SSMS-query in. In dit voorbeeld selecteert de query in de `customers`tabel in de container met de naam :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Voer de query uit. Het resultaat moet vergelijkbaar zijn met dit:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> De gekoppelde Cosmos DB-server ondersteunt geen naamgeving in vier delen. Een fout wordt geretourneerd vergelijkbaar met het volgende bericht:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Optioneel) Weergaven maken
U weergaven definiëren en maken als onderdeel van het bemonsteringsproces. Deze weergaven zijn gelijk aan SQL-weergaven. Ze zijn alleen-lezen en zijn scope de selecties en projecties van de Azure Cosmos DB SQL query gedefinieerd. 

Als u een weergave voor uw gegevens wilt maken, klikt u in het venster **Schema-editor** in de kolom **Definities weergeven** op **Toevoegen aan** de rij van de container om te samplen. 
    ![Een weergave van gegevens maken](./media/odbc-driver/odbc-driver-create-view.png)


Ga vervolgens in het venster **Definities weergeven** als volgt te werk:

1. Klik **op Nieuw,** voer een naam in voor de weergave, bijvoorbeeld EmployeesfromSeattleView en klik op **OK**.

1. Voer in het venster **Weergave bewerken** een Azure Cosmos DB-query in. Dit moet bijvoorbeeld `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`een Azure Cosmos DB [SQL-query](how-to-sql-query.md)zijn en vervolgens op **OK**klikken.

    ![Query toevoegen bij het maken van een weergave](./media/odbc-driver/odbc-driver-create-view-2.png)


U veel weergaven maken zoals u wilt. Zodra u klaar bent met het definiëren van de weergaven, u de gegevens bekijken. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Stap 5: Uw gegevens weergeven in BI-hulpprogramma's zoals Power BI Desktop

U uw nieuwe DSN gebruiken om verbinding te maken met Azure Cosmos DB met alle ODBC-compatibele hulpprogramma's - deze stap laat u gewoon zien hoe u verbinding maken met Power BI Desktop en een Power BI-visualisatie maken.

1. Open Power BI Desktop.

1. Klik **op Gegevens opvragen**.

    ![Gegevens ophalen in Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Klik in het venster **Gegevens opvragen** op **Andere** | **ODBC** | **Connect**.

    ![Kies ODBC-gegevensbron in Power BI-gegevens](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. Selecteer in het venster **Van ODBC** de gegevensbronnaam die u hebt gemaakt en klik op **OK**. U de vermeldingen **met geavanceerde opties** leeg laten.

    ![Kies Gegevensbronnaam (DSN) in Power BI-gegevens](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. Selecteer in **de gegevensbron Toegang tot een gegevensbron met een ODBC-stuurprogrammavenster** de optie **Standaard of Aangepast** en klik op **Verbinden**. U hoeft de eigenschappen van de **referentieverbindingstekenreeks**niet op te nemen.

1. Vouw in het **venster Navigator** in het linkerdeelvenster de database, het schema uit en selecteer vervolgens de tabel. Het resultatenvenster bevat de gegevens met behulp van het schema dat u hebt gemaakt.

    ![Tabel selecteren in Power BI-gegevens opvragen](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Als u de gegevens in Power BI-bureaublad wilt visualiseren, schakelt u het selectievakje voor de tabelnaam in en klikt u op **Laden**.

1. Selecteer in Power BI-bureaublad helemaal links het tabblad Gegevens ![Tabblad Gegevens in Power BI-bureaublad](./media/odbc-driver/odbc-driver-data-tab.png) om te bevestigen dat uw gegevens zijn geïmporteerd.

1. U nu visuals maken met Power BI ![door op het](./media/odbc-driver/odbc-driver-report-tab.png)tabblad Rapportrapport in Power BI-bureaublad te klikken, op **Nieuwe visual**te klikken en vervolgens de tegel aan te maken. Zie [Visualisatietypen in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)voor meer informatie over het maken van visualisaties in Power BI.

## <a name="troubleshooting"></a>Problemen oplossen

Als u de volgende fout ontvangt, moet u ervoor zorgen dat de waarden **host-** en **toegangssleutel** die u de Azure-portal in [stap 2](#connect) hebt gekopieerd, correct zijn en vervolgens opnieuw proberen. Gebruik de kopieerknoppen rechts van de waarden **Host-** en **Access-sleutel** in de Azure-portal om de waarden foutloos te kopiëren.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Volgende stappen

Zie [Welkom bij Azure Cosmos DB](introduction.md) voor meer informatie over Azure Cosmos DB.
