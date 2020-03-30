---
title: Offlinesynchronisatie inschakelen (Xamarin.Forms) | Microsoft Documenten
description: Meer informatie over het gebruik van App Service Mobile App om offline gegevens in uw toepassing Xamarin.Forms in de cache te bewaren en te synchroniseren.
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 57776073ef7e2760b308df22280faf1d65b8d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458848"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Xamarin.Forms-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
In deze zelfstudie wordt de offline synchronisatiefunctie van Azure Mobile Apps voor Xamarin.Forms geïntroduceerd. Met offline synchronisatie kunnen eindgebruikers communiceren met een mobiele app, gegevens weergeven, toevoegen of wijzigen, zelfs als er geen netwerkverbinding is. Wijzigingen worden opgeslagen in een lokale database. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe service.

Deze zelfstudie is gebaseerd op de Quickstart-oplossing xamarin.Forms voor mobiele apps die u maakt wanneer u de zelfstudie voltooit [Een Xamarin iOS-app maken]. De quickstart-oplossing voor Xamarin.Forms bevat de code ter ondersteuning van offline synchronisatie, die alleen moet worden ingeschakeld. In deze zelfstudie werkt u de quickstart-oplossing bij om de offline functies van Azure Mobile Apps in te schakelen. We markeren ook de offline-specifieke code in de app. Als u de gedownloade quickstart-oplossing niet gebruikt, moet u de uitbreidingspakketten voor gegevenstoegang aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor][1]meer informatie over serverextensiepakketten.

Zie het onderwerp [Offline gegevenssynchroniseren in Azure Mobile Apps][2]voor meer informatie over de functie offline synchronisatie.

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Offlinesynchronisatiefunctionaliteit inschakelen in de quickstart-oplossing
De offline synchronisatiecode is opgenomen in het project met behulp van C# preprocessor richtlijnen. Wanneer het symbool **OFFLINE\_SYNC\_INGESCHAKELD** is gedefinieerd, worden deze codepaden opgenomen in de build. Voor Windows-apps moet u ook het SQLite-platform installeren.

1. Klik in Visual Studio met de rechtermuisknop op de oplossing > **NuGet-pakketten beheren voor oplossing...** en zoek en installeer vervolgens het **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-pakket voor alle projecten in de oplossing.
2. Open in de Solution Explorer het TodoItemManager.cs bestand van het project met **Portable** in de naam, dat is Portable Class Library-project, en geef vervolgens geen commentaar op de volgende preprocessorrichtlijn:

        #define OFFLINE_SYNC_ENABLED
3. (Optioneel) Als u Windows-apparaten wilt ondersteunen, installeert u een van de volgende SQLite-runtime-pakketten:

   * **Windows 8.1 Runtime:** Installeer [SQLite voor Windows 8.1][3].
   * **Windows Phone 8.1:** Installeer [SQLite voor Windows Phone 8.1][4].
   * **Universeel Windows-platform** Installeer [SQLite voor de Universal Windows Universal][5].

     Hoewel de quickstart geen Universal Windows-project bevat, wordt het Universal Windows-platform ondersteund met Xamarin Forms.
4. (Optioneel) Klik in elk Project voor windows-apps met de rechtermuisknop **Extensions**op **Naslagreferenties...** > **Add Reference...** De **Windows-map** > extensies uitvouwen.
    Schakel de juiste **SQLite voor Windows** SDK in, samen met de **Visual C++ 2013 Runtime voor Windows** SDK.
    De SQLite SDK-namen variëren enigszins per Windows-platform.

## <a name="review-the-client-sync-code"></a>De synchronisatiecode van de client controleren
Hier is een kort overzicht van wat al `#if OFFLINE_SYNC_ENABLED` is opgenomen in de tutorial code binnen de richtlijnen. De offline synchronisatiefunctionaliteit bevindt zich in het TodoItemManager.cs projectbestand in het project Portable Class Library. Zie [Offline gegevenssynchroniseren in Azure Mobile Apps][2]voor een conceptueel overzicht van de functie.

* Voordat tabelbewerkingen kunnen worden uitgevoerd, moet de lokale winkel worden geïnitialiseerd. De lokale winkeldatabase wordt geïnitialiseerd in de constructor van de klasse **TodoItemManager** met behulp van de volgende code:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Deze code maakt een nieuwe lokale SQLite-database met behulp van de **MobileServiceSQLiteStore-klasse.**

    Met de methode **DefineTable** wordt een tabel gemaakt in het lokale archief die overeenkomt met de velden in het opgegeven type.  Het type hoeft niet alle kolommen in de externe database op te nemen. Het is mogelijk om een subset van kolommen op te slaan.
* Het veld **todoTable** in **TodoItemManager** is een **IMobileServiceSyncTable-type** in plaats van **IMobileServiceTable.** Deze klasse gebruikt de lokale database voor alle tabelbewerkingen voor het maken, lezen, bijwerken en verwijderen van (CRUD). U bepaalt zelf wanneer deze wijzigingen worden doorgevoerd naar de back-end van de mobiele app door **pushasync** te bellen op de **IMobileServiceSyncContext.** De synchronisatiecontext helpt tabelrelaties te behouden door wijzigingen bij te houden en te pushen in alle tabellen die een client-app heeft gewijzigd wanneer **PushAsync** wordt aangeroepen.

    De volgende **SyncAsync-methode** wordt opgeroepen om te synchroniseren met de back-end van de mobiele app:

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

    In dit voorbeeld wordt gebruik gemaakt van eenvoudige foutafhandeling met de standaardsynchronisatiehandler. Een echte toepassing zou omgaan met de verschillende fouten, zoals netwerkvoorwaarden en server conflicten met behulp van een aangepaste **IMobileServiceSyncHandler** implementatie.

