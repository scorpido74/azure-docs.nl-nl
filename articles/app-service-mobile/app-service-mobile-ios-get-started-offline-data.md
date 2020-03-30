---
title: Offline synchronisatie inschakelen (iOS)
description: Meer informatie over het gebruik van mobiele azure-apps voor App Service om offlinegegevens in iOS-toepassingen in de cache op te zetten en te synchroniseren.
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: d943213814b999f101a541abb0195a9fdd5a7423
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459171"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Offline synchroniseren met mobiele iOS-apps inschakelen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Overzicht
Deze zelfstudie heeft betrekking op offline synchronisatie met de functie Mobiele apps van Azure App Service voor iOS. Met offline synchroniserende eindgebruikers kunnen communiceren met een mobiele app om gegevens te bekijken, toe te voegen of te wijzigen, zelfs als ze geen netwerkverbinding hebben. Wijzigingen worden opgeslagen in een lokale database. Nadat het apparaat weer online is, worden de wijzigingen gesynchroniseerd met de externe back-end.

Als dit je eerste ervaring met mobiele apps is, moet je eerst de zelfstudie [Een iOS-app maken]voltooien. Als u het gedownloade quick-start serverproject niet gebruikt, moet u de uitbreidingspakketten voor gegevenstoegang aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie over serverextensiepakketten.

Zie [Offline gegevenssynchroniseren in mobiele apps]voor meer informatie over de functie offline synchronisatie.

## <a name="review-the-client-sync-code"></a><a name="review-sync"></a>De synchronisatiecode van de client controleren
Het clientproject dat u hebt gedownload voor de zelfstudie [Van een iOS-app maken] bevat al code die offlinesynchronisatie ondersteunt met behulp van een lokale core-gegevensgebaseerde database. In deze sectie wordt samengevat wat er al in de zelfstudiecode is opgenomen. Zie [Offline gegevenssynchroniseren in mobiele apps voor]een conceptueel overzicht van de functie.

Met behulp van de offline functie voor het synchroniseren van gegevens van Mobiele Apps kunnen eindgebruikers communiceren met een lokale database, zelfs wanneer het netwerk niet toegankelijk is. Als u deze functies in uw app wilt `MSClient` gebruiken, initialiseert u de synchronisatiecontext van en verwijst u naar een lokale winkel. Vervolgens verwijst u naar uw tabel via de **MSSyncTable-interface.**

Merk in **QSTodoService.m** (Objective-C) of **ToDoTableViewController.swift** (Swift) op dat het type van de **lidsyncTable** **MSSyncTable**is. Offline synchronisatie maakt gebruik van deze synchronisatietabelinterface in plaats van **MSTable**. Wanneer een synchronisatietabel wordt gebruikt, gaan alle bewerkingen naar de lokale winkel en worden ze alleen gesynchroniseerd met de externe back-end met expliciete push- en pull-bewerkingen.

 Als u een verwijzing naar een synchronisatietabel wilt `MSClient`krijgen, gebruikt u de methode **syncTableWithName** op . Als u offline synchronisatiefunctionaliteit wilt verwijderen, gebruikt u **tabelMetName** in plaats daarvan.

Voordat tabelbewerkingen kunnen worden uitgevoerd, moet de lokale winkel worden geïnitialiseerd. Hier is de relevante code:

* **Doelstelling-C**. In de **QSTodoService.init-methode:**

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift.** Ga als eerste naar de methode **ToDoTableViewController.viewDidLoad:**

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Met deze methode wordt een `MSCoreDataStore` lokale winkel gemaakt met behulp van de interface die de Mobile Apps SDK biedt. U ook een andere lokale winkel `MSSyncContextDataSource` bieden door het protocol te implementeren. Ook wordt de eerste parameter van **MSSyncContext** gebruikt om een conflicthandler op te geven. Omdat we `nil`zijn geslaagd, krijgen we de standaard conflict handler, die niet op elk conflict.

Laten we nu de werkelijke synchronisatiebewerking uitvoeren en gegevens ophalen van de externe back-end:

* **Doelstelling-C**. `syncData`duwt eerst nieuwe wijzigingen en vervolgens roept **pullData** om gegevens te krijgen van de externe back-end. De **pullData-methode** krijgt op zijn beurt nieuwe gegevens die overeenkomen met een query:

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

In de Objective-C-versie, in `syncData`, bellen we eerst **pushWithCompletion** op de sync context. Deze methode is `MSSyncContext` lid van (en niet de synchronisatietabel zelf) omdat deze wijzigingen in alle tabellen pusht. Alleen records die op een bepaalde manier lokaal zijn gewijzigd (via CUD-bewerkingen) worden naar de server verzonden. Vervolgens wordt de helper **pullData** aangeroepen, die **MSSyncTable.pullWithCall** aanroept om externe gegevens op te halen en op te slaan in de lokale database.

