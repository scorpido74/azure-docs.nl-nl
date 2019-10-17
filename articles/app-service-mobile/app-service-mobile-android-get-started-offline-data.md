---
title: Offline synchronisatie inschakelen voor uw mobiele Azure-app (Android)
description: Meer informatie over het gebruik van App Service Mobile Apps om offline gegevens in uw Android-toepassing in de cache op te slaan en te synchroniseren
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9c845c09c6b05436017f98323dfa78185cd58aa6
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388985"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Offline synchronisatie inschakelen voor uw mobiele Android-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
In deze zelf studie wordt de functie voor offline synchronisatie van Azure Mobile Apps voor Android beschreven. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app @ no__t-0viewing, het toevoegen of wijzigen van gegevens @ no__t-1even wanneer er geen netwerk verbinding is. Wijzigingen worden opgeslagen in een lokale data base. Zodra het apparaat weer online is, worden deze wijzigingen gesynchroniseerd met de externe back-end.

Als dit de eerste ervaring met Azure Mobile Apps is, moet u eerst de zelf studie [een Android-app maken]. Als u het gedownloade Quick Start Server-project niet gebruikt, moet u de pakketten voor gegevens toegangs uitbreidingen toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie over server extensie pakketten.

Zie het onderwerp [Offlinesynchronisatie van gegevens in Azure Mobile Apps]voor meer informatie over de functie voor offline synchronisatie.

## <a name="update-the-app-to-support-offline-sync"></a>De app bijwerken voor de ondersteuning van offline synchronisatie
Met offline synchronisatie leest u naar en schrijft u vanuit een *synchronisatie tabel* (met behulp van de *IMobileServiceSyncTable* -Interface), die deel uitmaakt van een **sqlite** -Data Base op uw apparaat.

Als u wijzigingen wilt pushen en pullen tussen het apparaat en Azure Mobile Services, gebruikt u een *synchronisatie context* (*mobileserviceclient te maken. SyncContext*), die u initialiseert met de lokale Data Base om gegevens lokaal op te slaan.

1. In `TodoActivity.java` moet u een opmerking toevoegen aan de bestaande definitie van `mToDoTable` en de opmerking de versie van de synchronisatie tabel opheffen:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. In de `onCreate`-methode kunt u de bestaande initialisatie van `mToDoTable` inchecken en de opmerking over deze definitie opheffen:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. In `refreshItemsFromTable` wordt de definitie van `results` door commentaar en wordt deze definitie onvermeld:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Commentaar uit de definitie van `refreshItemsFromMobileServiceTable`.
5. Verwijder de opmerking over de definitie van `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Verwijder de opmerking over de definitie van `sync`:
   
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
In deze sectie kunt u het gedrag testen met WiFi aan en vervolgens Wi-Fi uitschakelen om een offline scenario te maken.

Wanneer u gegevens items toevoegt, worden deze opgeslagen in de lokale SQLite-opslag, maar pas gesynchroniseerd met de mobiele service als u op de knop **vernieuwen** klikt. Andere apps hebben mogelijk andere vereisten met betrekking tot de gegevens die moeten worden gesynchroniseerd, maar voor demo doeleinden heeft deze zelf studie de gebruiker expliciet een aanvraag indienen.

Wanneer u op deze knop klikt, wordt een nieuwe achtergrond taak gestart. Eerst worden alle wijzigingen die in het lokale archief zijn aangebracht, gepusht met synchronisatie context en worden vervolgens alle gewijzigde gegevens van Azure naar de lokale tabel opgehaald.

### <a name="offline-testing"></a>Offline testen
1. Plaats het apparaat of de Simulator in de *vliegtuig*stand. Hiermee maakt u een offline scenario.
2. Voeg enkele *TODO* -items toe of markeer sommige items als voltooid. Sluit het apparaat of de Simulator (of sluit de app af) en start de computer opnieuw op. Controleer of uw wijzigingen zijn doorgevoerd op het apparaat omdat ze zijn opgeslagen in de lokale SQLite-opslag.
3. Bekijk de inhoud van de Azure *TodoItem* -tabel met een SQL-hulp programma zoals *SQL Server Management Studio*, of een rest-client, zoals *Fiddler* of *postman*. Controleren of de nieuwe items *niet* zijn gesynchroniseerd met de server
   
       + Voor een node. js-back-end gaat u naar de [Azure Portal](https://portal.azure.com/)en klikt u in de back-end van uw mobiele app op **eenvoudige tabellen** > **TodoItem** om de inhoud van de tabel `TodoItem` weer te geven.
       + Voor een .NET-back-end bekijkt u de inhoud van de tabel met een SQL-hulp programma zoals *SQL Server Management Studio*, of een rest-client, zoals *Fiddler* of *postman*.
4. Schakel WiFi in op het apparaat of in de Simulator. Klik vervolgens op de knop **vernieuwen** .
5. Bekijk de TodoItem-gegevens opnieuw in de Azure Portal. De nieuwe en gewijzigde TodoItems moeten nu worden weer gegeven.

## <a name="additional-resources"></a>Aanvullende bronnen
* [Offlinesynchronisatie van gegevens in Azure Mobile Apps]
* [Cloud cover: offline synchronisatie in Azure Mobile Services] \(note: de video bevindt zich op Mobile Services, maar offline synchronisatie werkt op dezelfde manier als in azure Mobile apps @ no__t-2

<!-- URLs. -->

[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Een Android-app maken]: app-service-mobile-android-get-started.md

[Cloud cover: offline synchronisatie in azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

