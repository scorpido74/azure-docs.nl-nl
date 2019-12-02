---
title: Werken met de node. js-back-end-server-SDK
description: Meer informatie over het werken met de node. js back-end-server-SDK voor Azure App Service Mobile Apps.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: fe5ced96a74bfbfbacf3ce1874d64711f6db7449
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668767"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>De Mobile Apps node. js-SDK gebruiken

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
>  Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

Dit artikel bevat gedetailleerde informatie en voor beelden die laten zien hoe u kunt werken met een node. js-back-end in de Mobile Apps-functie van Azure App Service.

## <a name="Introduction"></a>Inleiding

Mobile Apps biedt de mogelijkheid om een mobiel geoptimaliseerde Web-API voor gegevens toegang toe te voegen aan een webtoepassing. De Mobile Apps SDK wordt meegeleverd voor ASP.NET en node. js-webtoepassingen. De SDK biedt de volgende bewerkingen:

* Tabel bewerkingen (lezen, invoegen, bijwerken, verwijderen) voor toegang tot gegevens
* Aangepaste API-bewerkingen

Beide bewerkingen bieden verificatie voor alle id-providers die Azure App Service toestaan. Deze providers bevatten sociale id-providers zoals Facebook, Twitter, Google en micro soft, maar ook Azure Active Directory voor bedrijfs identiteit.

Voor elk use-case vindt u voor beelden in de [lijst met voor beelden op GitHub].

## <a name="supported-platforms"></a>Ondersteunde platforms

De Mobile Apps node. js SDK ondersteunt de huidige LTS-versie van het knoop punt en later. Momenteel is de meest recente versie van LTS knoop punt v 4.5.0. Andere versies van het knoop punt werken mogelijk wel, maar worden niet ondersteund.

De Mobile Apps node. js SDK ondersteunt twee database Stuur Programma's: 

* Het knoop punt-MSSQL-stuur programma ondersteunt Azure SQL Database en lokale SQL Server-exemplaren.  
* Het sqlite3-stuur programma ondersteunt alleen SQLite-data bases in één exemplaar.

### <a name="howto-cmdline-basicapp"></a>Een Basic node. js-back-end maken via de opdracht regel

Elke Mobile Apps node. js-back-end wordt gestart als een ExpressJS-toepassing. ExpressJS is het meest populaire Web Service-Framework dat beschikbaar is voor node. js. U kunt als volgt een eenvoudige [Express] -toepassing maken:

1. Maak in een opdracht-of Power shell-venster een map voor uw project:

        mkdir basicapp

1. Voer `npm init` uit om de pakket structuur te initialiseren:

        cd basicapp
        npm init

   De `npm init` opdracht vraagt een set vragen om het project te initialiseren. Bekijk het voor beeld van de uitvoer:

   ![De NPM init-uitvoer][0]

1. Installeer de `express`-en `azure-mobile-apps` bibliotheken vanuit de NPM-opslag plaats:

        npm install --save express azure-mobile-apps

1. Maak een app. js-bestand om de mobiele basis server te implementeren:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Met deze toepassing wordt een mobiel geoptimaliseerde Web-API gemaakt met één eind punt (`/tables/TodoItem`) dat niet-geverifieerde toegang biedt tot een onderliggend SQL-gegevens archief met behulp van een dynamisch schema. Het is geschikt voor het volgen van de Snelstartgids voor de client bibliotheek:

* [Quick start voor Android-client]
* [Snelstartgids voor Apache Cordova-client]
* [Quick start voor iOS-client]
* [Quick start voor Windows Store-client]
* [Quick start voor Xamarin. iOS-client]
* [Quick start voor Xamarin. Android-client]
* [Quick start voor Xamarin. Forms-client]

U kunt de code voor deze basis toepassing vinden in het [basicapp-voor beeld op github].

### <a name="howto-vs2015-basicapp"></a>Een node. js-back-end maken met behulp van Visual Studio 2015

