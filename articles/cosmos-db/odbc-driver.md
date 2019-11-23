---
title: Verbinding maken met Azure Cosmos DB, waarbij van BI analytics-hulpprogramma 's
description: Informatie over het gebruik van het Azure Cosmos DB ODBC-stuurprogramma om te maken van tabellen en weergaven zodat genormaliseerde gegevens kunnen worden weergegeven in BI en data analytics-software.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e8a982a100655934d4ae3ecd64564cf2da82dbbc
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035598"
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

## <a id="install"></a>Stap 1: Het Azure Cosmos DB ODBC-stuurprogramma installeren

1. Download de stuurprogramma's voor uw omgeving:

    | Installatieprogramma | Ondersteunde besturingssystemen| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC-64-bit.msi](https://aka.ms/cosmos-odbc-64x64) voor 64-bits Windows| 64-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 en Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/cosmos-odbc-32x64) voor 32-bits, 64-bits Windows| 64-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 en Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC-32-bit.msi](https://aka.ms/cosmos-odbc-32x32) voor 32-bits Windows|32-bits versies van Windows 8.1 of hoger, Windows 8, Windows 7, Windows XP en Windows Vista.|

    Voer het msi-bestand lokaal op, die begint de **Microsoft Azure Cosmos DB ODBC-stuurprogramma met installatiewizard**. 

1. Voltooi de installatiewizard met behulp van de invoer voor het installeren van het ODBC-stuurprogramma.

1. Open de **ODBC-gegevensbron beheerder** app op uw computer. U kunt dit doen door te typen **ODBC-gegevensbronnen** in de Windows-zoekvak. 
    U kunt bevestigen dat het stuurprogramma is geïnstalleerd door te klikken op de **stuurprogramma's** tabblad en ervoor te zorgen dat **ODBC-stuurprogramma voor Microsoft Azure Cosmos DB** wordt vermeld.

    ![Azure Cosmos DB ODBC-gegevensbronbeheer](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Stap 2: verbinding maken met uw Azure Cosmos-data base

1. Na [het Azure Cosmos DB ODBC-stuurprogramma installeren](#install), in de **ODBC-gegevensbronbeheer** venster, klikt u op **toevoegen**. U kunt een gebruiker of systeem-DSN maken. In dit voorbeeld maakt u een gebruikers-DSN.

1. In de **nieuwe gegevensbron maken** venster **ODBC-stuurprogramma voor Microsoft Azure Cosmos DB**, en klik vervolgens op **voltooien**.

1. In de **Setup van Azure Cosmos DB ODBC-stuurprogramma SDN** venster, vult u in de volgende informatie: 

    ![Het venster Azure Cosmos DB ODBC-stuurprogramma DSN-instellingen](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Naam van de gegevensbron**: uw eigen beschrijvende naam voor de ODBC-DSN. Deze naam uniek is voor uw Azure Cosmos DB-account is, dus op de juiste wijze naam als u meerdere accounts hebt.
    - **Beschrijving**: een korte beschrijving van de gegevensbron.
    - **Host**: URI voor uw Azure Cosmos DB-account. U kunt dit ophalen via de pagina sleutels voor Azure Cosmos DB in Azure portal, zoals wordt weergegeven in de volgende schermafbeelding. 
    - **Toegang tot sleutel**: de primaire of secundaire alleen-lezen of alleen-lezen-sleutel van de Azure Cosmos DB-sleutels pagina in Azure portal, zoals wordt weergegeven in de volgende schermafbeelding. Het is raadzaam om dat de alleen-lezen-sleutel te gebruiken als de DSN-naam wordt gebruikt voor de alleen-lezen gegevens verwerken en rapportage.
    ![Op de pagina Azure Cosmos DB-sleutels](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **De toegangssleutel voor versleutelen**: Selecteer de beste keuze op basis van de gebruikers van deze machine. 
    
1. Klik op de **Test** knop om te controleren of u kunt verbinding maken met uw Azure Cosmos DB-account. 

1.  Klik op **geavanceerde opties** en stel de volgende waarden:
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
    - **Query uitvoeren op consistentie**: Selecteer de [consistentieniveau](consistency-levels.md) voor uw activiteiten. De standaardwaarde is de sessie.
    - **Aantal nieuwe pogingen**: Voer het aantal keren dat een bewerking opnieuw als de eerste aanvraag niet wordt voltooid vanwege de frequentielimiet service.
    - **Schemabestand**: U hebt een aantal opties hier.
        - Standaard wordt de eerste pagina met gegevens voor alle containers door het stuur programma gecontroleerd om het schema van elke container te bepalen. Dit staat bekend als container toewijzing. Zonder een schemabestand dat is gedefinieerd, wordt het stuurprogramma is voor het uitvoeren van de scan voor elke sessie stuurprogramma en kan leiden tot een hogere opstarttijd van een toepassing met behulp van de DSN. Het is raadzaam dat u altijd een schemabestand voor een DSN koppelen.
        - Als u al een schema bestand hebt (mogelijk een dat u hebt gemaakt met de schema-editor), klikt u op **Bladeren**, navigeert u naar het bestand, klikt u op **Opslaan**en klikt u vervolgens op **OK**.
        - Als u een nieuw schema maken wilt, klikt u op **OK**, en klik vervolgens op **Schema-Editor** in het hoofdvenster. Ga vervolgens verder met de informatie over de schema-editor. Nadat het nieuwe schemabestand is gemaakt, moet u gaat u terug naar de **geavanceerde opties** venster om op te nemen van de zojuist gemaakte schemabestand.

1. Zodra u op Voltooien en sluit de **Setup van Azure Cosmos DB ODBC-stuurprogramma DSN** venster, de nieuwe gebruiker DSN-naam wordt toegevoegd aan de gebruikers-DSN-tabblad.

    ![Nieuwe Azure Cosmos DB ODBC DSN op het tabblad gebruikers-DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#container-mapping"></a>Stap 3: een schema definitie maken met behulp van de container toewijzings methode

Er zijn twee soorten bemonsterings methoden die u kunt gebruiken: **container toewijzing** of **tabel scheidings tekens**. Een steekproef sessie kan gebruikmaken van beide steekproef methoden, maar elke container kan alleen een specifieke steekproef methode gebruiken. Met de volgende stappen maakt u een schema voor de gegevens in een of meer containers met behulp van de container toewijzings methode. Met deze steekproef methode worden de gegevens in de pagina van een container opgehaald om de structuur van de gegevens te bepalen. Er wordt een container naar een tabel op de ODBC-zijde getransponeren. Deze steekproef methode is efficiënt en snel wanneer de gegevens in een container homo geen zijn. Als een container heterogene type gegevens bevat, kunt u het beste de [toewijzings methode voor tabel scheidingen](#table-mapping) gebruiken, aangezien deze een krachtigere bemonsterings methode biedt om de gegevens structuren in de container te bepalen. 

1. Klik na het volt ooien van de stappen 1-4 in [verbinding maken met uw Azure Cosmos-data base](#connect)op **schema-editor** in het venster **Azure Cosmos db odbc-stuur programma-DSN instellen** .

    ![Knop voor schema-editor in het venster Azure Cosmos DB ODBC-stuurprogramma DSN instellingen](./media/odbc-driver/odbc-driver-schema-editor.png)
1. In de **Schema-Editor** venster, klikt u op **nieuw**.
    In het venster **schema genereren** worden alle containers in het Azure Cosmos DB-account weer gegeven. 

1. Selecteer een of meer containers om voor beelden te selecteren en klik vervolgens op voor **beeld**. 

1. In de **ontwerpweergave** tabblad, de database, schema en tabel worden weergegeven. De scan weergegeven in de tabelweergave de set eigenschappen die zijn gekoppeld aan de namen van de kolommen (SQL-naam, naam van de gegevensbron, enzovoort).
    Voor elke kolom, kunt u de naam van de kolom SQL, de SQL-type, de SQL-lengte (indien van toepassing), schalen (indien van toepassing), de precisie (indien van toepassing) en de null-waarden bevatten.
    - U kunt instellen **kolom verbergen** naar **waar** als u wilt uitsluiten van die kolom van de resultaten van query. Kolommen gemarkeerd kolom verbergen = true niet worden geretourneerd voor de selectie en projectie, hoewel ze nog steeds deel van het schema uitmaken. U kunt bijvoorbeeld alle van de Azure Cosmos DB voor het systeem vereist-eigenschappen die beginnen met '_' verbergen.
    - De **id** kolom is het enige veld dat als deze wordt gebruikt als de primaire sleutel in het genormaliseerde schema kan niet worden verborgen. 

1. Wanneer u klaar bent met het definiëren van het schema, klikt u op **bestand** | **opslaan**, Ga naar de map op te slaan van het schema en klik vervolgens op **opslaan**.

1. Als u dit schema met een DSN wilt gebruiken, opent u het **venster Azure Cosmos DB ODBC driver DSN Setup** (via de ODBC-gegevens bron beheerder), klikt u op **Geavanceerde opties**en navigeert u in het vak **schema bestand** naar het opgeslagen schema. Opslaan van een schemabestand naar een bestaande DSN Hiermee wijzigt u de DSN-verbinding met bereik tot de gegevens en de definitie van schema-structuur.

## <a id="table-mapping"></a>Stap 4: De schemadefinitie van een met behulp van de tabel-scheidingstekens maken toewijzingsmethode

Er zijn twee soorten bemonsterings methoden die u kunt gebruiken: **container toewijzing** of **tabel scheidings tekens**. Een steekproef sessie kan gebruikmaken van beide steekproef methoden, maar elke container kan alleen een specifieke steekproef methode gebruiken. 

Met de volgende stappen maakt u een schema voor de gegevens in een of meer containers met behulp van de toewijzings methode voor **tabel scheidingen** . U wordt aangeraden deze steekproef methode te gebruiken wanneer uw containers heterogene gegevens typen bevatten. U kunt deze methode om het bereik van de meting naar een set met kenmerken en de bijbehorende waarden te gebruiken. Als een document bevat een eigenschap 'Type', kunt u bijvoorbeeld de steekproeven om de waarden van deze eigenschap te beperken. Het eindresultaat van de steekproeven is een set met tabellen voor elk van de waarden voor het Type dat u hebt opgegeven. Typ bijvoorbeeld = auto produceert een auto-tabel bij het Type = vlak geeft als resultaat een tabel van de gegevenslaag.

1. Klik na het volt ooien van de stappen 1-4 in [verbinding maken met uw Azure Cosmos-data base](#connect)op **schema-editor** in het venster Azure Cosmos DB ODBC-stuur programma-DSN instellen.

1. In de **Schema-Editor** venster, klikt u op **nieuw**.
    In het venster **schema genereren** worden alle containers in het Azure Cosmos DB-account weer gegeven. 

1. Selecteer een container op het tabblad **voor beeld weergave** in de kolom **toewijzings definitie** voor de container en klik op **bewerken**. Klik in de **Mapping Definition** venster **tabel scheidingstekens** methode. Ga daarna als volgt te werk:

    a. In de **kenmerken** typt u de naam van de eigenschap van een scheidingsteken. Dit is een eigenschap in het document dat u wilt het bereik van de steekproeven naar, bijvoorbeeld, plaats en druk op enter. 

    b. Als u wilt dat alleen als bereik voor de steekproeven aan bepaalde waarden voor het kenmerk dat u hierboven hebt ingevoerd, selecteert u het kenmerk in het selectiekader, typt u een waarde in de **waarde** in (bijvoorbeeld Amsterdam) en druk op invoeren. U kunt doorgaan met het toevoegen van meerdere waarden voor kenmerken. Zorg dat het juiste kenmerk is geselecteerd wanneer u waarden invoert.

    Als u bijvoorbeeld een **kenmerken** waarde van stad, en u wilt beperken van de tabel alleen rijen met de waarde van een stad New York en Dubai, voert u dus plaats in de kenmerken en New York en vervolgens Dubai in de **Waarden** vak.

1. Klik op **OK**. 

1. Klik na het volt ooien van de toewijzings definities voor de containers die u wilt bemonsteren in het venster **schema-editor** op voor **beeld**.
     Voor elke kolom, kunt u de naam van de kolom SQL, de SQL-type, de SQL-lengte (indien van toepassing), schalen (indien van toepassing), de precisie (indien van toepassing) en de null-waarden bevatten.
    - U kunt instellen **kolom verbergen** naar **waar** als u wilt uitsluiten van die kolom van de resultaten van query. Kolommen gemarkeerd kolom verbergen = true niet worden geretourneerd voor de selectie en projectie, hoewel ze nog steeds deel van het schema uitmaken. Bijvoorbeeld, kunt u alle Azure Cosmos DB voor het systeem vereist eigenschappen die zijn gestart met verbergen `_`.
    - De **id** kolom is het enige veld dat als deze wordt gebruikt als de primaire sleutel in het genormaliseerde schema kan niet worden verborgen. 

1. Wanneer u klaar bent met het definiëren van het schema, klikt u op **bestand** | **opslaan**, Ga naar de map op te slaan van het schema en klik vervolgens op **opslaan**.

1. Terug in de **Setup van Azure Cosmos DB ODBC-stuurprogramma DSN** venster, klikt u op **geavanceerde opties**. Klik in de **schemabestand** vak, Ga naar het schemabestand opgeslagen en klikt u op **OK**. Klik op **OK** opnieuw op te slaan van de DSN-naam. Dit bespaart het schema dat u hebt gemaakt naar de DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Optioneel) Gekoppelde serververbinding instellen

U kunt Azure Cosmos DB van SQL Server Management Studio (SSMS) door het instellen van een gekoppelde serververbinding een query.

1. Maak een gegevensbron, zoals beschreven in [stap 2](#connect)met de naam bijvoorbeeld `SDS Name`.

1. [SQL Server Management Studio installeren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en maak verbinding met de server. 

1. Maak in de SSMS-query-editor, een gekoppelde server-object `DEMOCOSMOS` voor de gegevensbron met de volgende opdrachten. Vervang `DEMOCOSMOS` met de naam voor de gekoppelde server en `SDS Name` met de naam van de gegevensbron van uw systeem.

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
    ![Een weergave van gegevens maken](./media/odbc-driver/odbc-driver-create-view.png)


Klik in de **weergavedefinities** venster de volgende handelingen uit:

1. Klik op **nieuw**, voer een naam voor de weergave, bijvoorbeeld EmployeesfromSeattleView en klik vervolgens op **OK**.

1. In de **weergave bewerken** venster, voert u een Azure Cosmos DB-query. Dit moet een [Azure Cosmos DB SQL-query](how-to-sql-query.md), bijvoorbeeld `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, en klik vervolgens op **OK**.

    ![Query bij het maken van een weergave toevoegen](./media/odbc-driver/odbc-driver-create-view-2.png)


Als u wilt, kunt u een groot aantal weergaven maken. Wanneer u klaar bent voor het definiëren van de weergaven die u kunt vervolgens steekproef de gegevens. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Stap 5: Uw gegevens weergeven in de BI-hulpprogramma's zoals Power BI Desktop

U kunt uw nieuwe DSN gebruiken om te verbinden met Azure Cosmos DB met een ODBC-hulpprogramma's: deze stap gewoon laat zien hoe u verbinding maakt met Power BI Desktop en maak een Power BI-visualisatie.

1. Open Power BI Desktop.

1. Klik op **gegevens ophalen**.

    ![Gegevens ophalen in Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. In de **gegevens ophalen** venster, klikt u op **andere** | **ODBC** | **Connect**.

    ![Kies ODBC-gegevensbron in gegevens ophalen voor Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. In de **uit ODBC** venster, selecteer de gegevensbron waarmee u hebt gemaakt en klik vervolgens op **OK**. U kunt laten de **geavanceerde opties** vermeldingen leeg.

    ![Kies de naam van gegevensbron (DSN) in gegevens ophalen voor Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. In de **toegang krijgen tot een gegevensbron met behulp van een ODBC-stuurprogramma** venster **standaard of aangepast** en klik vervolgens op **Connect**. U hoeft niet om op te nemen de **eigenschappen van een verbindingsreeks voor referenties**.

1. In de **Navigator** venster in het linkerdeelvenster, vouw de database, het schema, en selecteer vervolgens de tabel. Het deelvenster met resultaten bevat de gegevens met behulp van het schema dat u hebt gemaakt.

    ![Selecteer de tabel in Power BI-Get-gegevens](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Als u wilt de gegevens visualiseren in Power BI desktop, schakel het selectievakje in voor de naam van de tabel en klik vervolgens op **Load**.

1. In Power BI Desktop, uiterst links, selecteer het tabblad gegevens ![Tabblad gegevens op in Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) om te bevestigen van uw gegevens zijn geïmporteerd.

1. U kunt nu visuele elementen met behulp van Power BI door te klikken op het tabblad rapport maken ![tabblad rapport in Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), te klikken op **nieuwe Visual**, en vervolgens uw tegel aan te passen. Zie voor meer informatie over het maken van visualisaties in Power BI Desktop [visualisatietypen in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Problemen oplossen

Als u het volgende foutbericht krijgt, controleert u of de **Host** en **toegangssleutel** waarden die u hebt gekopieerd in de Azure-portal [stap 2](#connect) juist zijn en probeer het vervolgens opnieuw. Gebruik de kopieerknoppen aan de rechterkant van de **Host** en **toegangssleutel** waarden in de Azure-portal om te kopiëren van de waarden van gratis fout.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Volgende stappen

Zie [Welkom bij Azure Cosmos DB](introduction.md) voor meer informatie over Azure Cosmos DB.
