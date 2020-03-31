---
title: Offlinesynchronisatie inschakelen (Android)
description: Meer informatie over het gebruik van mobiele apps voor App Service om offline gegevens in uw Android-toepassing in de cache te bewaren en te synchroniseren.
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c215105af5fe1ef8056b0d816cf2c2a6b96f2038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461620"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Android-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
Deze zelfstudie behandelt de offline synchronisatiefunctie van Azure Mobile Apps voor Android. Met offline synchronisatie kunnen eindgebruikers communiceren&mdash;met een mobiele app&mdash;die gegevens bekijkt, toevoegt of wijzigt, zelfs als er geen netwerkverbinding is. Wijzigingen worden opgeslagen in een lokale database. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe backend.

Als dit uw eerste ervaring met Azure Mobile Apps is, moet u eerst de zelfstudie [Een Android-app maken]voltooien. Als u het gedownloade project voor snelle startserver niet gebruikt, moet u de uitbreidingspakketten voor gegevenstoegang aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie over serverextensiepakketten.

Zie het onderwerp [Offline gegevenssynchroniseren in Azure Mobile Apps]voor meer informatie over de functie offline synchronisatie.

## <a name="update-the-app-to-support-offline-sync"></a>De app bijwerken om offline synchronisatie te ondersteunen
Met offline synchronisatie leest en schrijft u vanuit een *synchronisatietabel* (met behulp van de *IMobileServiceSyncTable-interface),* die deel uitmaakt van een **SQLite-database** op uw apparaat.

Als u wijzigingen tussen het apparaat en Azure Mobile Services wilt doorvoeren en opte stellen, gebruikt u een *synchronisatiecontext* *(MobileServiceClient.SyncContext),* die u initialiseert met de lokale database om gegevens lokaal op te slaan.

1. Geef `TodoActivity.java`in , commentaar `mToDoTable` op de bestaande definitie van en uncomment de sync table versie:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. In `onCreate` de methode, commentaar op `mToDoTable` de bestaande initialisatie van en uncomment deze definitie:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. In `refreshItemsFromTable` commentaar uit `results` de definitie van en uncomment deze definitie:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Commentaar uit de `refreshItemsFromMobileServiceTable`definitie van .
5. Geen commentaar geven `refreshItemsFromMobileServiceTableSyncTable`op de definitie van :
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Geen commentaar geven `sync`op de definitie van :
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>De app testen
In deze sectie test u het gedrag met WiFi aan en schakelt u WiFi uit om een offline scenario te maken.

Wanneer u gegevensitems toevoegt, worden ze bewaard in de lokale SQLite-winkel, maar worden ze niet gesynchroniseerd met de mobiele service totdat u op de knop **Vernieuwen** drukt. Andere apps kunnen verschillende vereisten hebben met betrekking tot wanneer gegevens moeten worden gesynchroniseerd, maar voor demodoeleinden heeft deze zelfstudie de gebruiker expliciet om gevraagd.

Wanneer u op die knop drukt, wordt een nieuwe achtergrondtaak gestart. Het duwt eerst alle wijzigingen in de lokale winkel met behulp van synchronisatie context, dan trekt alle gewijzigde gegevens van Azure naar de lokale tabel.

### <a name="offline-testing"></a>Offline testen
1. Plaats het apparaat of de simulator in *vliegtuigmodus*. Hierdoor wordt een offline scenario mogelijk.
2. Voeg sommige *ToDo-items* toe of markeer sommige items als voltooid. Sluit het apparaat of de simulator af (of sluit de app met geweld) en start opnieuw op. Controleer of uw wijzigingen zijn doorgevoerd op het apparaat omdat ze in de lokale SQLite-winkel worden bewaard.
3. Bekijk de inhoud van de Azure *TodoItem-tabel* met een SQL-tool zoals *SQL Server Management Studio*of een REST-client zoals *Fiddler* of *Postman*. Controleren of de nieuwe items *niet* zijn gesynchroniseerd met de server
   
       + Ga voor een back-end van Node.js naar de [Azure-portal](https://portal.azure.com/)en klik in de back-end van uw mobiele app op **Easy Tables** > **TodoItem** om de inhoud van de `TodoItem` tabel weer te geven.
       + Bekijk voor een .NET-backend de inhoud van de tabel met een SQL-tool zoals *SQL Server Management Studio*of een REST-client zoals *Fiddler* of *Postman.*
4. WiFi inschakelen in het apparaat of de simulator. Druk vervolgens op de knop **Vernieuwen.**
5. Bekijk de TodoItem-gegevens opnieuw in de Azure-portal. De nieuwe en gewijzigde TodoItems moeten nu worden weergegeven.

## <a name="additional-resources"></a>Aanvullende resources
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [Cloud Cover: offline synchronisatie in Azure Mobile Services] \(opmerking: de video is op Mobiele Services, maar offline synchronisatie werkt op een vergelijkbare manier in Azure Mobile Apps\)

<!-- URLs. -->

[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Een Android-app maken]: app-service-mobile-android-get-started.md

[Cloud Cover: Offline synchroniseren in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

