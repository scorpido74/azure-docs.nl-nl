---
title: Verbinding maken met Azure Cosmos DB, waarbij van BI analytics-hulpprogramma 's
description: Informatie over het gebruik van het Azure Cosmos DB ODBC-stuurprogramma om te maken van tabellen en weergaven zodat genormaliseerde gegevens kunnen worden weergegeven in BI en data analytics-software.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721078"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Verbinding maken met Azure Cosmos DB met behulp van analysehulpprogramma's voor BI met het ODBC-stuurprogramma

Het Azure Cosmos DB ODBC-stuurprogramma kunt u verbinding maken met Azure Cosmos DB met behulp van BI analytics-hulpprogramma's zoals SQL Server Integration Services, Power BI Desktop en Tableau, zodat u kunt analyseren en visualisaties van uw Azure Cosmos DB-gegevens in deze oplossingen maken.

Het ODBC-stuurprogramma van Azure Cosmos DB is compatibel met ODBC 3.8 en ondersteunt ANSI SQL-92-syntaxis. Het stuurprogramma biedt uitgebreide functies waarmee u gegevens in Azure Cosmos DB opnieuw kunt normaliseren. Met behulp van het stuurprogramma kunt u gegevens in Azure Cosmos DB weergeven als tabellen en weergaven. Met het stuurprogramma kunt u SQL-bewerkingen uitvoeren ten opzichte van de tabellen en weergaven, zoals groeperen op query's, invoegingen, updates en verwijderingen.

> [!NOTE]
> Verbinding maken met Azure Cosmos DB met het ODBC-stuurprogramma wordt momenteel ondersteund voor Azure Cosmos DB SQL API-accounts.

## <a name="why-do-i-need-to-normalize-my-data"></a>Waarom moet ik mijn gegevens normaliseren?
Azure Cosmos DB is een database zonder schema, waarmee u snel ontwikkelen van toepassingen en de mogelijkheid om te herhalen zonder wordt beperkt tot een strikt schema gegevensmodellen over te brengen. Eén Azure Cosmos-data base kan JSON-documenten van verschillende structuren bevatten. Dit is ideaal voor het snel ontwikkelen van toepassingen, maar als u wilt analyseren en rapporten te maken van uw gegevens met behulp van gegevensanalyse en BI-hulpprogramma's, de gegevens vaak moeten worden "afgevlakt" en voldoen aan een specifieke schema.

Dit is waar het ODBC-stuurprogramma is beschikbaar in. U kunt nu met behulp van het ODBC-stuurprogramma opnieuw normaliseren gegevens in Azure Cosmos DB in tabellen en weergaven die geschikt zijn voor uw gegevensanalyse en rapportage u nodig hebt. De renormalized schema's hebben geen invloed op de onderliggende gegevens en ontwikkelaars om te voldoen aan deze niet kunnen beperken. In plaats daarvan kunnen deze u gebruikmaken van ODBC-hulpprogramma's voor toegang tot de gegevens. Nu is uw Azure Cosmos-data base niet alleen een favoriet voor uw ontwikkel team, maar uw gegevens analisten zullen het ook leuker maken.

Aan de slag met het ODBC-stuurprogramma.

## <a id="install"></a>Stap 1: het ODBC-stuur programma voor Azure Cosmos DB installeren

