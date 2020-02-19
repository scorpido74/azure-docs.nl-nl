---
title: Offline synchronisatie inschakelen (Xamarin. Forms) | Microsoft Docs
description: Leer hoe u App Service mobiele app kunt gebruiken om offline gegevens in de cache op te slaan en te synchroniseren in uw Xamarin. Forms-toepassing.
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 57776073ef7e2760b308df22280faf1d65b8d104
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458848"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele app Xamarin. Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
In deze zelf studie wordt de functie voor offline synchronisatie van Azure Mobile Apps voor Xamarin. Forms geïntroduceerd. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app--gegevens weer geven, toevoegen of wijzigen, zelfs wanneer er geen netwerk verbinding is. Wijzigingen worden opgeslagen in een lokale data base. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

Deze zelf studie is gebaseerd op de Quick Start-oplossing Xamarin. Forms voor Mobile Apps die u maakt wanneer u de zelf studie hebt voltooid [een Xamarin iOS-app maken]. De Quick Start-oplossing voor Xamarin. Forms bevat de code voor de ondersteuning van offline synchronisatie, die alleen moet worden ingeschakeld. In deze zelf studie werkt u de Quick Start-oplossing bij om de offline functies van Azure Mobile Apps in te scha kelen. We markeren ook de offline-specifieke code in de app. Als u de gedownloade Quick Start-oplossing niet gebruikt, moet u de pakketten voor gegevens toegangs uitbreidingen toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps][1]voor meer informatie over server extensie pakketten.

Zie het onderwerp [offline gegevens synchronisatie in Azure Mobile apps][2]voor meer informatie over de functie voor offline synchronisatie.

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>De functie voor offline synchronisatie inschakelen in de Quick Start-oplossing
De offline synchronisatie code is opgenomen in het project door preprocessor-instructies te gebruiken C# . Wanneer het pictogram **OFFLINE\_SYNC\_ingeschakeld** is gedefinieerd, worden deze code paden opgenomen in de build. Voor Windows-apps moet u ook het SQLite-platform installeren.

1. Klik in Visual Studio met de rechter muisknop op de oplossing > **NuGet-pakketten beheren voor oplossing...** en zoek en installeer het NuGet-pakket **micro soft. Azure. Mobile. client. SQLiteStore** voor alle projecten in de oplossing.
2. Open in het Solution Explorer het TodoItemManager.cs-bestand van het project met een **draag bare** naam, een draagbaar Class Library-project en verwijder vervolgens de opmerking over de volgende preprocessor-instructie:

        #define OFFLINE_SYNC_ENABLED
3. Beschrijving Als u Windows-apparaten wilt ondersteunen, installeert u een van de volgende SQLite-runtime pakketten:

   * **Windows 8,1 runtime:** Installeer [sqlite voor Windows 8,1][3].
   * **Windows Phone 8,1:** Installeer [sqlite voor Windows Phone 8,1][4].
   * **Universeel Windows-platform** Installeer [sqlite voor de universele Windows Universal][5].

     Hoewel de Snelstartgids geen universeel Windows-project bevat, wordt het Universal Windows-platform ondersteund met Xamarin Forms.
4. Beschrijving Klik in elk Windows-app-project met de rechter muisknop op **verwijzingen** > **referentie toevoegen...** Vouw de **Windows** -map > **extensies**uit.
    Schakel de juiste **sqlite voor Windows** SDK in samen met **de C++ Visual 2013 runtime voor de Windows** -SDK.
    De SQLite-SDK-namen variëren enigszins met elk Windows-platform.

## <a name="review-the-client-sync-code"></a>De client synchronisatie code controleren
Hier volgt een kort overzicht van wat er al in de zelfstudie code in de `#if OFFLINE_SYNC_ENABLED`-instructies is opgenomen. De offline synchronisatie functionaliteit bevindt zich in het TodoItemManager.cs-project bestand in het Portable Class Library-project. Zie [offline Data Sync in Azure Mobile apps][2]voor een conceptueel overzicht van de functie.

* Voordat een tabel bewerking kan worden uitgevoerd, moet het lokale archief worden geïnitialiseerd. De lokale store-data base wordt geïnitialiseerd in de **TodoItemManager** -klasse-constructor met de volgende code:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Met deze code wordt een nieuwe lokale SQLite-data base gemaakt met behulp van de **MobileServiceSQLiteStore** -klasse.

    De methode **DefineTable** maakt een tabel in het lokale archief die overeenkomt met de velden in het geleverde type.  Het type moet niet alle kolommen bevatten die zich in de externe data base bevinden. Het is mogelijk om een subset kolommen op te slaan.
* Het veld **todoTable** in **TodoItemManager** is een **IMobileServiceSyncTable** -type in plaats van **IMobileServiceTable**. Deze klasse maakt gebruik van de lokale Data Base voor alle bewerkingen voor maken, lezen, bijwerken en verwijderen (ruwe). U bepaalt wanneer deze wijzigingen worden gepusht naar de back-end van de mobiele app door het aanroepen van **PushAsync** op de **IMobileServiceSyncContext**. De synchronisatie context helpt bij het bewaren van tabel relaties door wijzigingen in alle tabellen bij te houden en te pushen. een client-app is gewijzigd wanneer **PushAsync** wordt aangeroepen.

    De volgende **SyncAsync** -methode wordt aangeroepen om te synchroniseren met de back-end van de mobiele app:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Dit voor beeld maakt gebruik van eenvoudige fout afhandeling met de standaardsynchronisatie-handler. Met een echte toepassing worden de verschillende fouten, zoals netwerk omstandigheden en server conflicten, verwerkt met behulp van een aangepaste **IMobileServiceSyncHandler** -implementatie.

