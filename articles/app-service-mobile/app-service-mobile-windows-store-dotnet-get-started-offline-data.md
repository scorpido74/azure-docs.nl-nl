---
title: Offline synchronisatie inschakelen voor uw UWP-app
description: Meer informatie over het gebruik van een mobiele Azure-app om offline gegevens in uw Universeel Windows-platform-app (UWP) in de cache op te slaan en te synchroniseren.
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 66ae11fb0532ae2a138eb8590eb92de15e997dfa
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668411"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Offlinesynchronisatie voor uw Windows-app inschakelen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht
In deze zelf studie wordt uitgelegd hoe u offline ondersteuning toevoegt aan een Universeel Windows-platform-app (UWP) met behulp van een back-end voor mobiele apps van Azure. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app--gegevens weer geven, toevoegen of wijzigen, zelfs wanneer er geen netwerk verbinding is. Wijzigingen worden opgeslagen in een lokale data base. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe back-end.

In deze zelf studie werkt u het UWP-app-project bij vanuit de zelf studie [een Windows-app maken] ter ondersteuning van de offline functies van Azure Mobile apps. Als u het gedownloade Quick Start Server-project niet gebruikt, moet u de pakketten voor gegevens toegangs uitbreidingen toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie over server extensie pakketten.

Zie het onderwerp [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over de functie voor offline synchronisatie.

## <a name="requirements"></a>Vereisten  
Voor deze zelf studie zijn de volgende vereisten vereist:

* Visual Studio 2013 uitgevoerd op Windows 8,1 of hoger.
* Volt ooien van [een Windows-app maken][een Windows-app maken].
* [Azure Mobile Services SQLite-archief][sqlite store nuget]
* [SQLite voor het ontwikkelen van Universeel Windows-platform](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>De client-app bijwerken voor de ondersteuning van offline functies
Met de offline functies van Azure Mobile App kunt u met een lokale data base werken wanneer u een offline scenario hebt. Als u deze functies in uw app wilt gebruiken, initialiseert u een [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable]-interface. SQLite wordt gebruikt als het lokale archief op het apparaat.

1. Installeer de [sqlite-runtime voor de universeel Windows-platform](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. Open in Visual Studio de NuGet-pakket Manager voor het UWP-app-project dat u hebt voltooid in de zelf studie [een Windows-app maken] .
    Zoek en installeer het pakket **micro soft. Azure. Mobile. client. SQLiteStore** NuGet.
3. Klik in Solution Explorer met de rechter muisknop op **verwijzingen** > **referentie toevoegen...** > **universele Windows** >- **extensies**en schakel vervolgens zowel **SQLite voor universeel Windows-platform** als **Visual C++ 2015 runtime in voor universeel Windows-platform apps**.

    ![SQLite UWP-referentie toevoegen][1]
4. Open het MainPage.xaml.cs-bestand en verwijder de opmerking over de `#define OFFLINE_SYNC_ENABLED` definitie.
5. Druk in Visual Studio op de toets **F5** om de client-app opnieuw te bouwen en uit te voeren. De app werkt op dezelfde manier als voordat u offline synchronisatie inschakelde. De lokale data base is nu echter gevuld met gegevens die in een offline scenario kunnen worden gebruikt.

## <a name="update-sync"></a>De app bijwerken om de verbinding met de back-end te verbreken
In deze sectie verbreekt u de verbinding met de back-end van uw mobiele app om een offline situatie te simuleren. Wanneer u gegevens items toevoegt, krijgt u een uitzonderings-handler te zien dat de app zich in de offline modus bevindt. In deze status worden nieuwe items toegevoegd aan het lokale archief en worden ze gesynchroniseerd met de back-end van de mobiele app wanneer push de volgende keer wordt uitgevoerd in een verbonden status.

1. Bewerk App.xaml.cs in het gedeelde project. Noteer de initialisatie van de **mobileserviceclient te maken** en voeg de volgende regel toe, waarbij een ongeldige URL voor de mobiele app wordt gebruikt:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    U kunt ook offline gedrag demonstreren door Wi-Fi-en cellulaire netwerken uit te scha kelen op het apparaat of door de vliegtuig modus te gebruiken.
2. Druk op **F5** om de app te bouwen en uit te voeren. U ziet dat de synchronisatie is mislukt bij het vernieuwen wanneer de app is gestart.
3. Voer nieuwe items in en Let op dat push mislukt met de status [CancelledByNetworkError] telkens wanneer u op **Opslaan**klikt. De nieuwe TODO-items bestaan echter pas in het lokale archief als ze kunnen worden gepusht naar de back-end van de mobiele app.  Als u in een productie-app deze uitzonde ringen onderdrukt, gedraagt de client-app zich alsof deze nog steeds is verbonden met de back-end van de mobiele app.
4. Sluit de app en start deze opnieuw om te controleren of de nieuwe items die u hebt gemaakt, zijn opgeslagen in het lokale archief.
5. Beschrijving Open **Server Explorer**in Visual Studio. Navigeer naar uw data base in **Azure**->**SQL-data bases**. Klik met de rechter muisknop op de data base en selecteer **openen in SQL Server-objectverkenner**. U kunt nu naar de SQL database-tabel en de inhoud bladeren. Controleer of de gegevens in de back-end-data base niet zijn gewijzigd.
6. Beschrijving Gebruik een REST-hulp programma, zoals Fiddler of Postman, om een query op uw mobiele back-end uit te geven met behulp van een GET-query in de vorm `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>De app bijwerken om de back-end van uw mobiele app opnieuw te verbinden
In deze sectie verbindt u de app opnieuw met de back-end van de mobiele app. Deze wijzigingen simuleren een netwerk herverbinding op de app.

Wanneer u de toepassing voor het eerst uitvoert, roept de gebeurtenis-handler van `OnNavigatedTo` `InitLocalStoreAsync`. Met deze methode wordt `SyncAsync` om uw lokale archief te synchroniseren met de back-end-data base. De app probeert te synchroniseren bij het opstarten.

1. Open App.xaml.cs in het gedeelde project en verwijder de opmerking bij uw eerdere initialisatie van `MobileServiceClient` om de juiste URL voor de mobiele app te gebruiken.
2. Druk op de toets **F5** om de app opnieuw te bouwen en uit te voeren. De app synchroniseert uw lokale wijzigingen met de back-end van de mobiele app van Azure met push-en pull-bewerkingen wanneer de gebeurtenis-handler van `OnNavigatedTo` wordt uitgevoerd.
3. Beschrijving Bekijk de bijgewerkte gegevens met behulp van SQL Server-objectverkenner of een REST hulp programma, zoals Fiddler. U ziet dat de gegevens zijn gesynchroniseerd tussen de back-updatabase van de Azure Mobile App en het lokale archief.
4. Klik in de app op het selectie vakje naast een aantal items om ze in het lokale archief te volt ooien.

   `UpdateCheckedTodoItem` roept `SyncAsync` op om elk voltooid item te synchroniseren met de back-end van de mobiele app. `SyncAsync` push-en pull-aanroepen. **Wanneer u echter een pull uitvoert op een tabel die door de client is gewijzigd, wordt een push-bewerking altijd automatisch uitgevoerd**. Dit gedrag zorgt ervoor dat alle tabellen in het lokale archief samen met de relaties consistent blijven. Dit gedrag kan leiden tot een onverwachte push.  Zie [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over dit gedrag.

## <a name="api-summary"></a>API-overzicht
Ter ondersteuning van de offline functies van Mobile Services hebben we de [IMobileServiceSyncTable] -interface en geïnitialiseerde [mobileserviceclient te maken. SyncContext][synccontext] met een lokale sqlite-data base gebruikt. Wanneer u offline bent, worden de normale ruwe bewerkingen voor Mobile Apps uitgevoerd alsof de app nog steeds verbonden is terwijl de bewerkingen worden uitgevoerd op het lokale archief. De volgende methoden worden gebruikt voor het synchroniseren van het lokale archief met de-server:

* **[PushAsync]** Omdat deze methode lid is van [IMobileServicesSyncContext], worden wijzigingen in alle tabellen naar de back-end gepusht. Alleen records met lokale wijzigingen worden naar de server verzonden.
* **[PullAsync]** Een pull wordt gestart vanuit een [IMobileServiceSyncTable]. Wanneer er bijgehouden wijzigingen in de tabel zijn, wordt een impliciete Push uitgevoerd om ervoor te zorgen dat alle tabellen in het lokale archief samen met de relaties consistent blijven. De *pushOtherTables* para meter bepaalt of andere tabellen in de context worden gepusht in een impliciete push. De *query* parameter neemt een [IMobileServiceTableQuery\<t >][IMobileServiceTableQuery] of de OData-query reeks om de geretourneerde gegevens te filteren. De para meter *queryId* wordt gebruikt voor het definiëren van incrementele synchronisatie. Zie [offline Data Sync in Azure Mobile apps](app-service-mobile-offline-data-sync.md#how-sync-works)voor meer informatie.
* **[PurgeAsync]** Uw app moet deze methode regel matig aanroepen om verouderde gegevens uit het lokale archief te verwijderen. Gebruik de para meter *Forces* als u wijzigingen wilt opschonen die nog niet zijn gesynchroniseerd.

Zie [offline Data Sync in Azure Mobile apps](app-service-mobile-offline-data-sync.md#how-sync-works)voor meer informatie over deze concepten.

## <a name="more-info"></a>Meer informatie
De volgende onderwerpen bevatten aanvullende achtergrond informatie over de functie voor offline synchronisatie van Mobile Apps:

* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [PROCEDURE voor Azure Mobile Apps .NET SDK][8]

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
[een Windows-app maken]: app-service-mobile-windows-store-dotnet-get-started.md
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
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