In de Swift-versie, omdat de push-operatie was niet strikt noodzakelijk, is er geen oproep tot **pushWithCompletion**. Als er wijzigingen in behandeling zijn in de synchronisatiecontext voor de tabel die een pushbewerking doet, geeft u altijd eerst een push uit. Als u echter meer dan één synchronisatietabel hebt, u het beste expliciet pushbellen om ervoor te zorgen dat alles consistent is tussen gerelateerde tabellen.

In zowel de Objective-C- als swift-versies u de **methode pullWithQuery** gebruiken om een query op te geven om de records te filteren die u wilt ophalen. In dit voorbeeld haalt de query alle `TodoItem` records in de externe tabel op.

De tweede parameter van **pullWithQuery** is een query-id die wordt gebruikt voor *incrementele synchronisatie.* Incrementele synchronisatie haalt alleen records op die sinds de `UpdatedAt` laatste synchronisatie `updatedAt` zijn gewijzigd, met behulp van de tijdstempel van de record (in de lokale winkel genoemd.) De query-id moet een beschrijvende tekenreeks zijn die uniek is voor elke logische query in uw app. Als u zich wilt afmelden voor incrementele synchronisatie, geeft u de `nil` query-id door. Deze aanpak kan mogelijk inefficiënt zijn, omdat het alle records ophaalt op elke pull-bewerking.

De Objective-C-app synchroniseert wanneer u gegevens wijzigt of toevoegt, wanneer een gebruiker de vernieuwingsbeweging uitvoert en bij de lancering.

De Swift-app synchroniseert wanneer de gebruiker de vernieuwingsbeweging uitvoert en bij de lancering.

Omdat de app synchroniseert wanneer gegevens worden gewijzigd (Objective-C) of wanneer de app wordt gestart (Objective-C en Swift), gaat de app ervan uit dat de gebruiker online is. In een later gedeelte wordt de app bijgewerkt, zodat gebruikers ook offline kunnen bewerken.

## <a name="review-the-core-data-model"></a><a name="review-core-data"></a>Bekijk het Core Data-model
Wanneer u het offlinearchief Core Data gebruikt, moet u bepaalde tabellen en velden in uw gegevensmodel definiëren. De voorbeeld-app bevat al een gegevensmodel met de juiste indeling. In deze sectie lopen we door deze tabellen om te laten zien hoe ze worden gebruikt.

Open **QSDataModel.xcdatamodeld**. Vier tabellen worden gedefinieerd - drie die worden gebruikt door de SDK en een die wordt gebruikt voor de to-do items zelf:
  * MS_TableOperations: houdt de items bij die moeten worden gesynchroniseerd met de server.
  * MS_TableOperationErrors: houdt eventuele fouten bij die zich voordoen tijdens offline synchronisatie.
  * MS_TableConfig: houdt de laatste bijgewerkte tijd bij voor de laatste synchronisatiebewerking voor alle pull-bewerkingen.
  * TodoItem: slaat de to-do-items op. De systeemkolommen **die zijn gemaaktBij**, **updatedAt**en **versie** zijn optionele systeemeigenschappen.

> [!NOTE]
> De SDK van Mobile Apps reserveert kolomnamen die beginnen met "**``**". Gebruik dit voorvoegsel niet met iets anders dan systeemkolommen. Anders worden uw kolomnamen gewijzigd wanneer u de externe back-end gebruikt.
>
>

Wanneer u de functie offline synchronisatie gebruikt, definieert u de drie systeemtabellen en de gegevenstabel.

### <a name="system-tables"></a>Systeemtabellen

**MS_TableOperations**  

![MS_TableOperations tabelkenmerken][defining-core-data-tableoperations-entity]

| Kenmerk | Type |
| --- | --- |
| id | Geheel getal 64 |
| itemId | Tekenreeks |
| properties | Binaire gegevens |
| tabel | Tekenreeks |
| tableKind | Geheel getal 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors tabelkenmerken][defining-core-data-tableoperationerrors-entity]

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
| Keytype |Geheel getal 64 |
| tabel |Tekenreeks |
| waarde |Tekenreeks |

### <a name="data-table"></a>Gegevenstabel

**TodoItem**