## <a name="offline-sync-considerations"></a>Overwegingen voor offline synchronisatie
In het voorbeeld wordt de **syncasync-methode** alleen aangeroepen bij het opstarten en wanneer een synchronisatie wordt aangevraagd.  Als u een synchronisatie wilt starten in een Android- of iOS-app, trekt u de lijst met items naar beneden. voor Windows, gebruik maken van de **knop Synchroniseren.** In een echte toepassing u ook de synchronisatietrigger maken wanneer de netwerkstatus verandert.

Wanneer een pull wordt uitgevoerd tegen een tabel die in behandeling is met lokale updates die door de context worden bijgehouden, activeert die pull-bewerking automatisch een voorafgaande contextpush. Wanneer u items in dit voorbeeld vernieuwt, toevoegt en voltooit, u de expliciete **PushAsync-oproep** weglaten.

In de meegeleverde code worden alle records in de externe TodoItem-tabel opgevraagd, maar het is ook mogelijk om records te filteren door een query-id en query door te geven aan **PushAsync.** Zie de sectie *Incrementele synchronisatie* in [Offline gegevenssynchronisatie in Azure Mobile Apps][2]voor meer informatie.

## <a name="run-the-client-app"></a>De client-app uitvoeren
Als offline synchronisatie nu is ingeschakeld, voert u de clienttoepassing ten minste eenmaal uit op elk platform om de lokale winkeldatabase te vullen. Simuleer later een offline scenario en wijzigt u de gegevens in de lokale winkel terwijl de app offline is.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Het synchronisatiegedrag van de client-app bijwerken
Wijzig in deze sectie het clientproject om een offline scenario te simuleren met behulp van een ongeldige toepassings-URL voor uw back-end. U ook netwerkverbindingen uitschakelen door uw apparaat naar 'Vliegtuigmodus' te verplaatsen.  Wanneer u gegevensitems toevoegt of wijzigt, worden deze wijzigingen in het lokale archief bewaard, maar niet gesynchroniseerd met het backend-gegevensarchief totdat de verbinding is hersteld.

1. Open in de Oplossingsverkenner het Constants.cs projectbestand van `ApplicationURL` het project **Portable** en wijzig de waarde van het aanwijzen van een ongeldige URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Open het TodoItemManager.cs bestand van het **Portable-project** en voeg vervolgens een **addertje onder** het gras toe voor de klasse **Basisuitzondering** aan de **proef... vangblok** in **SyncAsync**. Dit **vangblok** schrijft het uitzonderingsbericht aan de console, als volgt:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. De client-app bouwen en uitvoeren.  Voeg een aantal nieuwe items toe. Merk op dat er een uitzondering is aangemeld in de console voor elke poging om te synchroniseren met de backend. Deze nieuwe items bestaan alleen in de lokale winkel totdat ze kunnen worden geduwd naar de mobiele backend. De client-app gedraagt zich alsof deze is verbonden met de backend en ondersteunt alle bewerkingen voor maken, lezen, bijwerken, verwijderen (CRUD).
4. Sluit de app en start deze opnieuw op om te controleren of de nieuwe items die u hebt gemaakt, blijven bestaan in de lokale winkel.
5. (Optioneel) Gebruik Visual Studio om de tabel Azure SQL Database weer te geven om te zien dat de gegevens in de backenddatabase niet zijn gewijzigd.

    Open **Server Explorer**in Visual Studio. Navigeer naar uw database in **Azure**->**SQL-databases.** Klik met de rechtermuisknop op uw database en selecteer **Openen in SQL Server Object Explorer**. Nu u bladeren naar uw SQL-databasetabel en de inhoud ervan.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>De client-app bijwerken om de back-end van uw mobiele telefoon verbinding weer aan te sluiten
Sluit in deze sectie de app opnieuw aan op de mobiele backend, die de app simuleert die terugkomt naar een online status. Wanneer u de vernieuwingsbeweging uitvoert, worden gegevens gesynchroniseerd met de back-end van uw mobiele telefoon.

1. Heropenen Constants.cs. Corrigeer `applicationURL` de to-point naar de juiste URL.
2. De client-app opnieuw opbouwen en uitvoeren. De app probeert te synchroniseren met de mobiele app back-end na de lancering. Controleer of er geen uitzonderingen zijn aangemeld bij de foutopsporingsconsole.
3. (Optioneel) Bekijk de bijgewerkte gegevens met SQL Server Object Explorer of een REST-tool zoals Fiddler of [Postman.][6] Let op dat de gegevens zijn gesynchroniseerd tussen de backenddatabase en de lokale winkel.

    Let op: de gegevens zijn gesynchroniseerd tussen de database en de lokale winkel en bevat de items die u hebt toegevoegd terwijl de verbinding met uw app is verbroken.

## <a name="additional-resources"></a>Aanvullende resources
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps][2]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
