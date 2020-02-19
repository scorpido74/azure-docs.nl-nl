---
title: Offline synchronisatie inschakelen (Cordova)
description: Meer informatie over het gebruik van App Service mobiele app om offline gegevens in uw Cordova-toepassing in de cache op te slaan en te synchroniseren.
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5a2d5ec8da5c1a317039e656f6df884a10efe7c3
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459415"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Cordova-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
In deze zelf studie wordt de functie voor offline synchronisatie van Azure Mobile Apps voor Cordova geïntroduceerd. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app&mdash;het weer geven, toevoegen of wijzigen van gegevens&mdash;zelfs wanneer er geen netwerk verbinding is. Wijzigingen worden opgeslagen in een lokale data base.  Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

Deze zelf studie is gebaseerd op de Cordova Snelstartgids voor Mobile Apps die u maakt wanneer u de zelf studie [Apache Cordova Quick Start]voltooit. In deze zelf studie werkt u de Quick Start-oplossing bij om offline functies van Azure Mobile Apps toe te voegen.  We markeren ook de offline-specifieke code in de app.

Zie het onderwerp [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over de functie voor offline synchronisatie. Zie de [API-documentatie](https://azure.github.io/azure-mobile-apps-js-client)voor meer informatie over het API-gebruik.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Offline synchronisatie toevoegen aan de Quick Start-oplossing
De offline synchronisatie code moet worden toegevoegd aan de app. Voor offline synchronisatie is de Cordova-sqlite-opslag-invoeg toepassing vereist, die automatisch wordt toegevoegd aan uw app wanneer de Azure Mobile Apps-invoeg toepassing is opgenomen in het project. Het Quick start-project bevat beide invoeg toepassingen.

1. Open in Visual Studio de Solution Explorer index. js en vervang de volgende code

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    met deze code:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Vervang vervolgens de volgende code:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    met deze code:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    De voor gaande code toevoegingen initialiseert het lokale archief en definieert een lokale tabel die overeenkomt met de kolom waarden die worden gebruikt in uw Azure back-end. (U hoeft niet alle kolom waarden in deze code op te kunnen gebruiken.)  Het veld `version` wordt onderhouden door de mobiele back-end en wordt gebruikt voor het oplossen van conflicten.

    U krijgt een verwijzing naar de synchronisatie context door **getSyncContext**aan te roepen. De synchronisatie context helpt bij het bewaren van tabel relaties door wijzigingen in alle tabellen bij te houden en te pushen die een client-app heeft gewijzigd wanneer `.push()` wordt aangeroepen.

3. Werk de URL van de toepassing bij naar de URL van uw mobiele App-toepassing.

4. Vervang vervolgens de volgende code:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    met deze code:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    De voor gaande code initialiseert de synchronisatie context en roept vervolgens getSyncTable (in plaats van GetTable) aan om een verwijzing naar de lokale tabel op te halen.

    Deze code maakt gebruik van de lokale Data Base voor alle bewerkingen voor maken, lezen, bijwerken en verwijderen (ruwe).

    In dit voor beeld wordt een eenvoudige fout afhandeling uitgevoerd voor synchronisatie conflicten. Met een echte toepassing worden de verschillende fouten verwerkt, zoals netwerk omstandigheden, Server conflicten en andere. Zie voor code voorbeelden het voor [beeld van offline synchronisatie].

5. Voeg vervolgens deze functie toe om de werkelijke synchronisatie uit te voeren.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    U bepaalt wanneer u wijzigingen naar de back-end van de mobiele app pusht door **syncContext. push ()** aan te roepen. U kunt bijvoorbeeld **syncBackend** aanroepen in een knop gebeurtenis-handler die is gekoppeld aan een knop voor synchroniseren.

## <a name="offline-sync-considerations"></a>Overwegingen voor offline synchronisatie

In het voor beeld wordt de **Push** -methode van **syncContext** alleen aangeroepen bij het opstarten van de app in de call back functie voor aanmelden.  In een echte toepassing kunt u ook deze synchronisatie functionaliteit hand matig activeren of wanneer de netwerk status wordt gewijzigd.

Wanneer er een pull-bewerking wordt uitgevoerd voor een tabel met wachtende lokale updates die worden bijgehouden door de context, activeert de pull-bewerkingen automatisch een push. Bij het vernieuwen, toevoegen en volt ooien van items in dit voor beeld kunt u de expliciete **Push** aanroep weglaten, omdat deze mogelijk overbodig is.

In de gegeven code worden alle records in de tabel externe todoItem opgevraagd, maar het is ook mogelijk om records te filteren door een query-ID en een query naar **pushen**door te geven. Zie de sectie *incrementele synchronisatie* in [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie.

## <a name="optional-disable-authentication"></a>Beschrijving Verificatie uitschakelen

Als u geen verificatie wilt instellen voordat u de offline synchronisatie test, moet u de call back-functie voor aanmelding uitlichten, maar de code in de call back-functie ongewijzigd laten.  Na het afmelden van de aanmeldings regels volgt de code:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nu wordt de app gesynchroniseerd met de Azure-back-end wanneer u de app uitvoert.

## <a name="run-the-client-app"></a>De client-app uitvoeren
Als offline synchronisatie nu is ingeschakeld, kunt u de client toepassing ten minste één keer op elk platform uitvoeren om de lokale store-data base te vullen. Daarna kunt u een offline scenario simuleren en de gegevens in het lokale archief wijzigen terwijl de app offline is.

## <a name="optional-test-the-sync-behavior"></a>Beschrijving Het synchronisatie gedrag testen
In deze sectie wijzigt u het client project om een offline scenario te simuleren door gebruik te maken van een ongeldige toepassings-URL voor uw back-end. Wanneer u gegevens items toevoegt of wijzigt, worden deze wijzigingen opgeslagen in het lokale archief, maar worden ze niet gesynchroniseerd met het back-end-gegevens archief totdat de verbinding opnieuw tot stand is gebracht.

1. Open in het Solution Explorer het project bestand index. js en wijzig de URL van de toepassing zodat deze verwijst naar een ongeldige URL, zoals in de volgende code:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Werk in index. html het CSP-`<meta>` element bij met dezelfde ongeldige URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Bouw en voer de client-app uit en controleer of er een uitzonde ring wordt vastgelegd in de console wanneer de app na de aanmelding met de back-end probeert te synchroniseren. Nieuwe items die u toevoegt, bestaan alleen in het lokale archief totdat ze naar de mobiele back-end worden gepusht. De client-app gedraagt zich alsof deze is verbonden met de back-end.

4. Sluit de app en start deze opnieuw om te controleren of de nieuwe items die u hebt gemaakt, zijn opgeslagen in het lokale archief.

5. Beschrijving Gebruik Visual Studio om uw Azure SQL Database tabel weer te geven om te zien dat de gegevens in de back-end-data base niet zijn gewijzigd.

    Open **Server Explorer**in Visual Studio. Navigeer naar uw data base in **Azure**->**SQL-data bases**. Klik met de rechter muisknop op de data base en selecteer **openen in SQL Server-objectverkenner**. U kunt nu naar de SQL database-tabel en de inhoud bladeren.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Beschrijving De opnieuw verbinding met uw mobiele back-end testen

In deze sectie verbindt u de app opnieuw met de mobiele back-end, waarmee de app wordt gesimuleerd naar een online status. Wanneer u zich aanmeldt, worden gegevens gesynchroniseerd met uw mobiele back-end.

1. Open index. js opnieuw en herstel de toepassings-URL.
2. Open index. HTML opnieuw en corrigeer de toepassings-URL in het onderdeel CSP-`<meta>`.
3. Bouw en voer de client-app opnieuw uit. De app probeert na het aanmelden te synchroniseren met de back-end van de mobiele app. Controleer of er geen uitzonde ringen worden vastgelegd in de console voor fout opsporing.
4. Beschrijving Bekijk de bijgewerkte gegevens met behulp van SQL Server-objectverkenner of een REST hulp programma, zoals Fiddler. U ziet dat de gegevens zijn gesynchroniseerd tussen de back-end-data base en het lokale archief.

    U ziet dat de gegevens zijn gesynchroniseerd tussen de data base en het lokale archief en de items bevat die u hebt toegevoegd toen de verbinding van uw app werd verbroken.

## <a name="additional-resources"></a>Aanvullende resources
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Volgende stappen
* Bekijk meer geavanceerde functies voor offline synchronisatie, zoals conflict oplossing in het voor [beeld van offline synchronisatie]
* Raadpleeg de API-naslag voor offline synchronisatie in de [API-documentatie](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova Quick Start]: app-service-mobile-cordova-get-started.md
[beeld van offline synchronisatie]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
