---
title: Offline synchronisatie met mobiele iOS-apps inschakelen | Microsoft Docs
description: Meer informatie over het gebruik van Azure App Service mobiele apps voor het opslaan en synchroniseren van offline gegevens in iOS-toepassingen.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f29a28f9a80b64ef0a6890fa8fc7ecd0ca205e66
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388760"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Offline synchronisatie met mobiele iOS-apps inschakelen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
In deze zelf studie wordt de offline synchronisatie van de Mobile Apps-functie van Azure App Service voor iOS beschreven. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app om gegevens weer te geven, toe te voegen of te wijzigen, zelfs wanneer ze geen netwerk verbinding hebben. Wijzigingen worden opgeslagen in een lokale data base. Zodra het apparaat weer online is, worden de wijzigingen gesynchroniseerd met de externe back-end.

Als dit de eerste ervaring met Mobile Apps is, moet u eerst de zelf studie [een IOS-app maken]volt ooien. Als u het gedownloade Quick Start Server-project niet gebruikt, moet u de pakketten voor gegevens toegangs uitbreidingen toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie over server extensie pakketten.

Zie [Offline gegevens synchronisatie in Mobile Apps]voor meer informatie over de functie voor offline synchronisatie.

## <a name="review-sync"></a>De client synchronisatie code controleren
Het client project dat u hebt gedownload voor de zelf studie [een IOS-app maken] bevat al code die offline synchronisatie ondersteunt met behulp van een lokale kern data base. In deze sectie wordt een overzicht gegeven van wat er al in de zelfstudie code is opgenomen. Zie [Offline gegevens synchronisatie in Mobile Apps]voor een conceptueel overzicht van de functie.

Met de functie voor het offline synchroniseren van gegevens van Mobile Apps kunnen eind gebruikers met een lokale data base werken, zelfs wanneer het netwerk niet toegankelijk is. Als u deze functies in uw app wilt gebruiken, initialiseert u de synchronisatie context van `MSClient` en verwijst u naar een lokaal archief. Vervolgens verwijst u naar de tabel via de **MSSyncTable** -interface.

In **QSTodoService. m** (objectief-C) of **ToDoTableViewController. Swift** (SWIFT) ziet u dat het type van het lid **syncTable** **MSSyncTable**is. Offline synchronisatie maakt gebruik van deze synchronisatie tabel interface in plaats van **MSTable**. Wanneer een synchronisatie tabel wordt gebruikt, worden alle bewerkingen naar het lokale archief verzonden en worden ze alleen gesynchroniseerd met de externe back-end met expliciete push-en pull-bewerkingen.

 Als u een verwijzing naar een synchronisatie tabel wilt ophalen, gebruikt u de methode **syncTableWithName** op `MSClient`. Gebruik in plaats daarvan **tableWithName** om de offline synchronisatie functie te verwijderen.

Voordat een tabel bewerking kan worden uitgevoerd, moet het lokale archief worden geïnitialiseerd. Dit is de relevante code:

* **Doel-C**. In de methode **QSTodoService. init** :

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. In de methode **ToDoTableViewController. viewDidLoad** :

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Met deze methode maakt u een lokaal archief met behulp van de `MSCoreDataStore`-interface, die de Mobile Apps SDK biedt. U kunt ook een ander lokaal archief opgeven door het `MSSyncContextDataSource`-protocol te implementeren. De eerste para meter van **MSSyncContext** wordt ook gebruikt om een conflict-handler op te geven. Omdat we `nil` hebben door gegeven, krijgen we de standaard conflict afhandeling, die mislukt bij een conflict.

Nu gaan we de daad werkelijke synchronisatie bewerking uitvoeren en gegevens ophalen van de externe back-end:

* **Doel-C**. `syncData` pusht eerst nieuwe wijzigingen en roept vervolgens **pullData** aan om gegevens op te halen van de externe back-end. De methode **pullData** haalt op zijn beurt nieuwe gegevens op die overeenkomen met een query:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

In de doel-C-versie wordt in `syncData` eerst **pushWithCompletion** op de synchronisatie context aangeroepen. Deze methode is lid van `MSSyncContext` (en niet de synchronisatie tabel zelf) omdat hiermee wijzigingen in alle tabellen worden gepusht. Alleen records die lokaal op een of andere manier zijn gewijzigd (via CUD-bewerkingen), worden naar de server verzonden. Vervolgens wordt de helper **pullData** aangeroepen, die **MSSyncTable. pullWithQuery** aanroept om externe gegevens op te halen en op te slaan in de lokale data base.