| Kenmerk | Type | Opmerking |
| --- | --- | --- |
| id | Tekenreeks, gemarkeerd vereist |Primaire sleutel in externe winkel |
| voltooid | Booleaans | Veld Item to-do |
| tekst |Tekenreeks |Veld Item to-do |
| gemaaktOp | Date | (facultatief) Kaarten naar **gemaaktBij** systeemeigenschap |
| updatedAt updatedAt updatedAt updatedAt | Date | (facultatief) Kaarten naar **updatedBij** systeemeigenschap |
| versie | Tekenreeks | (facultatief) Wordt gebruikt om conflicten te detecteren, kaarten naar versie |

## <a name="change-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>Het synchronisatiegedrag van de app wijzigen
In deze sectie wijzigt u de app zodat deze niet wordt gesynchroniseerd op het begin van de app of wanneer u items invoegt en bijwerkt. Het synchroniseert alleen wanneer de knop gebaar vernieuwen wordt uitgevoerd.

**Doelstelling-C**:

1. Wijzig in **QSTodoListViewController.m**de **methode viewDidLoad** `[self refresh]` om de aanroep aan het einde van de methode te verwijderen. Nu worden de gegevens niet gesynchroniseerd met de server bij het starten van de app. In plaats daarvan wordt het gesynchroniseerd met de inhoud van de lokale winkel.
2. Wijzig in **QSTodoService.m**de `addItem` definitie van zodat het niet synchroniseert nadat het item is ingevoegd. Verwijder `self syncData` het blok en vervang het door het volgende:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Wijzig de `completeItem` definitie van zoals eerder vermeld. Verwijder het `self syncData` blok voor en vervang het door het volgende:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

In `viewDidLoad`, in **ToDoTableViewController.swift**, commentaar uit de twee regels hier weergegeven, om te stoppen met synchroniseren op app start. Op het moment van schrijven werkt de Swift Todo-app de service niet bij wanneer iemand een item toevoegt of voltooit. Het werkt de service alleen bij bij het starten van apps.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-the-app"></a><a name="test-app"></a>De app testen
In deze sectie maakt u verbinding met een ongeldige URL om een offline scenario te simuleren. Wanneer u gegevensitems toevoegt, worden ze bewaard in het lokale Core Data-archief, maar worden ze niet gesynchroniseerd met de back-end van de mobiele app.

1. Wijzig de URL van de mobiele app in **QSTodoService.m** in een ongeldige URL en voer de app opnieuw uit:

   **Doelstelling-C**. In QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift.** In ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Voeg enkele to-do-items toe. Sluit de simulator af (of sluit de app met geweld) en start deze opnieuw op. Controleer of uw wijzigingen blijven bestaan.

3. Bekijk de inhoud van de externe **TodoItem-tabel:**
   * Ga voor een back-end van Node.js naar de [Azure-portal](https://portal.azure.com/) en klik in uw back-end van uw mobiele app op **Easy Tables** > **TodoItem**.  
   * Gebruik voor een .NET-back-end een SQL-tool, zoals SQL Server Management Studio, of een REST-client, zoals Fiddler of Postman.  

4. Controleer of de nieuwe items *niet* zijn gesynchroniseerd met de server.

5. Wijzig de URL terug naar de juiste url in **QSTodoService.m**en voer de app opnieuw uit.

6. Voer de vernieuwingsbeweging uit door de lijst met items omlaag te halen.  
Er wordt een voortgangsspinner weergegeven.

7. Bekijk de **TodoItem-gegevens** opnieuw. De nieuwe en gewijzigde to-do-items moeten nu worden weergegeven.

## <a name="summary"></a>Samenvatting
Om de offline synchronisatiefunctie te `MSSyncTable` ondersteunen, `MSClient.syncContext` hebben we de interface gebruikt en geinitialiseerd met een lokale winkel. In dit geval was de lokale winkel een core data-gebaseerde database.

Wanneer u een lokaal archief van Core Data gebruikt, moet u verschillende tabellen definiëren met de [juiste systeemeigenschappen.](#review-core-data)

De normale CRUD-bewerkingen voor mobiele apps maken, lezen, bijwerken en verwijderen werken alsof de app nog steeds is verbonden, maar alle bewerkingen vinden plaats in het kader van de lokale winkel.

Toen we de lokale winkel synchroniseerden met de server, gebruikten we de **METHODE MSSyncTable.pullWith.**

## <a name="additional-resources"></a>Aanvullende bronnen
* [Offline gegevens synchroniseren in mobiele apps]
* [Cloud Cover: offline synchronisatie in Azure Mobile Services] \(De video gaat over mobiele services, maar offline synchronisatie van mobiele apps werkt op een vergelijkbare manier.\)

<!-- URLs. -->


[Een iOS-app maken]: app-service-mobile-ios-get-started.md
[Offline gegevens synchroniseren in mobiele apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline synchroniseren in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
