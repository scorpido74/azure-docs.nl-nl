---
title: Offline synchronisatie inschakelen voor uw mobiele Azure-app (Xamarin Android)
description: Meer informatie over het gebruik van App Service mobiele app om offline gegevens in uw Xamarin Android-toepassing in de cache op te slaan en te synchroniseren
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a7c9672db0a7cc12e78846bf82c54e3ee383c66
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025113"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Xamarin. Android-app

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end en geïntegreerde services in de ontwikkeling van mobiele apps. Ontwikkel aars kunnen services **bouwen**, **testen** en **distribueren** om een continue integratie-en leverings pijplijn in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkel aars de status en het gebruik van hun app bewaken met behulp van de **analyse** -en **diagnose** Services en gebruikers benaderen met behulp van de **Push** service. Ontwikkel aars kunnen ook gebruikmaken van **auth** voor het verifiëren van hun gebruikers en **gegevens** service om app-gegevens in de Cloud op te slaan en te synchroniseren.
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan met App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht

In deze zelf studie wordt de functie voor offline synchronisatie van Azure Mobile Apps voor Xamarin. Android geïntroduceerd. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app--gegevens weer geven, toevoegen of wijzigen, zelfs wanneer er geen netwerk verbinding is. Wijzigingen worden opgeslagen in een lokale data base.
Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

In deze zelf studie werkt u het client project bij vanuit de zelf studie [een Xamarin Android-app maken] ter ondersteuning van de offline functies van Azure Mobile apps. Als u het gedownloade Quick Start Server-project niet gebruikt, moet u de pakketten voor gegevens toegangs uitbreidingen toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie over server extensie pakketten.