1. Download de stuurprogramma's voor uw omgeving:

    | Installatieprogramma | Ondersteunde besturingssystemen| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit. msi](https://aka.ms/cosmos-odbc-64x64) voor 64-bits Windows| 64-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 en Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos db odbc 32x64-bit. msi](https://aka.ms/cosmos-odbc-32x64) voor 32-bits op 64-bits Windows| 64-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 en Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit. msi](https://aka.ms/cosmos-odbc-32x32) voor 32-bits Windows|32-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows XP en Windows Vista.|

    Voer het MSI-bestand lokaal uit, waarmee de **installatie wizard voor het Microsoft Azure Cosmos DB ODBC-stuur programma**wordt gestart. 

1. Voltooi de installatiewizard met behulp van de invoer voor het installeren van het ODBC-stuurprogramma.

1. Open de **ODBC-gegevens bron beheer-** app op uw computer. U kunt dit doen door **ODBC-gegevens bronnen** te typen in het zoekvak van Windows. 
    U kunt bevestigen dat het stuur programma is geïnstalleerd door op het tabblad **Stuur Programma's** te klikken en ervoor te zorgen dat **Microsoft Azure Cosmos db odbc-stuur programma** wordt weer gegeven.

    ![Azure Cosmos DB ODBC-gegevensbronbeheer](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Stap 2: verbinding maken met uw Azure Cosmos-data base

1. Nadat u [het ODBC-stuur programma voor Azure Cosmos DB hebt geïnstalleerd](#install), klikt u in het venster **ODBC-gegevens bron beheer** op **toevoegen**. U kunt een gebruiker of systeem-DSN maken. In dit voorbeeld maakt u een gebruikers-DSN.

1. Selecteer **Microsoft Azure Cosmos DB ODBC-stuur programma**in het venster **nieuwe gegevens bron maken** en klik op **volt ooien**.

1. Vul in **Azure Cosmos DB het venster Sdn-configuratie van ODBC-stuur programma** de volgende gegevens in: 

    ![Het venster Azure Cosmos DB ODBC-stuurprogramma DSN-instellingen](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Naam van gegevens bron**: uw eigen beschrijvende naam voor de ODBC-DSN. Deze naam uniek is voor uw Azure Cosmos DB-account is, dus op de juiste wijze naam als u meerdere accounts hebt.
    - **Beschrijving**: een korte beschrijving van de gegevens bron.
    - **Host**: URI voor uw Azure Cosmos DB-account. U kunt dit ophalen via de pagina sleutels voor Azure Cosmos DB in Azure portal, zoals wordt weergegeven in de volgende schermafbeelding. 
    - **Toegangs sleutel**: de primaire of secundaire, alleen-lezen of alleen-lezen sleutel van de pagina Azure Cosmos DB sleutels in de Azure Portal, zoals weer gegeven in de volgende scherm afbeelding. Het is raadzaam om dat de alleen-lezen-sleutel te gebruiken als de DSN-naam wordt gebruikt voor de alleen-lezen gegevens verwerken en rapportage.
    ![Azure Cosmos DB sleutels pagina](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Toegangs sleutel versleutelen voor**: Selecteer de beste keuze op basis van de gebruikers van deze computer. 
    
1. Klik op de knop **testen** om te controleren of u verbinding kunt maken met uw Azure Cosmos DB-account. 

1.  Klik op **Geavanceerde opties** en stel de volgende waarden in:
    *  **Rest API versie**: selecteer de [rest API versie](https://docs.microsoft.com/rest/api/cosmos-db/) voor uw bewerkingen. De standaard waarde is 2015-12-16. Als u containers met [grote partitie sleutels](large-partition-keys.md) hebt en REST API versie 2018-12-31 hebt vereist:
        - Typ **2018-12-31** voor rest API versie
        - Typ ' regedit ' in het menu **Start** om de toepassing **REGI ster-editor** te zoeken en te openen.
        - Navigeer in de REGI ster-editor naar het pad: **computer \ HKEY_LOCAL_MACHINE \software\odbc\odbc. INI**
        - Maak een nieuwe subsleutel met dezelfde naam als uw DSN, bijvoorbeeld ' contoso-account ODBC-DSN '.
        - Navigeer naar de subsleutel ' contoso account ODBC DSN '.
        - Klik met de rechter muisknop om een nieuwe **teken reeks** waarde toe te voegen:
            - Waardenaam: **IgnoreSessionToken**
            - Waardegegevens: **1**
            ![REGI ster-editor-instellingen](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Consistentie van query's**: Selecteer het [consistentie niveau](consistency-levels.md) voor uw bewerkingen. De standaardwaarde is de sessie.
    - **Aantal nieuwe pogingen**: Geef het aantal keren op dat een bewerking opnieuw moet worden uitgevoerd als de eerste aanvraag niet is voltooid als gevolg van de service limiet.
    - **Schema bestand**: u hebt hier een aantal opties.
        - Standaard wordt de eerste pagina met gegevens voor alle containers door het stuur programma gecontroleerd om het schema van elke container te bepalen. Dit staat bekend als container toewijzing. Zonder een schemabestand dat is gedefinieerd, wordt het stuurprogramma is voor het uitvoeren van de scan voor elke sessie stuurprogramma en kan leiden tot een hogere opstarttijd van een toepassing met behulp van de DSN. Het is raadzaam dat u altijd een schemabestand voor een DSN koppelen.
        - Als u al een schema bestand hebt (mogelijk een dat u hebt gemaakt met de schema-editor), klikt u op **Bladeren**, navigeert u naar het bestand, klikt u op **Opslaan**en klikt u vervolgens op **OK**.
        - Als u een nieuw schema wilt maken, klikt u op **OK**en klikt u vervolgens in het hoofd venster op **schema-editor** . Ga vervolgens verder met de informatie over de schema-editor. Nadat u het nieuwe schema bestand hebt gemaakt, gaat u terug naar het venster **Geavanceerde opties** om het zojuist gemaakte schema bestand op te slaan.

1. Zodra u Azure Cosmos DB het venster **Setup van ODBC-stuur programma-DSN** hebt voltooid en gesloten, wordt de nieuwe gebruikers-DSN toegevoegd aan het tabblad gebruikers-DSN.

    ![Nieuwe Azure Cosmos DB ODBC DSN op het tabblad gebruikers-DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#container-mapping"></a>Stap 3: een schema definitie maken met behulp van de container toewijzings methode

Er zijn twee soorten bemonsterings methoden die u kunt gebruiken: **container toewijzing** of **tabel scheidings tekens**. Een steekproef sessie kan gebruikmaken van beide steekproef methoden, maar elke container kan alleen een specifieke steekproef methode gebruiken. Met de volgende stappen maakt u een schema voor de gegevens in een of meer containers met behulp van de container toewijzings methode. Met deze steekproef methode worden de gegevens in de pagina van een container opgehaald om de structuur van de gegevens te bepalen. Er wordt een container naar een tabel op de ODBC-zijde getransponeren. Deze steekproef methode is efficiënt en snel wanneer de gegevens in een container homo geen zijn. Als een container hetero geen gegevens bevat, kunt u het beste de [toewijzings methode voor tabel scheidingen](#table-mapping) gebruiken, aangezien deze een krachtigere bemonsterings methode biedt om de gegevens structuren in de container te bepalen. 

1. Klik na het volt ooien van de stappen 1-4 in [verbinding maken met uw Azure Cosmos-data base](#connect)op **schema-editor** in het venster **Azure Cosmos db odbc-stuur programma-DSN instellen** .

    ![Knop voor schema-editor in het venster Azure Cosmos DB ODBC-stuurprogramma DSN instellingen](./media/odbc-driver/odbc-driver-schema-editor.png)
1. Klik in het venster **schema-editor** op **nieuwe maken**.
    In het venster **schema genereren** worden alle containers in het Azure Cosmos DB-account weer gegeven. 

1. Selecteer een of meer containers om voor beelden te selecteren en klik vervolgens op voor **beeld**. 

1. Op het tabblad **ontwerp weergave** worden de data base, het schema en de tabel weer gegeven. De scan weergegeven in de tabelweergave de set eigenschappen die zijn gekoppeld aan de namen van de kolommen (SQL-naam, naam van de gegevensbron, enzovoort).
    Voor elke kolom, kunt u de naam van de kolom SQL, de SQL-type, de SQL-lengte (indien van toepassing), schalen (indien van toepassing), de precisie (indien van toepassing) en de null-waarden bevatten.
    - U kunt de **kolom verbergen** instellen op **True** als u die kolom wilt uitsluiten van query resultaten. Kolommen gemarkeerd kolom verbergen = true niet worden geretourneerd voor de selectie en projectie, hoewel ze nog steeds deel van het schema uitmaken. U kunt bijvoorbeeld alle van de Azure Cosmos DB voor het systeem vereist-eigenschappen die beginnen met '_' verbergen.
    - De kolom **id** is het enige veld dat niet kan worden verborgen omdat het wordt gebruikt als primaire sleutel in het genormaliseerde schema. 

1. Wanneer u klaar bent met het definiëren van het schema, klikt u op **bestand** | **Opslaan**, gaat u naar de Directory om het schema op te slaan en klikt u vervolgens op **Opslaan**.

1. Als u dit schema met een DSN wilt gebruiken, opent u het **venster Azure Cosmos DB ODBC driver DSN Setup** (via de ODBC-gegevens bron beheerder), klikt u op **Geavanceerde opties**en navigeert u in het vak **schema bestand** naar het opgeslagen schema. Opslaan van een schemabestand naar een bestaande DSN Hiermee wijzigt u de DSN-verbinding met bereik tot de gegevens en de definitie van schema-structuur.

## <a id="table-mapping"></a>Stap 4: een schema definitie maken met behulp van de toewijzings methode voor tabel scheidingen

Er zijn twee soorten bemonsterings methoden die u kunt gebruiken: **container toewijzing** of **tabel scheidings tekens**. Een steekproef sessie kan gebruikmaken van beide steekproef methoden, maar elke container kan alleen een specifieke steekproef methode gebruiken. 

Met de volgende stappen maakt u een schema voor de gegevens in een of meer containers met behulp van de toewijzings methode voor **tabel scheidingen** . U wordt aangeraden deze steekproef methode te gebruiken wanneer uw containers heterogene gegevens typen bevatten. U kunt deze methode om het bereik van de meting naar een set met kenmerken en de bijbehorende waarden te gebruiken. Als een document bevat een eigenschap 'Type', kunt u bijvoorbeeld de steekproeven om de waarden van deze eigenschap te beperken. Het eindresultaat van de steekproeven is een set met tabellen voor elk van de waarden voor het Type dat u hebt opgegeven. Typ bijvoorbeeld = auto produceert een auto-tabel bij het Type = vlak geeft als resultaat een tabel van de gegevenslaag.

1. Klik na het volt ooien van de stappen 1-4 in [verbinding maken met uw Azure Cosmos-data base](#connect)op **schema-editor** in het venster Azure Cosmos DB ODBC-stuur programma-DSN instellen.

1. Klik in het venster **schema-editor** op **nieuwe maken**.
    In het venster **schema genereren** worden alle containers in het Azure Cosmos DB-account weer gegeven. 

1. Selecteer een container op het tabblad **voor beeld weergave** in de kolom **toewijzings definitie** voor de container en klik op **bewerken**. Selecteer vervolgens in het venster **toewijzings definitie** de methode **tabel scheidings tekens** . Ga daarna als volgt te werk:

    a. Typ in het vak **kenmerken** de naam van een scheidings eigenschap. Dit is een eigenschap in het document dat u wilt het bereik van de steekproeven naar, bijvoorbeeld, plaats en druk op enter. 

    b. Als u de steek proef alleen wilt beperken tot bepaalde waarden voor het kenmerk dat u hierboven hebt opgegeven, selecteert u het kenmerk in het selectie vakje, voert u een waarde in het vak **waarde** (bijvoorbeeld Seattle) in en drukt u op ENTER. U kunt doorgaan met het toevoegen van meerdere waarden voor kenmerken. Zorg dat het juiste kenmerk is geselecteerd wanneer u waarden invoert.

    Als u bijvoorbeeld een **kenmerk** waarde van City opneemt en u wilt beperken dat de tabel alleen rijen met de waarde City en Dubai bevat, voert u plaats in het vak kenmerken in en in New York en vervolgens Dubai in het vak **waarden** .

1. Klik op **OK**. 

1. Klik na het volt ooien van de toewijzings definities voor de containers die u wilt bemonsteren in het venster **schema-editor** op voor **beeld**.
     Voor elke kolom, kunt u de naam van de kolom SQL, de SQL-type, de SQL-lengte (indien van toepassing), schalen (indien van toepassing), de precisie (indien van toepassing) en de null-waarden bevatten.
    - U kunt de **kolom verbergen** instellen op **True** als u die kolom wilt uitsluiten van query resultaten. Kolommen gemarkeerd kolom verbergen = true niet worden geretourneerd voor de selectie en projectie, hoewel ze nog steeds deel van het schema uitmaken. U kunt bijvoorbeeld alle Azure Cosmos DB vereiste systeem eigenschappen verbergen, te beginnen met `_`.
    - De kolom **id** is het enige veld dat niet kan worden verborgen omdat het wordt gebruikt als primaire sleutel in het genormaliseerde schema. 

1. Wanneer u klaar bent met het definiëren van het schema, klikt u op **bestand** | **Opslaan**, gaat u naar de Directory om het schema op te slaan en klikt u vervolgens op **Opslaan**.

1. Klik in Azure Cosmos DB het venster **Setup van ODBC-stuur programma-DSN** op **Geavanceerde opties**. Ga vervolgens naar het opgeslagen schema bestand in het vak **schema bestand** en klik op **OK**. Klik nogmaals op **OK** om de DSN op te slaan. Dit bespaart het schema dat u hebt gemaakt naar de DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Optioneel) Gekoppelde serververbinding instellen

U kunt Azure Cosmos DB van SQL Server Management Studio (SSMS) door het instellen van een gekoppelde serververbinding een query.

1. Maak een systeemgegevens bron zoals beschreven in [stap 2](#connect), met de naam bijvoorbeeld `SDS Name`.

1. [Installeer SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en maak verbinding met de server. 

1. Maak in de SSMS-query-editor een gekoppeld Server object `DEMOCOSMOS` voor de gegevens bron met de volgende opdrachten. Vervang `DEMOCOSMOS` door de naam van de gekoppelde server en `SDS Name` door de naam van de gegevens bron van uw systeem.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Als u wilt zien van de nieuwe naam voor de gekoppelde server, de gekoppelde Servers-lijst te vernieuwen.

![Gekoppelde Server in SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Gekoppelde query uitvoeren op database

Als u wilt zoeken in de gekoppelde database, moet u een SSMS-query opgeven. In dit voor beeld wordt de query geselecteerd uit de tabel in de container met de naam `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Voer de query uit. Het resultaat moet ongeveer als volgt uit:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> De gekoppelde Cosmos DB-server biedt geen ondersteuning voor vierdelige naam geven. Een fout wordt geretourneerd die vergelijkbaar is met het volgende bericht:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Optioneel) Het maken van weergaven
U kunt definiëren en weergaven maken als onderdeel van het proces steekproeven. Deze weergaven zijn gelijk aan de SQL-weergaven. Ze zijn alleen-lezen en bereik zijn de selecties en projecties van de Azure Cosmos DB SQL-query die is gedefinieerd. 

Als u een weer gave voor uw gegevens wilt maken, klikt u in het venster **schema-editor** in de kolom **weergave definities** op **toevoegen** op de rij van de container om een voor beeld te geven. 
    ![een weer gave van gegevens maken](./media/odbc-driver/odbc-driver-create-view.png)


Ga vervolgens als volgt te werk in het venster **weergave definities** :

1. Klik op **Nieuw**, voer een naam in voor de weer gave, bijvoorbeeld EmployeesfromSeattleView, en klik vervolgens op **OK**.

1. Voer in het venster **weer gave bewerken** een Azure Cosmos DB query in. Dit moet een [Azure Cosmos DB SQL-query](how-to-sql-query.md), bijvoorbeeld `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, en klik vervolgens op **OK**.

    ![Query bij het maken van een weergave toevoegen](./media/odbc-driver/odbc-driver-create-view-2.png)


Als u wilt, kunt u een groot aantal weergaven maken. Wanneer u klaar bent voor het definiëren van de weergaven die u kunt vervolgens steekproef de gegevens. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Stap 5: Uw gegevens weergeven in de BI-hulpprogramma's zoals Power BI Desktop

U kunt uw nieuwe DSN gebruiken om te verbinden met Azure Cosmos DB met een ODBC-hulpprogramma's: deze stap gewoon laat zien hoe u verbinding maakt met Power BI Desktop en maak een Power BI-visualisatie.

1. Open Power BI Desktop.

1. Klik op **gegevens ophalen**.

    ![Gegevens ophalen in Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Klik in het venster **gegevens ophalen** op **andere** | **ODBC-**  | **verbinding maken**.

    ![Kies ODBC-gegevensbron in gegevens ophalen voor Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. Selecteer in het venster **van ODBC** de naam van de gegevens bron die u hebt gemaakt en klik vervolgens op **OK**. U kunt de **Geavanceerde opties** leeg laten.

    ![Kies de naam van gegevensbron (DSN) in gegevens ophalen voor Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. Selecteer in het venster **toegang tot een gegevens bron met een ODBC-stuur programma** de optie **standaard of aangepast** en klik vervolgens op **verbinding maken**. U hoeft de **referentie Connection String eigenschappen**niet op te neemen.

1. Vouw in het **Navigatie** venster in het linkerdeel venster de data base en het schema uit en selecteer vervolgens de tabel. Het deelvenster met resultaten bevat de gegevens met behulp van het schema dat u hebt gemaakt.

    ![Selecteer de tabel in Power BI-Get-gegevens](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Als u de gegevens in Power BI bureau blad wilt visualiseren, schakelt u het selectie vakje voor de tabel naam in en klikt u vervolgens op **laden**.

1. In Power BI Desktop, uiterst links, selecteer het tabblad gegevens ![Tabblad gegevens op in Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) om te bevestigen van uw gegevens zijn geïmporteerd.

1. U kunt nu visuals maken met behulp van Power BI door te klikken op het tabblad rapport ![tabblad rapport in Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), op **nieuwe Visual**te klikken en vervolgens uw tegel aan te passen. Zie [visualisatie typen in Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)voor meer informatie over het maken van visualisaties in Power bi Desktop.

## <a name="troubleshooting"></a>Problemen oplossen

Als u het volgende fout bericht ontvangt, moet u ervoor zorgen dat de **host** en de **toegangs sleutel** waarden die u hebt gekopieerd in [stap 2](#connect) de juiste Azure Portal zijn en probeer het opnieuw. Gebruik de Kopieer knoppen aan de rechter kant van de **host** en **toegangs sleutel** waarden in de Azure Portal om de fout vrije waarden te kopiëren.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Volgende stappen

Zie [Welkom bij Azure Cosmos DB](introduction.md) voor meer informatie over Azure Cosmos DB.
