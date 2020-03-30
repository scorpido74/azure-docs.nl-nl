---
title: Offlinesynchronisatie inschakelen voor uw UWP-app
description: Meer informatie over het gebruik van een Azure Mobile App om offline gegevens in uw UWP-app (Universal Windows Platform) in de cache te bewaren en te synchroniseren.
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 236d4c199a13b02bcd82ae02657bbd35e45f729b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458814"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Offlinesynchronisatie voor uw Windows-app inschakelen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
In deze zelfstudie ziet u hoe u offline ondersteuning toevoegt aan een UWP-app (Universal Windows Platform) met behulp van een back-end van Azure Mobile App. Met offline synchronisatie kunnen eindgebruikers communiceren met een mobiele app, gegevens weergeven, toevoegen of wijzigen, zelfs als er geen netwerkverbinding is. Wijzigingen worden opgeslagen in een lokale database. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe backend.

In deze zelfstudie werkt u het PROJECT UWP-app bij vanuit de zelfstudie [Een Windows-app maken] om de offline functies van Azure Mobile Apps te ondersteunen. Als u het gedownloade project voor snelle startserver niet gebruikt, moet u de uitbreidingspakketten voor gegevenstoegang aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie over serverextensiepakketten.

Zie het onderwerp [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over de functie offline synchronisatie.

## <a name="requirements"></a>Vereisten  
Deze zelfstudie vereist de volgende vereisten:

* Visual Studio 2013 draait op Windows 8.1 of hoger.
* Voltooiing van [Een Windows-app maken][maak een Windows-app].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite voor universele ontwikkeling van Windows-platform](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>De client-app bijwerken om offlinefuncties te ondersteunen
Met offlinefuncties van Azure Mobile App u communiceren met een lokale database wanneer u zich in een offlinescenario bevindt. Als u deze functies in uw app wilt gebruiken, initialiseert u een [SyncContext-interface.][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] SQLite wordt gebruikt als de lokale winkel op het apparaat.

1. Installeer de [SQLite-runtime voor het Universele Windows-platform.](https://sqlite.org/2016/sqlite-uwp-3120200.vsix)
2. Open in Visual Studio de NuGet-pakketbeheer voor het PROJECT UWP-app dat u hebt voltooid in de zelfstudie [van Een Windows-app] maken.
    Zoek naar en installeer het **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-pakket.
3. Klik in Solution Explorer met de rechtermuisknop **op Referenties** > **Toevoegen aan referentie...** > Universele **Windows-extensies** **Universal Windows** > en schakel vervolgens zowel **SQLite voor Universal Windows Platform** als Visual **C++ 2015 Runtime in voor Universele Windows Platform-apps**.

    ![SQLite UWP-verwijzing toevoegen][1]
4. Open het MainPage.xaml.cs bestand `#define OFFLINE_SYNC_ENABLED` en maak geen commentaar op de definitie.
5. Druk in Visual Studio op de **F5-toets** om de client-app opnieuw op te bouwen en uit te voeren. De app werkt hetzelfde als voordat u offline synchronisatie hebt ingeschakeld. De lokale database wordt nu echter gevuld met gegevens die kunnen worden gebruikt in een offline scenario.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>De app bijwerken om de verbinding met de backend te verbreken
In deze sectie verbreekt u de verbinding met de backend van uw mobiele app om een offline situatie te simuleren. Wanneer u gegevensitems toevoegt, vertelt uw uitzonderingshandler u dat de app in de offlinemodus staat. In deze status worden nieuwe items die in de lokale winkel zijn toegevoegd en worden ze gesynchroniseerd met de backend van de mobiele app wanneer push de volgende keer wordt uitgevoerd in een verbonden status.

1. Bewerk App.xaml.cs in het gedeelde project. Geef commentaar op de initialisatie van de **MobileServiceClient** en voeg de volgende regel toe, waarin een ongeldige URL van de mobiele app wordt gebruikt:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    U ook offline gedrag aantonen door wifi en mobiele netwerken op het apparaat uit te schakelen of de vliegtuigmodus te gebruiken.
2. Druk op **F5** om de app te bouwen en uit te voeren. Merk op dat de synchronisatie is mislukt bij het vernieuwen toen de app werd gestart.
3. Voer nieuwe items in en merk op dat push mislukt met een [Status CancelledByNetworkError] telkens wanneer u op **Opslaan**klikt. De nieuwe todo-items bestaan echter in de lokale winkel totdat ze naar de backend van de mobiele app kunnen worden gepusht.  Als u deze uitzonderingen onderdrukt, gedraagt de clientapp zich in een productie-app alsof deze nog steeds is verbonden met de back-end van de mobiele app.
4. Sluit de app en start deze opnieuw op om te controleren of de nieuwe items die u hebt gemaakt, blijven bestaan in de lokale winkel.
5. (Optioneel) Open **Server Explorer**in Visual Studio. Navigeer naar uw database in **Azure**->**SQL-databases.** Klik met de rechtermuisknop op uw database en selecteer **Openen in SQL Server Object Explorer**. Nu u bladeren naar uw SQL-databasetabel en de inhoud ervan. Controleer of de gegevens in de backenddatabase niet zijn gewijzigd.
6. (Optioneel) Gebruik een REST-tool zoals Fiddler of Postman om uw mobiele backend op te vragen met behulp van een GET-query in het formulier `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>De app bijwerken om de back-end van uw mobiele app opnieuw te verbinden
In deze sectie sluit u de app opnieuw aan op de backend van de mobiele app. Deze wijzigingen simuleren een netwerkherverbinding in de app.

Wanneer u de toepassing `OnNavigatedTo` voor het `InitLocalStoreAsync`eerst uitvoert, roept de gebeurtenishandler . Deze methode roept `SyncAsync` op zijn beurt om uw lokale winkel te synchroniseren met de backend-database. De app probeert te synchroniseren bij het opstarten.

1. Open App.xaml.cs in het gedeelde project en maak `MobileServiceClient` geen commentaar op uw vorige initialisatie om de juiste URL van de mobiele app te gebruiken.
2. Druk op de **F5-toets** om de app opnieuw op te bouwen en uit te voeren. De app synchroniseert uw lokale wijzigingen met de back-end `OnNavigatedTo` van azure mobile app met push- en pull-bewerkingen wanneer de gebeurtenishandler wordt uitgevoerd.
3. (Optioneel) Bekijk de bijgewerkte gegevens met SQL Server Object Explorer of een REST-tool zoals Fiddler. De gegevens zijn gesynchroniseerd tussen de backend-database van Azure Mobile App en de lokale winkel.
4. Klik in de app op het selectievakje naast een paar items om ze in de lokale winkel te voltooien.

   `UpdateCheckedTodoItem`oproepen `SyncAsync` om elk voltooid item te synchroniseren met de back-end van de mobiele app. `SyncAsync`gesprekken zowel duwen als trekken. Wanneer u echter **een pull uitvoert tegen een tabel waarin de client wijzigingen heeft aangebracht, wordt een push altijd automatisch uitgevoerd.** Dit gedrag zorgt ervoor dat alle tabellen in de lokale winkel, samen met relaties consistent blijven. Dit gedrag kan resulteren in een onverwachte push.  Zie [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over dit gedrag.

## <a name="api-summary"></a>API-overzicht
Om de offline functies van mobiele services te ondersteunen, gebruikten we de [IMobileServiceSyncTable-interface] en de geïnitialiseerde [MobileServiceClient.SyncContext][synccontext] met een lokale SQLite-database. Wanneer u offline bent, werken de normale CRUD-bewerkingen voor mobiele apps alsof de app nog steeds is verbonden terwijl de bewerkingen plaatsvinden tegen de lokale winkel. De volgende methoden worden gebruikt om de lokale winkel met de server te synchroniseren:

* **[PushAsync PushAsync]** Omdat deze methode lid is van [IMobileServicesSyncContext,]worden wijzigingen in alle tabellen naar de backend gepusht. Alleen records met lokale wijzigingen worden naar de server verzonden.
* **[PullAsync]** Een pull is gestart vanuit een [IMobileServiceSyncTable]. Wanneer er bijgehouden wijzigingen in de tabel zijn, wordt een impliciete push uitgevoerd om ervoor te zorgen dat alle tabellen in de lokale winkel samen met relaties consistent blijven. De parameter *pushOtherTables* bepaalt of andere tabellen in de context in een impliciete push worden gepusht. De *queryparameter* heeft een [IMobileServiceTableQuery\<T->][IMobileServiceTableQuery] of OData-queryreeks nodig om de geretourneerde gegevens te filteren. De parameter *queryId* wordt gebruikt om incrementele synchronisatie te definiëren. Zie [Offline gegevenssynchroniseren in Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works)voor meer informatie.
* **[PurgeAsync (PurgeAsync)]** Uw app moet deze methode periodiek aanroepen om verouderde gegevens uit de lokale winkel te verwijderen. Gebruik de *krachtparameter* wanneer u wijzigingen moet verwijderen die nog niet zijn gesynchroniseerd.

Zie [Offline gegevenssynchroniseren in Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works)voor meer informatie over deze concepten.

## <a name="more-info"></a>Meer informatie
De volgende onderwerpen geven aanvullende achtergrondinformatie over de offline synchronisatiefunctie van mobiele apps:

* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[een windows-app maken]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync (PurgeAsync)]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