In de SWIFT-versie, omdat de push bewerking niet strikt nood zakelijk was, is er geen aanroep van **pushWithCompletion**. Als er wijzigingen in de synchronisatie context optreden voor de tabel die een push-bewerking uitvoert, wordt door pull altijd eerst een push-bericht verzonden. Als u echter meer dan één synchronisatie tabel hebt, kunt u het beste pushen expliciet aanroepen om ervoor te zorgen dat alles consistent is in alle gerelateerde tabellen.

In zowel de doel-C-als de SWIFT-versie kunt u de methode **pullWithQuery** gebruiken om een query op te geven voor het filteren van de records die u wilt ophalen. In dit voor beeld worden alle records in de tabel externe `TodoItem` opgehaald met de query.

De tweede para meter van **pullWithQuery** is een query-id die wordt gebruikt voor *incrementele synchronisatie*. Met incrementele synchronisatie worden alleen records opgehaald die sinds de laatste synchronisatie zijn gewijzigd, met behulp van de `UpdatedAt` tijds tempel van de record (`updatedAt` in het lokale archief). De query-ID moet een beschrijvende teken reeks zijn die uniek is voor elke logische query in uw app. Als u geen incrementele synchronisatie wilt uitvoeren, geeft u `nil` door als query-ID. Deze methode kan mogelijk inefficiënt zijn, omdat hiermee alle records voor elke pull-bewerking worden opgehaald.

De doel-C-app wordt gesynchroniseerd wanneer u gegevens wijzigt of toevoegt, wanneer een gebruiker de penbeweging voor vernieuwen uitvoert en bij het starten.

De snelle app wordt gesynchroniseerd wanneer de gebruiker de penbeweging voor vernieuwen en bij het starten uitvoert.

Omdat de app wordt gesynchroniseerd wanneer gegevens worden gewijzigd (doel-C) of wanneer de app wordt gestart (objectief-C en Swift), wordt ervan uitgegaan dat de gebruiker online is. In een latere sectie werkt u de app bij zodat gebruikers deze kunnen bewerken, zelfs wanneer ze offline zijn.

## <a name="review-core-data"></a>Het kern gegevens model controleren
Wanneer u het offline archief met de basis gegevens gebruikt, moet u bepaalde tabellen en velden in uw gegevens model definiëren. De voor beeld-app bevat al een gegevens model met de juiste indeling. In deze sectie worden deze tabellen door lopen om te laten zien hoe ze worden gebruikt.

Open **QSDataModel. xcdatamodeld**. Er zijn vier tabellen gedefinieerd: drie die worden gebruikt door de SDK en een die wordt gebruikt voor de taak items zelf:
  * MS_TableOperations: Hiermee worden de items bijgehouden die moeten worden gesynchroniseerd met de server.
  * MS_TableOperationErrors: houdt eventuele fouten bij die optreden tijdens offline synchronisatie.
  * MS_TableConfig: houdt de laatst bijgewerkte tijd bij voor de laatste synchronisatie bewerking voor alle pull-bewerkingen.
  * TodoItem: slaat de taak items op. De systeem kolommen **createdAt**, **updatedAt**en **Version** zijn optionele systeem eigenschappen.

> [!NOTE]
> De Mobile Apps SDK reserveert kolom namen die beginnen met ' **``** '. Gebruik dit voor voegsel niet met iets anders dan systeem kolommen. Anders worden de kolom namen gewijzigd wanneer u de externe back-end gebruikt.
>
>

Wanneer u de functie voor offline synchronisatie gebruikt, definieert u de drie systeem tabellen en de gegevens tabel.

### <a name="system-tables"></a>Systeemtabellen

**MS_TableOperations**  

![Kenmerken van MS_TableOperations-tabel][defining-core-data-tableoperations-entity]

| Kenmerk | Type |
| --- | --- |
| id | Geheel getal 64 |
| itemId | Tekenreeks |
| properties | Binaire gegevens |
| Tabel | Tekenreeks |
| tableKind | Geheel getal 16 |


**MS_TableOperationErrors**

 ![Kenmerken van MS_TableOperationErrors-tabel][defining-core-data-tableoperationerrors-entity]

| Kenmerk | Type |
| --- | --- |
| id |Tekenreeks |
| operationId |Geheel getal 64 |
| properties |Binaire gegevens |
| tableKind |Geheel getal 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Kenmerk | Type |
| --- | --- |
| id |Tekenreeks |
| sleutel |Tekenreeks |
| keyType |Geheel getal 64 |
| Tabel |Tekenreeks |
| waarde |Tekenreeks |

### <a name="data-table"></a>Gegevens tabel

**TodoItem**

