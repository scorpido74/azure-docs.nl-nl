---
title: Offline synchronisatie inschakelen (Xamarin Android)
description: Meer informatie over het gebruik van App Service Mobile App om offline gegevens in uw Android-toepassing van Xamarin in de cache te plaatsen en te synchroniseren.
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4faab572cd77e20bb61d147ca6e58b25cd88ad9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458865"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Xamarin.Android-app

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht

Deze zelfstudie introduceert de offline synchronisatiefunctie van Azure Mobile Apps voor Xamarin.Android. Met offline synchronisatie kunnen eindgebruikers communiceren met een mobiele app, gegevens weergeven, toevoegen of wijzigen, zelfs als er geen netwerkverbinding is. Wijzigingen worden opgeslagen in een lokale database.
Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

In deze zelfstudie werkt u het clientproject bij vanuit de zelfstudie [Maak een Android-app voor Xamarin] om de offline functies van Azure Mobile Apps te ondersteunen. Als u het gedownloade project voor snelle startserver niet gebruikt, moet u de uitbreidingspakketten voor gegevenstoegang aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie over serverextensiepakketten.

Zie het onderwerp [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over de functie offline synchronisatie.

## <a name="update-the-client-app-to-support-offline-features"></a>De client-app bijwerken om offlinefuncties te ondersteunen

Met offlinefuncties van Azure Mobile App u communiceren met een lokale database wanneer u zich in een offlinescenario bevindt. Als u deze functies in uw app wilt gebruiken, initialiseert u een [SyncContext] naar een lokale winkel. Verwijs vervolgens naar uw tabel via de [IMobileServiceSyncTable-interface.](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) SQLite wordt gebruikt als de lokale winkel op het apparaat.

1. Open in Visual Studio de NuGet-pakketmanager in het project dat u hebt voltooid in de zelfstudie [van de Xamarin Android-app maken.]  Zoek naar en installeer het **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-pakket.
2. Open het ToDoActivity.cs bestand `#define OFFLINE_SYNC_ENABLED` en maak geen commentaar op de definitie.
3. Druk in Visual Studio op de **F5-toets** om de client-app opnieuw op te bouwen en uit te voeren. De app werkt hetzelfde als voordat u offline synchronisatie hebt ingeschakeld. De lokale database wordt nu echter gevuld met gegevens die kunnen worden gebruikt in een offline scenario.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>De app bijwerken om de verbinding met de backend te verbreken

In deze sectie verbreekt u de verbinding met de backend van uw mobiele app om een offline situatie te simuleren. Wanneer u gegevensitems toevoegt, vertelt uw uitzonderingshandler u dat de app in de offlinemodus staat. In deze status worden nieuwe items toegevoegd in de lokale winkel en worden ze gesynchroniseerd met de backend van de mobiele app wanneer een push wordt uitgevoerd in een verbonden status.

1. Bewerk ToDoActivity.cs in het gedeelde project. Wijzig de **toepassings-URL** om een ongeldige URL aan te wijzen:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    U ook offline gedrag aantonen door wifi en mobiele netwerken op het apparaat uit te schakelen of de vliegtuigmodus te gebruiken.
2. Druk op **F5** om de app te bouwen en uit te voeren. Merk op dat de synchronisatie is mislukt bij het vernieuwen toen de app werd gestart.
3. Voer nieuwe items in en merk op dat push mislukt met een status [CancelledByNetworkError] telkens wanneer u op **Opslaan**klikt. De nieuwe todo-items bestaan echter in de lokale winkel totdat ze naar de backend van de mobiele app kunnen worden gepusht.  Als u deze uitzonderingen onderdrukt, gedraagt de clientapp zich in een productie-app alsof deze nog steeds is verbonden met de back-end van de mobiele app.
4. Sluit de app en start deze opnieuw op om te controleren of de nieuwe items die u hebt gemaakt, blijven bestaan in de lokale winkel.
5. (Optioneel) Open **Server Explorer**in Visual Studio. Navigeer naar uw database in **Azure**->**SQL-databases.** Klik met de rechtermuisknop op uw database en selecteer **Openen in SQL Server Object Explorer**. Nu u bladeren naar uw SQL-databasetabel en de inhoud ervan. Controleer of de gegevens in de backenddatabase niet zijn gewijzigd.
6. (Optioneel) Gebruik een REST-tool zoals Fiddler of Postman om uw mobiele backend op te vragen met behulp van een GET-query in het formulier `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>De app bijwerken om de back-end van uw mobiele app opnieuw te verbinden

Sluit in deze sectie de app opnieuw aan op de backend van de mobiele app. Wanneer u de toepassing `OnCreate` voor het `OnRefreshItemsSelected`eerst uitvoert, roept de gebeurtenishandler . Met deze `SyncAsync` methode wordt opgeroepen om uw lokale winkel te synchroniseren met de backenddatabase.

1. Open ToDoActivity.cs in het gedeelde project en draai de eigenschap **applicationURL** terug.
2. Druk op de **F5-toets** om de app opnieuw op te bouwen en uit te voeren. De app synchroniseert uw lokale wijzigingen met de back-end `OnRefreshItemsSelected` van azure mobile app met push- en pull-bewerkingen wanneer de methode wordt uitgevoerd.
3. (Optioneel) Bekijk de bijgewerkte gegevens met SQL Server Object Explorer of een REST-tool zoals Fiddler. De gegevens zijn gesynchroniseerd tussen de backend-database van Azure Mobile App en de lokale winkel.
4. Klik in de app op het selectievakje naast een paar items om ze in de lokale winkel te voltooien.

   `CheckItem`oproepen `SyncAsync` om elk voltooid item te synchroniseren met de back-end van de mobiele app. `SyncAsync`gesprekken zowel duwen als trekken. **Wanneer u een pull uitvoert tegen een tabel waarin de client wijzigingen heeft aangebracht, wordt een push altijd automatisch uitgevoerd.** Dit zorgt ervoor dat alle tabellen in de lokale winkel, samen met relaties consistent blijven. Dit gedrag kan resulteren in een onverwachte push. Zie [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over dit gedrag.

## <a name="review-the-client-sync-code"></a>De synchronisatiecode van de client controleren

Het Xamarin-clientproject dat u hebt gedownload toen u de zelfstudie hebt voltooid [Maak een Android-app van Xamarin] bevat al code die offlinesynchronisatie ondersteunt met behulp van een lokale SQLite-database. Hier is een kort overzicht van wat al is opgenomen in de tutorial code. Zie [Offline gegevenssynchroniseren in Azure Mobile Apps]voor een conceptueel overzicht van de functie.

* Voordat tabelbewerkingen kunnen worden uitgevoerd, moet de lokale winkel worden geïnitialiseerd. De lokale winkeldatabase wordt `ToDoActivity.OnCreate()` geïnitialiseerd wanneer deze wordt `ToDoActivity.InitLocalStoreAsync()`uitgevoerd. Met deze methode wordt een `MobileServiceSQLiteStore` lokale SQLite-database gemaakt met behulp van de klasse die wordt geleverd door de Azure Mobile Apps-client SDK.

    Met `DefineTable` de methode wordt `ToDoItem` in dit geval een tabel gemaakt in de lokale winkel die overeenkomt met de velden in het opgegeven type. Het type hoeft niet alle kolommen in de externe database op te nemen. Het is mogelijk om slechts een subset van kolommen op te slaan.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* Het `toDoTable` lid `ToDoActivity` van `IMobileServiceSyncTable` is van `IMobileServiceTable`het type in plaats van . De IMobileServiceSyncTable stuurt alle bewerkingen van de tabel voor maken, lezen, bijwerken en verwijderen naar de lokale winkeldatabase.

    U bepaalt zelf wanneer wijzigingen worden doorgevoerd in `IMobileServiceSyncContext.PushAsync()`de back-end van de Azure Mobile App door te bellen. De synchronisatiecontext helpt tabelrelaties te behouden door wijzigingen bij te `PushAsync` houden en te pushen in alle tabellen die een client-app heeft gewijzigd wanneer deze wordt aangeroepen.

    De meegeleverde `ToDoActivity.SyncAsync()` code oproepen om te synchroniseren wanneer de todoitem lijst wordt vernieuwd of een todoitem wordt toegevoegd of voltooid. De code wordt gesynchroniseerd na elke lokale wijziging.

    In de meegeleverde code worden `TodoItem` alle records in de externe tabel opgevraagd, maar het `PushAsync`is ook mogelijk om records te filteren door een query-id en query door te geven aan . Zie de sectie *Incrementele synchronisatie* in [Offline gegevenssynchronisatie in Azure Mobile Apps]voor meer informatie.

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Aanvullende resources

* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[Een Xamarin Android-app maken]: ./app-service-mobile-xamarin-android-get-started.md
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Een Xamarin Android-app maken]: app-service-mobile-xamarin-android-get-started.md
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
