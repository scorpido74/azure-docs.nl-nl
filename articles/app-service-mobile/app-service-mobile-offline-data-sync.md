---
title: Offline gegevens synchronisatie
description: Conceptuele Naslag informatie en overzicht van de functie voor offline gegevens synchronisatie voor Azure Mobile Apps
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 9238ebd06a4aa532d20a2a98499963a75780f025
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668416"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Offlinegegevens synchroniseren in Azure Mobile Apps

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="what-is-offline-data-sync"></a>Wat is offline gegevens synchronisatie?
Offline gegevens synchronisatie is een functie voor client-en Server-SDK van Azure Mobile Apps waarmee ontwikkel aars eenvoudig apps kunnen maken die zonder een netwerk verbinding werken.

Als uw app zich in de offline modus bevindt, kunt u nog steeds gegevens maken en wijzigen, die worden opgeslagen in een lokaal archief. Wanneer de app weer online is, kunnen lokale wijzigingen worden gesynchroniseerd met de back-end van de mobiele app van Azure. De functie biedt ook ondersteuning voor het detecteren van conflicten wanneer dezelfde record op zowel de client als de back-end wordt gewijzigd. Conflicten kunnen vervolgens worden verwerkt op de server of de client.

Offline synchronisatie heeft verschillende voor delen:

* De reactie snelheid van de app verbeteren door Server gegevens lokaal op het apparaat in de cache te plaatsen
* Maak robuuste apps die handig blijven wanneer er netwerk problemen zijn
* Eind gebruikers toestaan om gegevens te maken en te wijzigen, zelfs wanneer er geen netwerk toegang is, ondersteunings scenario's met weinig of geen connectiviteit
* Gegevens synchroniseren op meerdere apparaten en conflicten detecteren wanneer dezelfde record door twee apparaten wordt gewijzigd
* Netwerk gebruik beperken voor netwerken met een hoge latentie of een Data limiet

De volgende zelf studies laten zien hoe u offline synchronisatie kunt toevoegen aan uw mobiele clients met behulp van Azure Mobile Apps:

* [Android: offline synchronisatie inschakelen]
* [Apache Cordova: offline synchronisatie inschakelen](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: offline synchronisatie inschakelen]
* [Xamarin iOS: offline synchronisatie inschakelen]
* [Xamarin Android: offline synchronisatie inschakelen]
* [Xamarin. Forms: offline synchronisatie inschakelen](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Universeel Windows-platform: offline synchronisatie inschakelen]

## <a name="what-is-a-sync-table"></a>Wat is een synchronisatie tabel?
Voor toegang tot het eind punt "/Tables" bieden de Azure Mobile client-Sdk's interfaces als `IMobileServiceTable` (.NET client SDK) of `MSTable` (iOS-client). Deze Api's maken rechtstreeks verbinding met de back-end van de mobiele app van Azure en mislukken als het client apparaat geen netwerk verbinding heeft.

Ter ondersteuning van offline gebruik moet uw app in plaats daarvan gebruikmaken van de *synchronisatie tabel* api's, zoals `IMobileServiceSyncTable` (.net client SDK) of `MSSyncTable` (Ios-client). Alle dezelfde ruwe bewerkingen (maken, lezen, bijwerken, verwijderen) werken op basis van synchronisatie tabel-Api's, behalve nu ze lezen van of schrijven naar een *lokale opslag*. Voordat de bewerkingen van de synchronisatie tabel kunnen worden uitgevoerd, moet het lokale archief worden geïnitialiseerd.

## <a name="what-is-a-local-store"></a>Wat is een lokaal archief?
Een lokaal archief is de laag voor gegevens persistentie op het client apparaat. De Sdk's van de Azure Mobile Apps-client bieden een standaard implementatie van lokale opslag. In Windows, Xamarin en Android is gebaseerd op SQLite. Op iOS is gebaseerd op kern gegevens.

Als u de op SQLite gebaseerde implementatie wilt gebruiken op Windows Phone of Microsoft Store, moet u een SQLite-extensie installeren. Zie [universeel Windows-platform: offline synchronisatie inschakelen]voor meer informatie. Android en iOS worden geleverd met een versie van SQLite in het besturings systeem van het apparaat, dus het is niet nodig om naar uw eigen versie van SQLite te verwijzen.

Ontwikkel aars kunnen ook hun eigen lokale opslag implementeren. Als u bijvoorbeeld gegevens wilt opslaan in een versleutelde indeling op de mobiele client, kunt u een lokaal archief definiëren dat SQLCipher gebruikt voor versleuteling.

## <a name="what-is-a-sync-context"></a>Wat is een synchronisatie context?
Een *synchronisatie context* is gekoppeld aan een mobiel client object (zoals `IMobileServiceClient` of `MSClient`) en houdt wijzigingen bij die zijn aangebracht met synchronisatie tabellen. De synchronisatie context houdt een *bewerkings wachtrij*bij, waarmee een geordende lijst met CUD-bewerkingen (maken, bijwerken, verwijderen) wordt bewaard die later naar de server worden verzonden.

