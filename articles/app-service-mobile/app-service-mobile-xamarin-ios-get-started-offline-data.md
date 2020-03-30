---
title: Offline synchronisatie inschakelen (Xamarin iOS)
description: Meer informatie over het gebruik van App Service Mobile App om offline gegevens in uw Xamarin iOS-toepassing in de cache te bewaren en te synchroniseren.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3a5128f6918b22be2ff1ef6adf3e453b1f373ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461297"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
In deze zelfstudie wordt de offline synchronisatiefunctie van Azure Mobile Apps voor Xamarin.iOS geïntroduceerd. Met offline synchronisatie kunnen eindgebruikers communiceren met een mobiele app, gegevens weergeven, toevoegen of wijzigen, zelfs als er geen netwerkverbinding is. Wijzigingen worden opgeslagen in een lokale database. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

Werk in deze zelfstudie het xamarin.iOS-appproject bij [vanuit Een Xamarin iOS-app maken] om de offline functies van Azure Mobile Apps te ondersteunen. Als u het gedownloade project voor snelle startserver niet gebruikt, moet u de uitbreidingspakketten voor gegevenstoegang aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie over serverextensiepakketten.

Zie het onderwerp [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over de functie offline synchronisatie.

## <a name="update-the-client-app-to-support-offline-features"></a>De client-app bijwerken om offlinefuncties te ondersteunen
Met offlinefuncties van Azure Mobile App u communiceren met een lokale database wanneer u zich in een offlinescenario bevindt. Als u deze functies in uw app wilt gebruiken, initialiseert u een [SyncContext] naar een lokale winkel. Verwijs uw tabel door de interface [IMobileServiceSyncTable]. SQLite wordt gebruikt als de lokale winkel op het apparaat.

1. Open de NuGet-pakketbeheerder in het project dat u hebt voltooid in de zelfstudie [van de Xamarin iOS-app] maken en zoek en installeer vervolgens het **NuGet-pakket Microsoft.Azure.Mobile.Client.SQLiteStore.**
2. Open het QSTodoService.cs bestand `#define OFFLINE_SYNC_ENABLED` en maak geen commentaar op de definitie.
3. De client-app opnieuw opbouwen en uitvoeren. De app werkt hetzelfde als voordat u offline synchronisatie hebt ingeschakeld. De lokale database wordt nu echter gevuld met gegevens die kunnen worden gebruikt in een offline scenario.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>De app bijwerken om de verbinding met de backend te verbreken
In deze sectie verbreekt u de verbinding met de backend van uw mobiele app om een offline situatie te simuleren. Wanneer u gegevensitems toevoegt, vertelt uw uitzonderingshandler u dat de app in de offlinemodus staat. In deze status worden nieuwe items die in de lokale winkel zijn toegevoegd en worden ze gesynchroniseerd met de backend van de mobiele app wanneer push de volgende keer wordt uitgevoerd in een verbonden status.

1. Bewerk QSToDoService.cs in het gedeelde project. Wijzig de **toepassings-URL** om een ongeldige URL aan te wijzen:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    U ook offline gedrag aantonen door wifi en mobiele netwerken op het apparaat uit te schakelen of de vliegtuigmodus te gebruiken.
2. Bouw de app en voer deze uit. Merk op dat de synchronisatie is mislukt bij het vernieuwen toen de app werd gestart.
3. Voer nieuwe items in en merk op dat push mislukt met een status [CancelledByNetworkError] telkens wanneer u op **Opslaan**klikt. De nieuwe todo-items bestaan echter in de lokale winkel totdat ze naar de backend van de mobiele app kunnen worden gepusht.  Als u deze uitzonderingen onderdrukt, gedraagt de clientapp zich in een productie-app alsof deze nog steeds is verbonden met de back-end van de mobiele app.
4. Sluit de app en start deze opnieuw op om te controleren of de nieuwe items die u hebt gemaakt, blijven bestaan in de lokale winkel.
5. (Optioneel) Als U Visual Studio op een pc hebt geïnstalleerd, opent u **Server Explorer**. Navigeer naar uw database in **Azure**-> **SQL-databases.** Klik met de rechtermuisknop op uw database en selecteer **Openen in SQL Server Object Explorer**. Nu u bladeren naar uw SQL-databasetabel en de inhoud ervan. Controleer of de gegevens in de backenddatabase niet zijn gewijzigd.
6. (Optioneel) Gebruik een REST-tool zoals Fiddler of Postman om uw mobiele backend op te vragen met behulp van een GET-query in het formulier `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>De app bijwerken om de back-end van uw mobiele app opnieuw te verbinden
Sluit in deze sectie de app opnieuw aan op de backend van de mobiele app. Dit simuleert de app die van een offline status naar een online status gaat met de backend van de mobiele app.   Als u de netwerkbreuk hebt gesimuleerd door de netwerkverbinding uit te schakelen, zijn er geen codewijzigingen nodig.
Schakel het netwerk weer in.  Wanneer u de toepassing `RefreshDataAsync` voor het eerst uitvoert, wordt de methode aangeroepen. Dit op `SyncAsync` zijn beurt oproepen om uw lokale winkel te synchroniseren met de backend database.

1. Open QSToDoService.cs in het gedeelde project en draai de eigenschap **applicationURL** terug.
2. De app opnieuw opbouwen en uitvoeren. De app synchroniseert uw lokale wijzigingen met de back-end `OnRefreshItemsSelected` van azure mobile app met push- en pull-bewerkingen wanneer de methode wordt uitgevoerd.
3. (Optioneel) Bekijk de bijgewerkte gegevens met SQL Server Object Explorer of een REST-tool zoals Fiddler. De gegevens zijn gesynchroniseerd tussen de backend-database van Azure Mobile App en de lokale winkel.
4. Klik in de app op het selectievakje naast een paar items om ze in de lokale winkel te voltooien.

   `CompleteItemAsync`oproepen `SyncAsync` om elk voltooid item te synchroniseren met de back-end van de mobiele app. `SyncAsync`gesprekken zowel duwen als trekken.
   **Wanneer u een pull uitvoert tegen een tabel waarin de client wijzigingen heeft aangebracht, wordt een push op de clientsynchronisatiecontext altijd eerst automatisch uitgevoerd.** De impliciete push zorgt ervoor dat alle tabellen in de lokale winkel, samen met relaties consistent blijven. Zie [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over dit gedrag.

## <a name="review-the-client-sync-code"></a>De synchronisatiecode van de client controleren
Het Xamarin-clientproject dat u hebt gedownload toen u de zelfstudie hebt voltooid [Maak een Xamarin iOS-app] bevat al code die offlinesynchronisatie ondersteunt met behulp van een lokale SQLite-database. Hier is een kort overzicht van wat al is opgenomen in de tutorial code. Zie [Offline gegevenssynchroniseren in Azure Mobile Apps]voor een conceptueel overzicht van de functie.

* Voordat tabelbewerkingen kunnen worden uitgevoerd, moet de lokale winkel worden geïnitialiseerd. De lokale winkeldatabase wordt `QSTodoListViewController.ViewDidLoad()` geïnitialiseerd wanneer deze wordt `QSTodoService.InitializeStoreAsync()`uitgevoerd. Met deze methode wordt een nieuwe `MobileServiceSQLiteStore` lokale SQLite-database gemaakt met behulp van de klasse die wordt geleverd door de Azure Mobile App-client SDK.

    Met `DefineTable` de methode wordt `ToDoItem` in dit geval een tabel gemaakt in de lokale winkel die overeenkomt met de velden in het opgegeven type. Het type hoeft niet alle kolommen in de externe database op te nemen. Het is mogelijk om slechts een subset van kolommen op te slaan.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Het `todoTable` lid `QSTodoService` van `IMobileServiceSyncTable` is van `IMobileServiceTable`het type in plaats van . De IMobileServiceSyncTable stuurt alle bewerkingen van de tabel voor maken, lezen, bijwerken en verwijderen naar de lokale winkeldatabase.

    U bepaalt zelf wanneer deze wijzigingen worden doorgevoerd `IMobileServiceSyncContext.PushAsync()`naar de back-end van de Azure Mobile App door te bellen. De synchronisatiecontext helpt tabelrelaties te behouden door wijzigingen bij te `PushAsync` houden en te pushen in alle tabellen die een client-app heeft gewijzigd wanneer deze wordt aangeroepen.

    De meegeleverde `QSTodoService.SyncAsync()` code oproepen om te synchroniseren wanneer de todoitem lijst wordt vernieuwd of een todoitem wordt toegevoegd of voltooid. De app synchroniseert na elke lokale wijziging. Als een pull wordt uitgevoerd tegen een tabel die in behandeling is met lokale updates die door de context worden bijgehouden, wordt automatisch een contextpush geactiveerd.

    In de meegeleverde code worden `TodoItem` alle records in de externe tabel opgevraagd, maar het `PushAsync`is ook mogelijk om records te filteren door een query-id en query door te geven aan . Zie de sectie *Incrementele synchronisatie* in [Offline gegevenssynchronisatie in Azure Mobile Apps]voor meer informatie.

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Aanvullende resources
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Een Xamarin iOS-app maken]: app-service-mobile-xamarin-ios-get-started.md
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
