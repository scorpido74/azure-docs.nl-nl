---
title: Offline synchronisatie inschakelen (Xamarin iOS)
description: Meer informatie over het gebruik van App Service mobiele app om offline gegevens in de iOS-toepassing in uw Xamarin op te slaan en te synchroniseren.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 615f8f028182178928d3d755c102daceef402ef4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668254"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Offlinesynchronisatie inschakelen voor uw mobiele Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht
In deze zelf studie wordt de functie voor offline synchronisatie van Azure Mobile Apps voor Xamarin. iOS geïntroduceerd. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app--gegevens weer geven, toevoegen of wijzigen, zelfs wanneer er geen netwerk verbinding is. Wijzigingen worden opgeslagen in een lokale data base. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

In deze zelf studie werkt u het Xamarin. iOS-app-project bij vanuit [een Xamarin IOS-app maken] ter ondersteuning van de offline functies van Azure Mobile apps. Als u het gedownloade Quick Start Server-project niet gebruikt, moet u de pakketten voor gegevens toegangs uitbreidingen toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie over server extensie pakketten.

Zie het onderwerp [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over de functie voor offline synchronisatie.

## <a name="update-the-client-app-to-support-offline-features"></a>De client-app bijwerken voor de ondersteuning van offline functies
Met de offline functies van Azure Mobile App kunt u met een lokale data base werken wanneer u een offline scenario hebt. Als u deze functies in uw app wilt gebruiken, initialiseert u een [SyncContext] naar een lokale opslag. Verwijs naar uw tabel via de interface [IMobileServiceSyncTable]. SQLite wordt gebruikt als het lokale archief op het apparaat.

1. Open NuGet package manager in het project dat u in de zelf studie [een Xamarin IOS-app maken] hebt uitgevoerd, zoek naar en installeer het pakket **micro soft. Azure. Mobile. client. SQLiteStore** NuGet.
2. Open het QSTodoService.cs-bestand en verwijder de opmerking over de `#define OFFLINE_SYNC_ENABLED` definitie.
3. Bouw en voer de client-app opnieuw uit. De app werkt op dezelfde manier als voordat u offline synchronisatie inschakelde. De lokale data base is nu echter gevuld met gegevens die in een offline scenario kunnen worden gebruikt.

## <a name="update-sync"></a>De app bijwerken om de verbinding met de back-end te verbreken
In deze sectie verbreekt u de verbinding met de back-end van uw mobiele app om een offline situatie te simuleren. Wanneer u gegevens items toevoegt, krijgt u een uitzonderings-handler te zien dat de app zich in de offline modus bevindt. In deze status worden nieuwe items toegevoegd aan het lokale archief en worden ze gesynchroniseerd met de back-end van de mobiele app wanneer push de volgende keer wordt uitgevoerd in een verbonden status.

1. Bewerk QSToDoService.cs in het gedeelde project. Wijzig de **applicationURL** zodat deze verwijst naar een ongeldige URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    U kunt ook offline gedrag demonstreren door Wi-Fi-en cellulaire netwerken op het apparaat uit te scha kelen of door de vliegtuig modus te gebruiken.
2. Bouw de app en voer deze uit. U ziet dat de synchronisatie is mislukt bij het vernieuwen wanneer de app is gestart.
3. Voer nieuwe items in en Let op dat push mislukt met de status [CancelledByNetworkError] telkens wanneer u op **Opslaan**klikt. De nieuwe TODO-items bestaan echter pas in het lokale archief als ze kunnen worden gepusht naar de back-end van de mobiele app.  Als u in een productie-app deze uitzonde ringen onderdrukt, gedraagt de client-app zich alsof deze nog steeds is verbonden met de back-end van de mobiele app.
4. Sluit de app en start deze opnieuw om te controleren of de nieuwe items die u hebt gemaakt, zijn opgeslagen in het lokale archief.
5. Beschrijving Als Visual Studio op een PC is geïnstalleerd, opent u **Server Explorer**. Navigeer naar uw data base in **Azure**-> **SQL-data bases**. Klik met de rechter muisknop op de data base en selecteer **openen in SQL Server-objectverkenner**. U kunt nu naar de SQL database-tabel en de inhoud bladeren. Controleer of de gegevens in de back-end-data base niet zijn gewijzigd.
6. Beschrijving Gebruik een REST-hulp programma, zoals Fiddler of Postman, om een query op uw mobiele back-end uit te geven met behulp van een GET-query in de vorm `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>De app bijwerken om de back-end van uw mobiele app opnieuw te verbinden
In deze sectie verbindt u de app opnieuw met de back-end van de mobiele app. Hiermee wordt de app gesimuleerd van een offline status naar een online status met de back-end van de mobiele app.   Als u de netwerk storing hebt gesimuleerd door de netwerk verbinding uit te scha kelen, zijn er geen code wijzigingen nodig.
Het netwerk opnieuw in te scha kelen.  Wanneer u de toepassing voor het eerst uitvoert, wordt de `RefreshDataAsync` methode aangeroepen. Op deze beurt roepen `SyncAsync` om uw lokale archief te synchroniseren met de back-end-data base.

1. Open QSToDoService.cs in het gedeelde project en wijzig de wijziging van de eigenschap **applicationURL** .
2. Maak de app opnieuw en voer deze uit. De app synchroniseert uw lokale wijzigingen met de back-end van de mobiele app van Azure met push-en pull-bewerkingen wanneer de `OnRefreshItemsSelected` methode wordt uitgevoerd.
3. Beschrijving Bekijk de bijgewerkte gegevens met behulp van SQL Server-objectverkenner of een REST hulp programma, zoals Fiddler. U ziet dat de gegevens zijn gesynchroniseerd tussen de back-updatabase van de Azure Mobile App en het lokale archief.
4. Klik in de app op het selectie vakje naast een aantal items om ze in het lokale archief te volt ooien.

   `CompleteItemAsync` roept `SyncAsync` op om elk voltooid item te synchroniseren met de back-end van de mobiele app. `SyncAsync` push-en pull-aanroepen.
   **Wanneer u een pull-bewerking uitvoert op een tabel die door de client is gewijzigd, wordt een push-bewerking op de client synchronisatie context altijd automatisch uitgevoerd**. De impliciete push zorgt ervoor dat alle tabellen in het lokale archief samen met de relaties consistent blijven. Zie [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over dit gedrag.

## <a name="review-the-client-sync-code"></a>De client synchronisatie code controleren
Het Xamarin-client project dat u hebt gedownload tijdens het volt ooien van de zelf studie [een Xamarin IOS-app maken] bevat al code die ondersteuning biedt voor offline synchronisatie met een lokale sqlite-data base. Hier volgt een kort overzicht van wat er al in de zelfstudie code is opgenomen. Zie [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor een conceptueel overzicht van de functie.

* Voordat een tabel bewerking kan worden uitgevoerd, moet het lokale archief worden geïnitialiseerd. De lokale store-data base wordt geïnitialiseerd wanneer `QSTodoListViewController.ViewDidLoad()` `QSTodoService.InitializeStoreAsync()`uitvoert. Met deze methode maakt u een nieuwe lokale SQLite-data base met behulp van de `MobileServiceSQLiteStore`-klasse van de Azure Mobile App client SDK.

    Met de methode `DefineTable` maakt u een tabel in het lokale archief die overeenkomt met de velden in het geleverde type `ToDoItem` in dit geval. Het type moet niet alle kolommen bevatten die zich in de externe data base bevinden. Het is mogelijk om alleen een subset van kolommen op te slaan.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Het `todoTable` lid van `QSTodoService` is van het `IMobileServiceSyncTable` type in plaats van `IMobileServiceTable`. De IMobileServiceSyncTable stuurt alle bewerkingen voor maken, lezen, bijwerken en verwijderen (ruw) naar de lokale store-data base.

    U bepaalt wanneer deze wijzigingen worden gepusht naar de back-end van de mobiele app van Azure door `IMobileServiceSyncContext.PushAsync()`aan te roepen. De synchronisatie context helpt bij het bewaren van tabel relaties door wijzigingen in alle tabellen bij te houden en te pushen die een client-app heeft gewijzigd wanneer `PushAsync` wordt aangeroepen.

    De gegeven code roept `QSTodoService.SyncAsync()` op om te synchroniseren wanneer de todoitem-lijst wordt vernieuwd of een todoitem wordt toegevoegd of voltooid. De app wordt gesynchroniseerd na elke lokale wijziging. Als er een pull-bewerking wordt uitgevoerd voor een tabel die in behandeling zijnde lokale updates die worden bijgehouden door de context, wordt door deze pull-actie automatisch een context push geactiveerd.

    In de gegeven code worden alle records in de tabel met externe `TodoItem` opgevraagd, maar het is ook mogelijk om records te filteren door een query-ID en-query door te geven aan `PushAsync`. Zie de sectie *incrementele synchronisatie* in [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie.

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

## <a name="additional-resources"></a>Aanvullende bronnen
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [PROCEDURE voor Azure Mobile Apps .NET SDK][8]

<!-- Images -->

<!-- URLs. -->
[Een Xamarin iOS-app maken]: app-service-mobile-xamarin-ios-get-started.md
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