Een lokaal archief wordt gekoppeld aan de synchronisatie context met behulp van een initialisatie methode zoals `IMobileServicesSyncContext.InitializeAsync(localstore)` in de [.net-client-SDK].

## <a name="how-sync-works"></a>Hoe werkt offline synchronisatie?
Wanneer u synchronisatie tabellen gebruikt, bepaalt uw client code wanneer lokale wijzigingen worden gesynchroniseerd met een back-end van een mobiele app van Azure. Er wordt niets verzonden naar de back-end totdat er een aanroep is voor het *pushen* van lokale wijzigingen. Op dezelfde manier wordt het lokale archief alleen met nieuwe gegevens gevuld wanneer er een aanroep is van het *ophalen* van gegevens.

* **Push**: Push is een bewerking op de synchronisatie context en verzendt alle CUD-wijzigingen sinds de laatste push. Houd er rekening mee dat het niet mogelijk is om alleen de wijzigingen van een afzonderlijke tabel te verzenden, omdat andere bewerkingen uit de juiste volg orde zouden kunnen worden verzonden. Push voert een reeks REST-aanroepen uit naar de back-end van uw mobiele app van Azure, die op zijn beurt uw server database wijzigt.
* **Pull**: pull wordt uitgevoerd per tabel en kan worden aangepast met een query om alleen een subset van de server gegevens op te halen. Met de Sdk's van de Azure Mobile client worden de resulterende gegevens vervolgens in het lokale archief ingevoegd.
* **Impliciete pushes**: als er een pull-bewerking wordt uitgevoerd voor een tabel met wachtende lokale updates, voert de pull eerst een `push()` uit op de synchronisatie context. Deze push-opdracht helpt conflicten te minimaliseren tussen wijzigingen die al in de wachtrij staan en nieuwe gegevens van de server.
* **Incrementele synchronisatie**: de eerste para meter voor de pull-bewerking is een *query naam* die alleen op de client wordt gebruikt. Als u een query naam gebruikt die niet gelijk is aan nul, voert de Azure Mobile SDK een *incrementele synchronisatie*uit. Telkens wanneer een pull-bewerking een set resultaten retourneert, wordt de laatste `updatedAt` tijds tempel van die resultatenset opgeslagen in de lokale SDK-systeem tabellen. Bij volgende pull-bewerkingen worden alleen records opgehaald na die tijds tempel.

  Als u een incrementele synchronisatie wilt gebruiken, moet uw server zinvolle `updatedAt` waarden retour neren en moet u ook sorteren op dit veld ondersteunen. Omdat de SDK echter een eigen sortering aan het veld updatedAt toevoegt, kunt u geen pull-query gebruiken die een eigen `orderBy`-component bevat.

  De query naam kan een wille keurige teken reeks zijn die u kiest, maar deze moet uniek zijn voor elke logische query in uw app.
  Anders kunnen verschillende pull-bewerkingen dezelfde incrementele synchronisatie-tijds tempel overschrijven en kunnen uw query's onjuiste resultaten retour neren.

  Als de query een para meter heeft, is een manier om een unieke query naam te maken, het opnemen van de parameter waarde.
  Als u bijvoorbeeld filtert op UserID, kan de naam van de query als volgt C#zijn:

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Als u de incrementele synchronisatie wilt afmelden, geeft u `null` door als query-ID. In dit geval worden alle records opgehaald bij elke aanroep van `PullAsync`. Dit is mogelijk inefficiënt.
* **Opschonen**: u kunt de inhoud van het lokale archief wissen met behulp van `IMobileServiceSyncTable.PurgeAsync`.
  Het kan nodig zijn om te verwijderen als u verouderde gegevens in de client database hebt of als u alle wijzigingen die in behandeling zijn, wilt negeren.

  Met een opschonen wordt een tabel uit het lokale archief gewist. Als er bewerkingen met de Server database wachten, wordt door de opschoning een uitzonde ring gegenereerd tenzij de para meter voor *geforceerd opschonen* is ingesteld.

  Als voor beeld van verouderde gegevens op de client, kunt u in het voor beeld ' taken lijst ' Device1 alleen items ophalen die niet zijn voltooid. Een todoitem ' melk kopen ' is gemarkeerd als voltooid op de server door een ander apparaat. Device1 heeft echter nog steeds de todoitem ' melk kopen ' in het lokale archief omdat het alleen items bevat die niet zijn gemarkeerd als voltooid. Dit verouderde item wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen
* [iOS: offline synchronisatie inschakelen]
* [Xamarin iOS: offline synchronisatie inschakelen]
* [Xamarin Android: offline synchronisatie inschakelen]
* [Universeel Windows-platform: offline synchronisatie inschakelen]

<!-- Links -->
[.NET-client-SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: offline synchronisatie inschakelen]: app-service-mobile-android-get-started-offline-data.md
[iOS: offline synchronisatie inschakelen]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: offline synchronisatie inschakelen]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: offline synchronisatie inschakelen]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Universeel Windows-platform: offline synchronisatie inschakelen]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