Zie het onderwerp [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over de functie voor offline synchronisatie.

## <a name="update-the-client-app-to-support-offline-features"></a>De client-app bijwerken voor de ondersteuning van offline functies

Met de offline functies van Azure Mobile App kunt u met een lokale data base werken wanneer u een offline scenario hebt. Als u deze functies in uw app wilt gebruiken, initialiseert u een [SyncContext] naar een lokale opslag. Ga vervolgens naar de tabel via de [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) -interface. SQLite wordt gebruikt als het lokale archief op het apparaat.

1. Open in Visual Studio de NuGet-pakket manager in het project dat u hebt voltooid in de zelf studie [Een Xamarin Android-app maken] .  Zoek en installeer het pakket **micro soft. Azure. Mobile. client. SQLiteStore** NuGet.
2. Open het ToDoActivity.cs-bestand en verwijder de opmerking over de definitie van de @no__t 0.
3. Druk in Visual Studio op de toets **F5** om de client-app opnieuw te bouwen en uit te voeren. De app werkt op dezelfde manier als voordat u offline synchronisatie inschakelde. De lokale data base is nu echter gevuld met gegevens die in een offline scenario kunnen worden gebruikt.

## <a name="update-sync"></a>De app bijwerken om de verbinding met de back-end te verbreken

In deze sectie verbreekt u de verbinding met de back-end van uw mobiele app om een offline situatie te simuleren. Wanneer u gegevens items toevoegt, krijgt u een uitzonderings-handler te zien dat de app zich in de offline modus bevindt. In deze status worden nieuwe items toegevoegd aan het lokale archief en gesynchroniseerd met de back-end van de mobiele app wanneer een push wordt uitgevoerd met een verbonden status.

1. Bewerk ToDoActivity.cs in het gedeelde project. Wijzig de **applicationURL** zodat deze verwijst naar een ongeldige URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    U kunt ook offline gedrag demonstreren door Wi-Fi-en cellulaire netwerken op het apparaat uit te scha kelen of door de vliegtuig modus te gebruiken.
2. Druk op **F5** om de app te bouwen en uit te voeren. U ziet dat de synchronisatie is mislukt bij het vernieuwen wanneer de app is gestart.
3. Voer nieuwe items in en Let op dat push mislukt met de status [CancelledByNetworkError] telkens wanneer u op **Opslaan**klikt. De nieuwe TODO-items bestaan echter pas in het lokale archief als ze kunnen worden gepusht naar de back-end van de mobiele app.  Als u in een productie-app deze uitzonde ringen onderdrukt, gedraagt de client-app zich alsof deze nog steeds is verbonden met de back-end van de mobiele app.
4. Sluit de app en start deze opnieuw om te controleren of de nieuwe items die u hebt gemaakt, zijn opgeslagen in het lokale archief.
5. Beschrijving Open **Server Explorer**in Visual Studio. Navigeer naar uw data base in **Azure**->**SQL-data bases**. Klik met de rechter muisknop op de data base en selecteer **openen in SQL Server-objectverkenner**. U kunt nu naar de SQL database-tabel en de inhoud bladeren. Controleer of de gegevens in de back-end-data base niet zijn gewijzigd.
6. Beschrijving Gebruik een REST-hulp programma, zoals Fiddler of Postman, om een query uit te geven op uw mobiele back-end, met behulp van een GET-query in de vorm `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>De app bijwerken om de back-end van uw mobiele app opnieuw te verbinden

In deze sectie verbindt u de app opnieuw met de back-end van de mobiele app. Wanneer u de toepassing voor het eerst uitvoert, roept de gebeurtenis-handler van @no__t 0 de `OnRefreshItemsSelected` aan. Deze methode roept `SyncAsync` op om uw lokale archief te synchroniseren met de back-end-data base.

1. Open ToDoActivity.cs in het gedeelde project en wijzig de wijziging van de eigenschap **applicationURL** .
2. Druk op de toets **F5** om de app opnieuw te bouwen en uit te voeren. De app synchroniseert uw lokale wijzigingen met de back-end van de mobiele app van Azure met push-en pull-bewerkingen wanneer de methode `OnRefreshItemsSelected` wordt uitgevoerd.
3. Beschrijving Bekijk de bijgewerkte gegevens met behulp van SQL Server-objectverkenner of een REST hulp programma, zoals Fiddler. U ziet dat de gegevens zijn gesynchroniseerd tussen de back-updatabase van de Azure Mobile App en het lokale archief.
4. Klik in de app op het selectie vakje naast een aantal items om ze in het lokale archief te volt ooien.

   `CheckItem` roept `SyncAsync` aan om elk voltooid item te synchroniseren met de back-end van de mobiele app. `SyncAsync` roept zowel push als pull aan. **Wanneer u een pull-bewerking uitvoert op een tabel die door de client is gewijzigd, wordt een push-bewerking altijd automatisch uitgevoerd**. Dit zorgt ervoor dat alle tabellen in het lokale archief samen met de relaties consistent blijven. Dit gedrag kan leiden tot een onverwachte push. Zie [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over dit gedrag.

## <a name="review-the-client-sync-code"></a>De client synchronisatie code controleren

Het Xamarin-client project dat u bij het volt ooien van de zelf studie hebt gedownload, bevat al [Een Xamarin Android-app maken] die ondersteuning biedt voor offline synchronisatie met een lokale sqlite-data base. Hier volgt een kort overzicht van wat er al in de zelfstudie code is opgenomen. Zie [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor een conceptueel overzicht van de functie.

* Voordat een tabel bewerking kan worden uitgevoerd, moet het lokale archief worden geïnitialiseerd. De lokale store-data base wordt geïnitialiseerd als `ToDoActivity.OnCreate()` wordt uitgevoerd `ToDoActivity.InitLocalStoreAsync()`. Met deze methode maakt u een lokale SQLite-data base met behulp van de `MobileServiceSQLiteStore`-klasse van de Azure Mobile Apps client-SDK.

    Met de methode `DefineTable` wordt een tabel in het lokale archief gemaakt die overeenkomt met de velden in het geleverde type, `ToDoItem` in dit geval. Het type moet niet alle kolommen bevatten die zich in de externe data base bevinden. Het is mogelijk om alleen een subset van kolommen op te slaan.

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
* Het `toDoTable`-lid van `ToDoActivity` is van het type `IMobileServiceSyncTable` in plaats van `IMobileServiceTable`. De IMobileServiceSyncTable stuurt alle bewerkingen voor maken, lezen, bijwerken en verwijderen (ruw) naar de lokale store-data base.

    U bepaalt wanneer wijzigingen worden gepusht naar de back-end van de mobiele app van Azure door `IMobileServiceSyncContext.PushAsync()` aan te roepen. De synchronisatie context helpt bij het bewaren van tabel relaties door wijzigingen in alle tabellen bij te houden en te pushen die een client-app heeft gewijzigd wanneer `PushAsync` is aangeroepen.

    De gegeven code roept `ToDoActivity.SyncAsync()` op om te synchroniseren wanneer de todoitem-lijst wordt vernieuwd of een todoitem wordt toegevoegd of voltooid. De code wordt gesynchroniseerd na elke lokale wijziging.

    In de gegeven code worden alle records in de tabel met externe `TodoItem` opgevraagd, maar het is ook mogelijk om records te filteren door een query-ID en een query uit te geven aan `PushAsync`. Zie de sectie *incrementele synchronisatie* in [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie.

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
* [PROCEDURE voor Azure Mobile Apps .NET SDK][8]

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