| Kenmerk | Type | Opmerking |
| --- | --- | --- |
| id | Teken reeks, gemarkeerd als vereist |Primaire sleutel in externe opslag |
| aangevuld | Booleaans | Veld taak item |
| tekst |Tekenreeks |Veld taak item |
| CreatedAt | Datum | Beschrijving Verwijst naar de systeem eigenschap **createdAt** |
| updatedAt | Datum | Beschrijving Verwijst naar de systeem eigenschap **updatedAt** |
| versie | Tekenreeks | Beschrijving Wordt gebruikt om conflicten te detecteren, toegewezen aan versie |

## <a name="setup-sync"></a>Het synchronisatie gedrag van de App wijzigen
In deze sectie wijzigt u de app zodat deze niet wordt gesynchroniseerd bij het starten van de app of wanneer u items invoegt en bijwerkt. Het wordt alleen gesynchroniseerd wanneer de knop beweging vernieuwen wordt uitgevoerd.

**Doel-C**:

1. Wijzig in **QSTodoListViewController. m**de methode **viewDidLoad** om de aanroep te verwijderen naar `[self refresh]` aan het einde van de methode. De gegevens worden nu niet gesynchroniseerd met de server op de app starten. In plaats daarvan wordt het gesynchroniseerd met de inhoud van het lokale archief.
2. In **QSTodoService. m**wijzigt u de definitie van `addItem` zodat deze niet wordt gesynchroniseerd nadat het item is ingevoegd. Verwijder het `self syncData`-blok en vervang dit door het volgende:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Wijzig de definitie van `completeItem` zoals eerder vermeld. Verwijder het blok voor `self syncData` en vervang dit door het volgende:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

In `viewDidLoad`, in **ToDoTableViewController. Swift**, worden de twee regels die hier worden weer gegeven, voor het stoppen van de synchronisatie bij het starten van de app. Op het moment van deze schrijf bewerking wordt de service niet door de app TODO bijgewerkt wanneer iemand een item toevoegt of voltooit. De service wordt alleen bij het starten van de app bijgewerkt.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>De app testen
In deze sectie maakt u verbinding met een ongeldige URL om een offline scenario te simuleren. Wanneer u gegevens items toevoegt, worden deze opgeslagen in de lokale kern gegevens opslag, maar ze worden niet gesynchroniseerd met de back-end van de mobiele app.

1. Wijzig de URL van de mobiele app in **QSTodoService. m** in een ongeldige URL en voer de app opnieuw uit:

   **Doel-C**. In QSTodoService. m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. In ToDoTableViewController. Swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Voeg enkele taak items toe. Sluit de Simulator af (of sluit de app af) en start deze opnieuw. Controleer of de wijzigingen behouden blijven.

3. De inhoud van de tabel externe **TodoItem** weer geven:
   * Voor een node. js-back-end gaat u naar de [Azure Portal](https://portal.azure.com/) en klikt u in de back-end van uw mobiele app op **eenvoudige tabellen** > **TodoItem**.  
   * Voor een .NET-back-end gebruikt u een SQL-hulp programma, zoals SQL Server Management Studio, of een REST-client, zoals Fiddler of postman.  

4. Controleer of de nieuwe items *niet* zijn gesynchroniseerd met de server.

5. Wijzig de URL in **QSTodoService. m**in het juiste item en voer de app opnieuw uit.

6. Voer de vernieuwings beweging uit door de lijst met items op te halen.  
Er wordt een voortgangs kring veld weer gegeven.

7. Bekijk de **TodoItem** -gegevens opnieuw. De nieuwe en gewijzigde taak items moeten nu worden weer gegeven.

## <a name="summary"></a>Samenvatting
Ter ondersteuning van de functie voor offline synchronisatie hebben we de `MSSyncTable`-interface gebruikt en `MSClient.syncContext` geïnitialiseerd met een lokale opslag. In dit geval was het lokale archief een kern database op basis van gegevens.

Wanneer u een lokaal archief met basis gegevens gebruikt, moet u verschillende tabellen met de [juiste systeem eigenschappen](#review-core-data)definiëren.

De normale bewerkingen voor maken, lezen, bijwerken en verwijderen (ruw) voor mobiele apps werken alsof de app nog steeds is verbonden, maar alle bewerkingen worden uitgevoerd voor het lokale archief.

Toen we het lokale archief met de-server hebben gesynchroniseerd, hebben we de methode **MSSyncTable. pullWithQuery** gebruikt.

## <a name="additional-resources"></a>Aanvullende bronnen
* [Offline gegevens synchronisatie in Mobile Apps]
* [Cloud cover: offline synchronisatie in Azure Mobile Services] @no__t 1The video over Mobile Services, maar Mobile apps offline synchronisatie werkt op een vergelijk bare manier. \)

<!-- URLs. -->


[Een iOS-app maken]: app-service-mobile-ios-get-started.md
[Offline gegevens synchronisatie in Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud cover: offline synchronisatie in azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