## <a name="offline-sync-considerations"></a>Overwegingen voor offline synchronisatie
In het voor beeld wordt de methode **SyncAsync** alleen aangeroepen voor opstarten en wanneer een synchronisatie wordt aangevraagd.  Als u een synchronisatie in een Android-of iOS-app wilt starten, gaat u naar de lijst items. voor Windows, gebruikt u de knop **synchroniseren** . In een echte toepassing kunt u ook de synchronisatie trigger maken wanneer de netwerk status wordt gewijzigd.

Wanneer een pull-bewerking wordt uitgevoerd op basis van een tabel met in behandeling zijnde lokale updates die worden bijgehouden door de context, activeert die pull-bewerkingen automatisch een voor gaande context push. Wanneer u items in dit voor beeld vernieuwt, toevoegt en voltooit, kunt u de expliciete **PushAsync** -aanroep weglaten.

In de gegeven code worden alle records in de tabel externe TodoItem opgevraagd, maar het is ook mogelijk om records te filteren door een query-ID en een query door te geven aan **PushAsync**. Zie de sectie *incrementele synchronisatie* in [offline gegevens synchronisatie in azure Mobile apps][2]voor meer informatie.

## <a name="run-the-client-app"></a>De client-app uitvoeren
Als offline synchronisatie nu is ingeschakeld, voert u de client toepassing ten minste één keer uit op elk platform om de lokale store-data base te vullen. Daarna kunt u een offline scenario simuleren en de gegevens in het lokale archief wijzigen terwijl de app offline is.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Het synchronisatie gedrag van de client-app bijwerken
In deze sectie wijzigt u het client project om een offline scenario te simuleren door gebruik te maken van een ongeldige toepassings-URL voor uw back-end. U kunt ook netwerk verbindingen uitschakelen door het apparaat naar de vliegtuig modus te verplaatsen.  Wanneer u gegevens items toevoegt of wijzigt, worden deze wijzigingen opgeslagen in het lokale archief, maar niet gesynchroniseerd met het back-end-gegevens archief totdat de verbinding opnieuw tot stand is gebracht.

1. Open in het Solution Explorer het project bestand Constants.cs uit het **draag bare** project en wijzig de waarde van `ApplicationURL` zodanig dat deze verwijst naar een ongeldige URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Open het TodoItemManager.cs-bestand van het **draag bare** project en voeg vervolgens een **Catch** -waarde voor de basis **uitzonderings** klasse toe aan de **try... Catch** -blok in **SyncAsync**. Dit **Catch** -blok schrijft het uitzonderings bericht als volgt naar de-console:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. De client-app bouwen en uitvoeren.  Voeg enkele nieuwe items toe. U ziet dat er een uitzonde ring wordt geregistreerd in de-console voor elke poging om te synchroniseren met de back-end. Deze nieuwe items bestaan alleen in het lokale archief totdat ze naar de mobiele back-end kunnen worden gepusht. De client-app gedraagt zich alsof deze is verbonden met de back-end, zodat alle bewerkingen voor maken, lezen, bijwerken, verwijderen (ruw) worden ondersteund.
4. Sluit de app en start deze opnieuw om te controleren of de nieuwe items die u hebt gemaakt, zijn opgeslagen in het lokale archief.
5. Beschrijving Gebruik Visual Studio om uw Azure SQL Database tabel weer te geven om te zien dat de gegevens in de back-end-data base niet zijn gewijzigd.

    Open **Server Explorer**in Visual Studio. Navigeer naar uw data base in **Azure**->**SQL-data bases**. Klik met de rechter muisknop op de data base en selecteer **openen in SQL Server-objectverkenner**. U kunt nu naar de SQL database-tabel en de inhoud bladeren.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>De client-app bijwerken om uw mobiele back-end opnieuw te verbinden
In deze sectie verbindt u de app opnieuw met de mobiele back-end, waardoor de app wordt weer gegeven in een online status. Wanneer u de vernieuwings beweging uitvoert, worden de gegevens gesynchroniseerd met uw mobiele back-end.

1. Open Constants.cs opnieuw. Corrigeer de `applicationURL` zodat deze naar de juiste URL verwijst.
2. Bouw en voer de client-app opnieuw uit. De app probeert te synchroniseren met de back-end van de mobiele app nadat deze is gestart. Controleer of er geen uitzonde ringen worden vastgelegd in de console voor fout opsporing.
3. Beschrijving Bekijk de bijgewerkte gegevens met behulp van SQL Server-objectverkenner of een REST hulp programma zoals Fiddler of [postman][6]. U ziet dat de gegevens zijn gesynchroniseerd tussen de back-end-data base en het lokale archief.

    U ziet dat de gegevens zijn gesynchroniseerd tussen de data base en het lokale archief en de items bevat die u hebt toegevoegd toen de verbinding van uw app werd verbroken.

## <a name="additional-resources"></a>Aanvullende bronnen
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps][2]
* [PROCEDURE voor Azure Mobile Apps .NET SDK][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