Voor Visual Studio 2015 is een uitbrei ding vereist voor het ontwikkelen van node. js-toepassingen in de IDE. Installeer de [node. js-Hulpprogram ma's 1,1 voor Visual Studio]om te starten. Wanneer u klaar bent met de installatie, maakt u een Express 4. x-toepassing:

1. Open het dialoog venster **Nieuw project** (uit **bestand** > **Nieuw** > - **project**).
1. Vouw **sjablonen** uit > **Java script** > **node. js**.
1. Selecteer **Basic Azure node. js Express 4-toepassing**.
1. Vul de project naam in. Selecteer **OK**.

   ![Visual Studio 2015 nieuw project][1]
1. Klik met de rechter muisknop op het knoop punt **NPM** en selecteer **nieuwe NPM-pakketten installeren**.
1. Mogelijk moet u de NPM-catalogus vernieuwen nadat u uw eerste node. js-toepassing hebt gemaakt. Selecteer **vernieuwen** als dat nodig is.
1. Voer **Azure-Mobile-apps** in het zoekvak in. Selecteer het **Azure-Mobile-apps 2.0.0** -pakket en selecteer vervolgens **pakket installeren**.

   ![Nieuwe NPM-pakketten installeren][2]
1. Selecteer **Sluiten**.
1. Open het bestand app. js om ondersteuning toe te voegen voor de Mobile Apps SDK. Voeg de volgende code toe op regel 6 aan de onderkant van de bibliotheek `require`-instructies:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Voeg de volgende code toe op ongeveer regel 27 na de instructies van de andere `app.use`:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Sla het bestand op.

1. Voer de toepassing lokaal uit (de API wordt geleverd op `http://localhost:3000`) of publiceer naar Azure.

### <a name="create-node-backend-portal"></a>Een node. js-back-end maken met behulp van de Azure Portal

U kunt een Mobile Apps back-end direct maken in de [Azure-portal]. U kunt de volgende stappen uitvoeren of een client en server samen maken door de zelf studie [een mobiele app maken](app-service-mobile-ios-get-started.md) te volgen. De zelf studie bevat een vereenvoudigde versie van deze instructies en is het meest geschikt voor testen van concept projecten.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Klik in het deel venster **aan de slag** onder **Create a Table API**op **node. js** als uw back-end-taal.
Schakel het selectie vakje in om te **bevestigen dat hiermee alle site-inhoud wordt overschreven**en selecteer vervolgens **TodoItem-tabel maken**.

### <a name="download-quickstart"></a>De Quick Start-code van het node. js-project downloaden met behulp van Git

Wanneer u een node. js-Mobile Apps back-end maakt met behulp van het deel venster **Quick Start** van de portal, wordt er een node. js-project voor u gemaakt en geïmplementeerd op uw site. In de portal kunt u tabellen en Api's toevoegen en code bestanden bewerken voor de back-end van node. js. U kunt ook verschillende implementatie hulpprogramma's gebruiken om het back-end-project te downloaden, zodat u tabellen en Api's kunt toevoegen of wijzigen, en vervolgens het project opnieuw publiceert. Zie [Implementatie handleiding Azure App Service]) voor meer informatie.

In de volgende procedure wordt een Git-opslag plaats gebruikt voor het downloaden van de project code Quick Start:

1. Installeer Git als u dit nog niet hebt gedaan. De stappen die nodig zijn voor het installeren van Git, zijn afhankelijk van de besturings systemen. Zie [git installeren](https://git-scm.com/book/en/Getting-Started-Installing-Git)voor informatie over specifieke distributies en installatie richtlijnen voor besturings systemen.
2. Zie [uw opslag plaats voorbereiden](../app-service/deploy-local-git.md#prepare-your-repository) om de Git-opslag plaats voor uw back-end-site in te scha kelen. Noteer de gebruikers naam en het wacht woord voor de implementatie.
3. In het deel venster voor uw Mobile Apps back-end noteert u de instelling voor de **Git-kloon-URL** .
4. Voer de `git clone` opdracht uit met behulp van de Git-kloon-URL. Voer uw wacht woord in als dat vereist is, zoals in het volgende voor beeld:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Blader naar de lokale map (`/todolist` in het vorige voor beeld) en u ziet dat er project bestanden zijn gedownload. Zoek het bestand todoitem. json in de map `/tables`. In dit bestand worden de machtigingen voor de tabel gedefinieerd. Zoek ook het bestand todoitem. js in dezelfde map. Het definieert de scripts voor ruwe bewerkingen voor de tabel.
6. Nadat u wijzigingen hebt aangebracht in Project bestanden, voert u de volgende opdrachten uit om de wijzigingen toe te voegen, door te voeren en vervolgens te uploaden naar de site:

        $ git commit -m "updated the table script"
        $ git push origin master

   Wanneer u nieuwe bestanden aan het project toevoegt, moet u eerst de opdracht `git add .` uitvoeren.

De site wordt opnieuw gepubliceerd wanneer een nieuwe verzameling door Voer wordt gepusht naar de site.

### <a name="howto-publish-to-azure"></a>Uw node. js-back-end naar Azure publiceren

Microsoft Azure biedt veel mechanismen voor het publiceren van uw Mobile Apps node. js-back-end naar de Azure-service. Deze mechanismen omvatten implementatie hulpprogramma's die zijn geïntegreerd in Visual Studio, opdracht regel Programma's en continue implementatie opties op basis van broncode beheer. Zie [Implementatie handleiding Azure App Service]) voor meer informatie.

Azure App Service heeft specifiek advies voor node. js-toepassingen die u moet controleren voordat u de back-end publiceert:

* [De knooppunt versie opgeven]
* [Knooppunt modules gebruiken]

### <a name="howto-enable-homepage"></a>Een start pagina voor uw toepassing inschakelen

Veel toepassingen zijn een combi natie van web-en mobiele apps. U kunt het ExpressJS-Framework gebruiken om de twee facetten te combi neren. Soms wilt u echter alleen een mobiele interface implementeren. Het is handig om een start pagina op te geven om ervoor te zorgen dat de app service actief is. U kunt uw eigen start pagina opgeven of een tijdelijke start pagina inschakelen. Als u een tijdelijke start pagina wilt inschakelen, gebruikt u de volgende code om Mobile Apps te instantiëren:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Als u deze optie alleen beschikbaar wilt hebben bij het ontwikkelen van lokale bestanden, kunt u deze instelling toevoegen aan uw mobiele. js-bestand.

## <a name="TableOperations"></a>Tabel bewerkingen

De Azure-Mobile-apps node. js-Server-SDK biedt mechanismen voor het beschikbaar maken van gegevens tabellen die zijn opgeslagen in Azure SQL Database als een web-API. Het biedt vijf bewerkingen:

| Bewerking | Beschrijving |
| --- | --- |
| /Tables/-*tabel naam* ophalen |Alle records in de tabel ophalen. |
| /Tables/-*TableName*/:-id ophalen |Een specifieke record in de tabel ophalen. |
| /Tables/-*tabel naam* |Maak een record in de tabel. |
| PATCH/Tables/*TableName*/: id |Een record in de tabel bijwerken. |
| De/Tables/ */* :-id verwijderen |Een record in de tabel verwijderen. |

Deze web-API ondersteunt [OData] en breidt het tabel schema uit ter ondersteuning van [offline gegevens synchronisatie].

### <a name="howto-dynamicschema"></a>Tabellen definiëren met behulp van een dynamisch schema

Voordat u een tabel kunt gebruiken, moet u deze definiëren. U kunt tabellen definiëren met behulp van een statisch schema (waarin u de kolommen in het schema definieert) of dynamisch (waarbij de SDK het schema beheert op basis van binnenkomende aanvragen). Daarnaast kunt u specifieke aspecten van de Web-API beheren door Java script-code toe te voegen aan de definitie.

Als best practice moet u elke tabel definiëren in een Java script-bestand in de `tables` map en vervolgens de methode `tables.import()` gebruiken om de tabellen te importeren. Als u het voor beeld van een eenvoudige app uitbreidt, moet u het bestand app. js aanpassen:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Definieer de tabel in./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabellen gebruiken standaard een dynamisch schema. Als u het dynamische schema globaal wilt uitschakelen, stelt u de `MS_DynamicSchema` app-instelling in op False in de Azure Portal.

U kunt een volledig voor beeld vinden in het TODO-voor beeld [voor beeld van TODO op GitHub].

### <a name="howto-staticschema"></a>Tabellen definiëren met behulp van een statisch schema

U kunt de kolommen die beschikbaar zijn via de Web-API, expliciet definiëren. De Azure-Mobile-apps node. js SDK voegt automatisch extra kolommen toe die vereist zijn voor het offline synchroniseren van gegevens naar de lijst die u opgeeft. Voor de Quick Start-client toepassingen is bijvoorbeeld een tabel met twee kolommen vereist: `text` (een teken reeks) en `complete` (een Booleaanse waarde).  
De tabel kan als volgt worden gedefinieerd in het Java script-bestand van de tabel definitie (dat zich in de `tables` Directory bevindt):

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Als u de tabellen statisch definieert, moet u ook de `tables.initialize()`-methode aanroepen om het database schema te maken bij het opstarten. De methode `tables.initialize()` retourneert een [Toezeg] , zodat de webservice geen aanvragen bedient voordat de data base wordt geïnitialiseerd.

### <a name="howto-sqlexpress-setup"></a>SQL Server Express als een gegevens Archief voor ontwikkeling gebruiken op uw lokale computer

De Mobile Apps node. js SDK biedt drie opties voor het uitvoeren van gegevens uit het vak:

* Gebruik het stuur programma voor het **geheugen** om een niet-persistent voor beeld van een archief op te geven.
* Gebruik het **MSSQL** -stuur programma om een SQL Server Express gegevens archief op te geven voor ontwikkeling.
* Gebruik het **MSSQL** -stuur programma om een Azure SQL database gegevens archief op te geven voor productie.

De Mobile Apps node. js SDK gebruikt het [MSSQL node. js-pakket] om een verbinding met zowel SQL Server Express als SQL database te maken en te gebruiken. Voor dit pakket moet u TCP-verbindingen inschakelen op uw SQL Server Express-exemplaar.

> [!TIP]
> Het stuur programma voor het geheugen biedt geen volledige set voorzieningen om te testen. Als u uw back-end lokaal wilt testen, is het raadzaam om een SQL Server Express gegevens archief en het MSSQL-stuur programma te gebruiken.

1. Down load en Installeer [Microsoft SQL Server 2014 Express]. Zorg ervoor dat u de editie SQL Server 2014 Express met Hulpprogram Ma's installeert. Tenzij u ondersteuning voor 64 bits expliciet vereist, verbruikt de 32-bits versie minder geheugen wanneer u uitvoert.
1. Voer SQL Server 2014 Configuration Manager:

   a. Vouw het knoop punt **SQL Server netwerk configuratie** uit in het structuur menu.

   b. Selecteer **protocollen voor SQLEXPRESS**.

   c. Klik met de rechter muisknop op **TCP/IP** en selecteer **inschakelen**. Selecteer **OK** in het pop-upvenster.

   d. Klik met de rechter muisknop op **TCP/IP** en selecteer **Eigenschappen**.

   e. Selecteer het tabblad **IP-adressen** .

   f. Zoek het knoop punt **IPAll** . Voer **1433**in het veld **TCP-poort** in.

      ![SQL Server Express configureren voor TCP/IP][3]

   g. Selecteer **OK**. Selecteer **OK** in het pop-upvenster.

   h. Selecteer **SQL Server services** in het structuur menu.

   i. Klik met de rechter muisknop op **SQL Server (SQLEXPRESS)** en selecteer **opnieuw starten**.

   j. Sluit SQL Server 2014 Configuration Manager.

1. Voer SQL Server 2014 Management Studio uit en maak verbinding met uw lokale SQL Server Express-exemplaar:

   1. Klik met de rechter muisknop op uw exemplaar in Objectverkenner en selecteer **Eigenschappen**.
   1. Selecteer de pagina **beveiliging** .
   1. Zorg ervoor dat **SQL Server en Windows-verificatie modus** is geselecteerd.
   1. Selecteer **OK**.

      ![SQL Server Express-verificatie configureren][4]
   1. Vouw **beveiligings** > **aanmeldingen** in objectverkenner uit.
   1. Klik met de rechter muisknop op **aanmeldingen** en selecteer **nieuwe aanmelding**.
   1. Voer een aanmeldings naam in. Selecteer **SQL Server-verificatie**. Voer een wacht woord in en geef hetzelfde wacht woord op bij **wacht woord bevestigen**. Het wacht woord moet voldoen aan de complexiteits vereisten van Windows.
   1. Selecteer **OK**.

      ![Een nieuwe gebruiker toevoegen aan SQL Server Express][5]
   1. Klik met de rechter muisknop op uw nieuwe aanmelding en selecteer **Eigenschappen**.
   1. Selecteer de pagina **Server functies** .
   1. Schakel het selectie vakje in voor de server functie **dbcreator** .
   1. Selecteer **OK**.
   1. Sluit SQL Server 2015 Management Studio.

Zorg ervoor dat u de gebruikers naam en het wacht woord noteert die u hebt geselecteerd. U moet mogelijk extra server functies of machtigingen toewijzen, afhankelijk van uw database vereisten.

De node. js-toepassing leest de `SQLCONNSTR_MS_TableConnectionString` omgevings variabele voor de connection string voor deze data base. U kunt deze variabele instellen in uw omgeving. U kunt bijvoorbeeld Power shell gebruiken om deze omgevings variabele in te stellen:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Toegang tot de data base via een TCP/IP-verbinding. Geef een gebruikers naam en wacht woord op voor de verbinding.

### <a name="howto-config-localdev"></a>Uw project configureren voor lokale ontwikkeling

Mobile Apps leest een Java script-bestand met de naam *mobiele. js* van het lokale bestands systeem. Gebruik dit bestand niet voor het configureren van de Mobile Apps SDK in productie. Gebruik in plaats daarvan **app-instellingen** in de [Azure-portal].

Het mobiele. js-bestand moet een configuratie object exporteren. De meest voorkomende instellingen zijn:

* Data base-instellingen
* Instellingen voor diagnostische logboek registratie
* Alternatieve CORS-instellingen

In dit voor beeld **mobiele. js** -bestand worden de voor gaande data base-instellingen geïmplementeerd:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

We raden u aan om **mobiele. js** toe te voegen aan uw **. gitignore** -bestand (of een ander broncode beheer bestand) om te voor komen dat wacht woorden worden opgeslagen in de Cloud. Configureer instellingen voor de productie altijd in de **app-instellingen** binnen de [Azure-portal].

### <a name="howto-appsettings"></a>App-instellingen voor uw mobiele app configureren

De meeste instellingen in het bestand mobiele. js hebben een equivalente app-instelling in de [Azure-portal]. Gebruik de volgende lijst om uw app te configureren in de **app-instellingen**:

| App-instelling | de instelling mobiele. js | Beschrijving | Geldige waarden |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |De naam van de app |string |
| **MS_MobileLoggingLevel** |Logging. level |Mini maal logboek niveau van te registreren berichten |fout, waarschuwing, info, uitgebreid, fouten opsporen, Silly |
| **MS_DebugMode** |fout opsporing |Hiermee wordt de foutopsporingsmodus in-of uitgeschakeld |waar, onwaar |
| **MS_TableSchema** |data. schema |Standaard schema naam voor SQL-tabellen |teken reeks (standaard: dbo) |
| **MS_DynamicSchema** |data. dynamicSchema |Hiermee wordt de foutopsporingsmodus in-of uitgeschakeld |waar, onwaar |
| **MS_DisableVersionHeader** |versie (ingesteld op niet gedefinieerd) |Hiermee wordt de X-ZUMO-Server-versie-header uitgeschakeld |waar, onwaar |
| **MS_SkipVersionCheck** |skipversioncheck |Hiermee schakelt u de client API-versie controle uit |waar, onwaar |

Een app-instelling instellen:

1. Meld u aan bij de [Azure-portal].
1. Selecteer **alle resources** of **app Services**en selecteer vervolgens de naam van uw mobiele app.
1. Het deel venster **instellingen** wordt standaard geopend. Als dat niet het geval is, selecteert u **instellingen**.
1. Selecteer **Toepassings instellingen**in het menu **Algemeen** .
1. Ga naar de sectie **app-instellingen** .
1. Als uw app-instelling al bestaat, selecteert u de waarde van de app-instelling om de waarde te bewerken.
   Als uw app-instelling niet bestaat, voert u de app-instelling in het vak **sleutel** en de waarde in het vak **waarde** in.
1. Selecteer **Opslaan**.

Voor het wijzigen van de meeste app-instellingen moet de service opnieuw worden gestart.

### <a name="howto-use-sqlazure"></a>SQL Database gebruiken als uw productie gegevens archief

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Het gebruik van Azure SQL Database als een gegevens archief is identiek voor alle Azure App Service toepassings typen. Als u dit nog niet hebt gedaan, volgt u deze stappen om een Mobile Apps back-end te maken:

1. Meld u aan bij de [Azure-portal].
1. Selecteer in de linkerbovenhoek van het venster de knop **+ nieuw** > **Web en mobiel** > **mobiele app**en geef een naam op voor uw Mobile apps back-end.
1. Voer in het vak **resource groep** dezelfde naam in als uw app.
1. Het standaard App Service plan wordt geselecteerd. Als u uw App Service plan wilt wijzigen:

   a. Selecteer **app Service Plan** >  **+ nieuwe maken**.

   b. Geef een naam op voor het nieuwe App Service plan en selecteer een geschikte locatie.

   c. Selecteer een geschikte prijs categorie voor de service. Selecteer **alles weer geven** om meer prijs opties weer te geven, zoals **gratis** en **gedeeld**.

   d. Klik op de knop **selecteren** .

   e. Selecteer **OK**in het deel venster **app service plan** .
1. Selecteer **Maken**.

Het inrichten van een Mobile Apps back-end kan een paar minuten duren. Nadat de Mobile Apps back-end is ingericht, opent de portal het deel venster **instellingen** voor de Mobile apps back-end.

U kunt ervoor kiezen om een bestaand SQL database te koppelen aan uw Mobile Apps back-end of een nieuwe SQL database te maken. In deze sectie maken we een SQL database.

> [!NOTE]
> Als u al een Data Base op dezelfde locatie hebt als de Mobile Apps back-end, kunt u in plaats daarvan **een bestaande Data Base gebruiken** selecteren en vervolgens die data base selecteren. Het gebruik van een Data Base op een andere locatie wordt niet aanbevolen vanwege hogere latenties.

1. Selecteer in de nieuwe Mobile Apps back-end **instellingen** > **mobiele App** > **gegevens** >  **+ toevoegen**.
1. Selecteer in het deel venster **gegevens verbinding toevoegen** de optie **SQL database-vereiste instellingen configureren** > **een nieuwe data base maken**. Voer de naam van de nieuwe data base in het vak **naam** in.
1. Selecteer **Server**. Voer in het deel venster **nieuwe server** een unieke server naam in het vak **Server naam** in en geef een geschikte aanmeldings-en wacht woord voor de server beheerder op. Zorg ervoor dat **Azure-Services toegang tot de server toestaan** is geselecteerd. Selecteer **OK**.

   ![Een Azure SQL-database maken][6]
1. Selecteer **OK**in het deel venster **nieuwe data base** .
1. In het deel venster **gegevens verbinding toevoegen** selecteert u **verbindings reeks**en voert u de aanmeldings naam en het wacht woord in die u hebt opgegeven tijdens het maken van de data base. Als u een bestaande data base gebruikt, moet u de aanmeldings referenties voor die data base opgeven. Selecteer **OK**.
1. Selecteer in het deel venster **gegevens verbinding toevoegen** de optie **OK** om de data base te maken.

<!--- END OF ALTERNATE INCLUDE -->

Het maken van de data base kan enkele minuten duren. Gebruik het gebied **meldingen** om de voortgang van de implementatie te bewaken. Pas de voortgang aan als de data base is geïmplementeerd. Nadat de data base is geïmplementeerd, wordt er een connection string gemaakt voor het SQL Database exemplaar in uw Mobile Apps back-end-app-instellingen. U kunt deze app-instelling zien in **instellingen** > **Toepassings instellingen** > **verbindings reeksen**.

### <a name="howto-tables-auth"></a>Verificatie vereisen voor toegang tot tabellen

Als u App Service verificatie met het `tables`-eind punt wilt gebruiken, moet u eerst App Service-verificatie configureren in de [Azure-portal] . Zie de configuratie handleiding voor de ID-provider die u wilt gebruiken voor meer informatie:

* [Azure Active Directory-verificatie configureren]
* [Facebook-verificatie configureren]
* [Google-verificatie configureren]
* [Micro soft-verificatie configureren]
* [Twitter-verificatie configureren]

Elke tabel heeft een eigenschap Access die u kunt gebruiken om de toegang tot de tabel te beheren. Het volgende voor beeld toont een statisch gedefinieerde tabel waarvoor verificatie is vereist.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

De eigenschap Access kan een van de drie volgende waarden hebben:

* *Anonymous* geeft aan dat de client toepassing gegevens mag lezen zonder authenticatie.
* *geverifieerd* geeft aan dat de client toepassing een geldig verificatie token moet verzenden met de aanvraag.
* *uitgeschakeld* geeft aan dat deze tabel momenteel is uitgeschakeld.

Als de eigenschap Access niet is gedefinieerd, is niet-geverifieerde toegang toegestaan.

### <a name="howto-tables-getidentity"></a>Verificatie claims gebruiken met uw tabellen
U kunt verschillende claims instellen die worden aangevraagd wanneer authenticatie is ingesteld. Deze claims zijn normaal gesp roken niet beschikbaar via het `context.user`-object. U kunt ze echter wel ophalen met behulp van de `context.user.getIdentity()` methode. De methode `getIdentity()` retourneert een Promise die wordt omgezet in een object. Het object wordt gesleuteld op basis van de verificatie methode (`facebook`, `google`, `twitter`, `microsoftaccount`of `aad`).

Als u bijvoorbeeld Microsoft-account-verificatie hebt ingesteld en de claim voor de e-mail adressen hebt aangevraagd, kunt u het e-mail adres toevoegen aan de record met de volgende tabel controller:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Als u wilt zien welke claims er beschikbaar zijn, gebruikt u een webbrowser om het `/.auth/me`-eind punt van uw site weer te geven.

### <a name="howto-tables-disabled"></a>Toegang tot specifieke tabel bewerkingen uitschakelen

Naast de tabel kan de eigenschap Access worden gebruikt voor het beheren van afzonderlijke bewerkingen. Er zijn vier bewerkingen:

* `read` is de REST GET-bewerking voor de tabel.
* `insert` is de REST POST-bewerking op de tabel.
* `update` is de REST PATCH bewerking voor de tabel.
* `delete` is de REST VERWIJDERings bewerking voor de tabel.

U kunt bijvoorbeeld een niet-geverifieerde alleen-lezen tabel opgeven:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>De query die wordt gebruikt met tabel bewerkingen, aanpassen

Een veelvoorkomende vereiste voor tabel bewerkingen is het bieden van een beperkte weer gave van de gegevens. U kunt bijvoorbeeld een tabel opgeven die is gelabeld met de geverifieerde gebruikers-ID, zodat u alleen uw eigen records kunt lezen of bijwerken. De volgende tabel definitie biedt deze functionaliteit:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

Bewerkingen waarbij een query normaal gesp roken wordt uitgevoerd, hebben een query-eigenschap die u kunt aanpassen met behulp van een `where`-component. De eigenschap query is een [QueryJS] -object dat wordt gebruikt om een OData-query te converteren naar iets dat de back-end van de gegevens kan verwerken. Voor eenvoudige gelijkheids cases (zoals de voor gaande) kunt u een kaart gebruiken. U kunt ook specifieke SQL-componenten toevoegen:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Een tijdelijke verwijdering voor een tabel configureren

Bij een tijdelijke verwijdering worden records niet echt verwijderd. In plaats daarvan worden deze gemarkeerd als verwijderd in de data base door de kolom verwijderd in te stellen op True. De Mobile Apps SDK verwijdert automatisch verwijderde records uit de resultaten, tenzij de mobiele client-SDK gebruikmaakt van `IncludeDeleted()`. Als u een tabel voor een zacht verwijderen wilt configureren, stelt u de eigenschap `softDelete` in het definitie bestand van de tabel in:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

U moet een mechanisme voor het verwijderen van records instellen: een client toepassing, een Webtaak, een Azure-functie of een aangepaste API.

### <a name="howto-tables-seeding"></a>Uw data base seeden met gegevens

Wanneer u een nieuwe toepassing maakt, wilt u mogelijk een tabel met gegevens seeden. U kunt dit als volgt doen in het Java script-bestand van de tabel definitie:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Seeding van gegevens gebeurt alleen wanneer u de Mobile Apps SDK hebt gebruikt om de tabel te maken. Als de tabel al in de data base bestaat, worden er geen gegevens in de tabel geïnjecteerd. Als het dynamische schema is ingeschakeld, wordt het schema afgeleid van de seeded data.

U wordt aangeraden de methode `tables.initialize()` expliciet aan te roepen om de tabel te maken wanneer de service wordt gestart.

### <a name="Swagger"></a>Swagger-ondersteuning inschakelen
Mobile Apps wordt geleverd met ingebouwde [Swagger] -ondersteuning. Als u Swagger-ondersteuning wilt inschakelen, installeert u eerst Swagger-UI als afhankelijkheid:

    npm install --save swagger-ui

U kunt vervolgens Swagger-ondersteuning inschakelen in de Mobile Apps-constructor:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Waarschijnlijk wilt u de ondersteuning voor Swagger alleen inschakelen in de ontwikkelings edities. U kunt dit doen met behulp van de app-instelling `NODE_ENV`:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Het `swagger`-eind punt bevindt zich op http://*yoursite*. azurewebsites.net/Swagger. U kunt de Swagger-gebruikers interface openen via het `/swagger/ui`-eind punt. Als u ervoor kiest om verificatie voor uw hele toepassing te vereisen, geeft Swagger een fout op. Voor de beste resultaten kunt u ervoor kiezen om niet-geverifieerde aanvragen toe te staan in de Azure App Service verificatie/autorisatie-instellingen en vervolgens de verificatie te beheren met behulp van de eigenschap `table.access`.

U kunt ook de Swagger-optie toevoegen aan uw mobiele. js-bestand als u alleen Swagger-ondersteuning wilt voor het ontwikkelen van lokale bestanden.

## <a name="a-namepushpush-notifications"></a><a name="push"/>push meldingen

Mobile Apps integreert met Azure Notification Hubs zodat u gerichte push meldingen kunt verzenden naar miljoenen apparaten op alle grote platforms. U kunt met behulp van Notification Hubs push meldingen verzenden naar iOS-, Android-en Windows-apparaten. Zie [Notification hubs Overview](../notification-hubs/notification-hubs-push-notification-overview.md)voor meer informatie over alles wat u met Notification hubs kunt doen.

### <a name="send-push"></a>Push meldingen verzenden

De volgende code laat zien hoe u het `push`-object kunt gebruiken om een push melding naar de geregistreerde iOS-apparaten te verzenden:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Als u een sjabloon push registratie maakt van de client, kunt u in plaats daarvan een sjabloon push bericht verzenden naar apparaten op alle ondersteunde platforms. De volgende code laat zien hoe u een sjabloon melding verzendt:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>Push meldingen naar een geverifieerde gebruiker verzenden met behulp van Tags
Wanneer een geverifieerde gebruiker zich aanmeldt voor push meldingen, wordt er automatisch een label voor de gebruikers-ID toegevoegd aan de registratie. Met deze tag kunt u push meldingen verzenden naar alle apparaten die zijn geregistreerd door een specifieke gebruiker. Met de volgende code wordt de SID van de gebruiker die de aanvraag doet, opgehaald en wordt een sjabloon push melding verzonden naar elke apparaatregistratie voor die gebruiker:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Wanneer u registreert voor push meldingen van een geverifieerde client, moet u ervoor zorgen dat de verificatie is voltooid voordat u een registratie uitvoert.

## <a name="CustomAPI"></a>Aangepaste Api's

### <a name="howto-customapi-basic"></a>Een aangepaste API definiëren

Naast de API voor gegevens toegang via het `/tables`-eind punt, kunt Mobile Apps aangepaste API-dekking bieden. Aangepaste Api's worden op een vergelijk bare manier gedefinieerd als de tabel definities en hebben toegang tot dezelfde faciliteiten, inclusief verificatie.

Als u App Service verificatie met een aangepaste API wilt gebruiken, moet u eerst App Service-verificatie in de [Azure-portal] configureren. Zie de configuratie handleiding voor de ID-provider die u wilt gebruiken voor meer informatie:

* [Azure Active Directory-verificatie configureren]
* [Facebook-verificatie configureren]
* [Google-verificatie configureren]
* [Micro soft-verificatie configureren]
* [Twitter-verificatie configureren]

Aangepaste Api's worden op ongeveer dezelfde manier gedefinieerd als de Table-API:

1. Een `api` Directory maken.
1. Maak een Java script-bestand van de API-definitie in de map `api`.
1. Gebruik de import methode om de `api` map te importeren.

Hier volgt de prototype-API-definitie op basis van het voor beeld van de Basic-app die we eerder hebben gebruikt:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Laten we een voor beeld van een API die de server datum retourneert met behulp van de `Date.now()` methode. Dit is het API/date. js-bestand:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Elke para meter is een van de standaard REST woorden: GET, POST, PATCH of DELETE. De methode is een standaard [ExpressJS-middleware] -functie die de vereiste uitvoer verzendt.

### <a name="howto-customapi-auth"></a>Verificatie vereisen voor toegang tot een aangepaste API

De Mobile Apps SDK implementeert verificatie op dezelfde manier voor zowel het `tables`-eind punt als aangepaste Api's. Als u verificatie wilt toevoegen aan de API die in de vorige sectie is ontwikkeld, voegt u een `access` eigenschap toe:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

U kunt ook verificatie voor specifieke bewerkingen opgeven:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Het token dat wordt gebruikt voor het `tables`-eind punt moet worden gebruikt voor aangepaste Api's waarvoor verificatie is vereist.

### <a name="howto-customapi-auth"></a>Uploads van grote bestanden verwerken

De Mobile Apps SDK gebruikt de [gemiddlewarede Body-parser](https://github.com/expressjs/body-parser) om hoofd inhoud in uw inzending te accepteren en te decoderen. U kunt de hoofd tekst-parser vooraf configureren voor het accepteren van grotere uploads van bestanden:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

Het bestand is base-64-code ring vóór verzen ding. Met deze code ring wordt de grootte van de daad werkelijke upload (en de grootte die u moet account voor) verg Roten.

### <a name="howto-customapi-sql"></a>Aangepaste SQL-instructies uitvoeren

De Mobile Apps SDK biedt toegang tot de hele context via het aanvraag object. U kunt eenvoudig SQL-instructies met para meters uitvoeren voor de gedefinieerde gegevens provider:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>Foutopsporingsmodus

### <a name="howto-diagnostic-logs"></a>Mobile Apps fouten opsporen, vaststellen en problemen oplossen

Azure App Service biedt verschillende technieken voor fout opsporing en probleem oplossing voor node. js-toepassingen.
Raadpleeg de volgende artikelen om aan de slag te gaan met het oplossen van problemen met het knoop punt. js Mobile Apps back-end:

* [Bewakings Azure App Service]
* [Diagnostische logboek registratie inschakelen in Azure App Service]
* [Problemen met Azure App Service in Visual Studio oplossen]

Node. js-toepassingen hebben toegang tot een breed scala aan hulpprogram ma's voor Diagnostische logboeken. Intern gebruikt de Mobile Apps node. js SDK [Winston] voor diagnostische logboek registratie. Logboek registratie wordt automatisch ingeschakeld wanneer u de foutopsporingsmodus inschakelt of de `MS_DebugMode` app-instelling instelt op waar in de [Azure-portal]. Gegenereerde logboeken worden weer gegeven in de diagnostische Logboeken in de [Azure-portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Quick start voor Android-client]: app-service-mobile-android-get-started.md
[Snelstartgids voor Apache Cordova-client]: app-service-mobile-cordova-get-started.md
[Quick start voor iOS-client]: app-service-mobile-ios-get-started.md
[Quick start voor Xamarin. iOS-client]: app-service-mobile-xamarin-ios-get-started.md
[Quick start voor Xamarin. Android-client]: app-service-mobile-xamarin-android-get-started.md
[Quick start voor Xamarin. Forms-client]: app-service-mobile-xamarin-forms-get-started.md
[Quick start voor Windows Store-client]: app-service-mobile-windows-store-dotnet-get-started.md
[offline gegevens synchronisatie]: app-service-mobile-offline-data-sync.md
[Azure Active Directory-verificatie configureren]: ../app-service/configure-authentication-provider-aad.md
[Facebook-verificatie configureren]: ../app-service/configure-authentication-provider-facebook.md
[Google-verificatie configureren]: ../app-service/configure-authentication-provider-google.md
[Micro soft-verificatie configureren]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter-verificatie configureren]: ../app-service/configure-authentication-provider-twitter.md
[Implementatie handleiding Azure App Service]: ../app-service/deploy-local-git.md
[Bewakings Azure App Service]: ../app-service/web-sites-monitor.md
[Diagnostische logboek registratie inschakelen in Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Problemen met Azure App Service in Visual Studio oplossen]: ../app-service/troubleshoot-dotnet-visual-studio.md
[de knooppunt versie opgeven]: ../nodejs-specify-node-version-azure-apps.md
[Knooppunt modules gebruiken]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure-portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Toezeg]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp-voor beeld op GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[voor beeld van TODO op GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[lijst met voor beelden op GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node. js-Hulpprogram Ma's 1,1 voor Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL node. js-pakket]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS-middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
