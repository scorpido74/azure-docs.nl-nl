---
title: Offline synchronisatie inschakelen (Cordova)
description: Meer informatie over het gebruik van App Service Mobile App om offline gegevens in uw Cordova-toepassing in de cache op te nemen en te synchroniseren.
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5a2d5ec8da5c1a317039e656f6df884a10efe7c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459415"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Cordova-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
Deze zelfstudie introduceert de offline synchronisatiefunctie van Azure Mobile Apps voor Cordova. Met offline synchronisatie kunnen eindgebruikers communiceren&mdash;met een mobiele app&mdash;die gegevens bekijkt, toevoegt of wijzigt, zelfs als er geen netwerkverbinding is. Wijzigingen worden opgeslagen in een lokale database.  Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

Deze zelfstudie is gebaseerd op de Cordova quickstart-oplossing voor mobiele apps die u maakt wanneer u de zelfstudie [Apache Cordova snel start.] In deze zelfstudie werkt u de quickstart-oplossing bij om offline functies van Azure Mobile Apps toe te voegen.  We markeren ook de offline-specifieke code in de app.

Zie het onderwerp [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over de functie offline synchronisatie. Zie de [API-documentatie](https://azure.github.io/azure-mobile-apps-js-client)voor meer informatie over API-gebruik.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Offlinesynchronisatie toevoegen aan de quickstart-oplossing
De offline synchronisatiecode moet aan de app worden toegevoegd. Offline synchronisatie vereist de plug-in cordova-sqlite-storage, die automatisch aan uw app wordt toegevoegd wanneer de Plug-in Azure Mobile Apps in het project is opgenomen. Het Quickstart-project omvat beide plug-ins.

1. Open index.js in de Solution Explorer van Visual Studio en vervang de volgende code

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

    De voorgaande codetoevoegingen initialiseren de lokale winkel en definiëren een lokale tabel die overeenkomt met de kolomwaarden die worden gebruikt in uw Azure-back-end. (U hoeft niet alle kolomwaarden in deze code op te nemen.)  Het `version` veld wordt onderhouden door de mobiele backend en wordt gebruikt voor conflictoplossing.

    U krijgt een verwijzing naar de synchronisatiecontext door **getSyncContext**aan te roepen. De synchronisatiecontext helpt tabelrelaties te behouden door wijzigingen bij te `.push()` houden en te pushen in alle tabellen die een client-app heeft gewijzigd wanneer deze wordt aangeroepen.

3. Werk de URL van de toepassing bij naar de URL van uw mobiele app-toepassing.

4. Vervang vervolgens deze code:

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

    De vorige code initialiseert de synchronisatiecontext en roept vervolgens getSyncTable (in plaats van getTable) aan om een verwijzing naar de lokale tabel te krijgen.

    Deze code gebruikt de lokale database voor alle tabelbewerkingen voor het maken, lezen, bijwerken en verwijderen van (CRUD).

    In dit voorbeeld wordt eenvoudige foutafhandeling uitgevoerd bij synchronisatieconflicten. Een echte toepassing zou omgaan met de verschillende fouten, zoals netwerkvoorwaarden, server conflicten, en anderen. Zie het [voorbeeld van offline synchronisatie]voor codevoorbeelden.

5. Voeg vervolgens deze functie toe om de werkelijke synchronisatie uit te voeren.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    U bepaalt zelf wanneer u wijzigingen in de back-end van de mobiele app wilt pushen door te bellen naar **syncContext.push()**. U bijvoorbeeld **syncBackend** oproepen in een gebeurtenishandler voor knoppen die is gekoppeld aan een synchronisatieknop.

## <a name="offline-sync-considerations"></a>Overwegingen voor offline synchronisatie

In het voorbeeld wordt de **pushmethode** van **syncContext** alleen aangeroepen bij het opstarten van apps in de callback-functie voor het inloggen.  In een echte toepassing u deze synchronisatiefunctionaliteit ook handmatig laten geactiveerd of wanneer de netwerkstatus verandert.

Wanneer een pull wordt uitgevoerd tegen een tabel die in afwachting van lokale updates is bijgehouden door de context, activeert die pull-bewerking automatisch een push. Wanneer u items in dit voorbeeld vernieuwt, toevoegt en voltooit, u de expliciete **pushcall** weglaten, omdat deze mogelijk overbodig is.

In de meegeleverde code worden alle records in de externe todoItem-tabel opgevraagd, maar het is ook mogelijk om records te filteren door een query-id en query door te **geven**. Zie de sectie *Incrementele synchronisatie* in [Offline gegevenssynchronisatie in Azure Mobile Apps]voor meer informatie.

## <a name="optional-disable-authentication"></a>(Optioneel) Verificatie uitschakelen

Als u geen verificatie wilt instellen voordat u offline synchronisatie test, geeft u commentaar op de terugbelfunctie voor aanmelding, maar laat u de code in de terugbelfunctie onbecommentarieerd.  Na het becommentariës van de inlogregels volgt de code:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nu synchroniseert de app met de Azure-backend wanneer u de app uitvoert.

## <a name="run-the-client-app"></a>De client-app uitvoeren
Als offline synchronisatie nu is ingeschakeld, u de clienttoepassing ten minste één keer op elk platform uitvoeren om de lokale winkeldatabase te vullen. Simuleer later een offline scenario en wijzigt u de gegevens in de lokale winkel terwijl de app offline is.

## <a name="optional-test-the-sync-behavior"></a>(Optioneel) Het synchronisatiegedrag testen
In deze sectie wijzigt u het clientproject om een offlinescenario te simuleren met behulp van een ongeldige toepassings-URL voor uw back-end. Wanneer u gegevensitems toevoegt of wijzigt, worden deze wijzigingen in het lokale archief bewaard, maar worden ze niet gesynchroniseerd met het backend-gegevensarchief totdat de verbinding is hersteld.

1. Open in de Oplossingsverkenner het projectbestand index.js en wijzig de URL van de toepassing om een ongeldige URL aan te wijzen, zoals de volgende code:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Werk in index.html het `<meta>` CSP-element bij met dezelfde ongeldige URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Bouw en voer de client-app uit en merk op dat er een uitzondering is aangemeld in de console wanneer de app probeert te synchroniseren met de backend na het inloggen. Alle nieuwe items die u toevoegt, bestaan alleen in de lokale winkel totdat ze naar de mobiele backend worden geduwd. De client-app gedraagt zich alsof deze is verbonden met de backend.

4. Sluit de app en start deze opnieuw op om te controleren of de nieuwe items die u hebt gemaakt, blijven bestaan in de lokale winkel.

5. (Optioneel) Gebruik Visual Studio om de tabel Azure SQL Database weer te geven om te zien dat de gegevens in de backenddatabase niet zijn gewijzigd.

    Open **Server Explorer**in Visual Studio. Navigeer naar uw database in **Azure**->**SQL-databases.** Klik met de rechtermuisknop op uw database en selecteer **Openen in SQL Server Object Explorer**. Nu u bladeren naar uw SQL-databasetabel en de inhoud ervan.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Optioneel) Test de heraansluiting met uw mobiele backend

In deze sectie sluit u de app opnieuw aan op de mobiele backend, die de app simuleert die terugkomt naar een online status. Wanneer u zich aanmeldt, worden gegevens gesynchroniseerd met uw mobiele backend.

1. Open index.js opnieuw en herstel de URL van de toepassing.
2. Open index.html opnieuw en corrigeer de `<meta>` toepassings-URL in het CSP-element.
3. De client-app opnieuw opbouwen en uitvoeren. De app probeert te synchroniseren met de mobiele app back-end na het inloggen. Controleer of er geen uitzonderingen zijn aangemeld bij de foutopsporingsconsole.
4. (Optioneel) Bekijk de bijgewerkte gegevens met SQL Server Object Explorer of een REST-tool zoals Fiddler. Let op dat de gegevens zijn gesynchroniseerd tussen de backenddatabase en de lokale winkel.

    Let op: de gegevens zijn gesynchroniseerd tussen de database en de lokale winkel en bevat de items die u hebt toegevoegd terwijl de verbinding met uw app is verbroken.

## <a name="additional-resources"></a>Aanvullende bronnen
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [Visual Studio Tools voor Apache Cordova]

## <a name="next-steps"></a>Volgende stappen
* Meer geavanceerde offlinesynchronisatiefuncties bekijken, zoals conflictoplossing in het [voorbeeld van offline synchronisatie]
* Controleer de api-verwijzing voor offline synchronisatie in de [API-documentatie](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova snel aan de slag]: app-service-mobile-cordova-get-started.md
[voorbeeld van offlinesynchronisatie]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools voor Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
