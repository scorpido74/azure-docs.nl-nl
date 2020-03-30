---
title: Offline gegevens synchroniseren
description: Conceptueel naslagwerk en overzicht van de functie offline gegevenssynchronisatie voor Azure Mobile Apps
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458746"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Offlinesynchronisatie van gegevens in Azure Mobile Apps

## <a name="what-is-offline-data-sync"></a>Wat is offline datasync?
Offline gegevenssynchronisatie is een SDK-functie voor clients en servers van Azure Mobile Apps, waarmee ontwikkelaars eenvoudig apps kunnen maken die functioneel zijn zonder netwerkverbinding.

Wanneer uw app in de offlinemodus staat, u nog steeds gegevens maken en wijzigen die worden opgeslagen in een lokale winkel. Wanneer de app weer online is, kan deze lokale wijzigingen synchroniseren met de back-end van uw Azure Mobile App.When the app is backend, it can synchronize local changes with your Azure Mobile App backend. De functie bevat ook ondersteuning voor het detecteren van conflicten wanneer dezelfde record wordt gewijzigd op zowel de client als de backend. Conflicten kunnen vervolgens worden afgehandeld op de server of de client.

Offline synchronisatie heeft verschillende voordelen:

* De reactiesnelheid van de app verbeteren door servergegevens lokaal op het apparaat te plaatsen
* Robuuste apps maken die nuttig blijven als er netwerkproblemen zijn
* Eindgebruikers toestaan om gegevens te maken en aan te passen, zelfs wanneer er geen netwerktoegang is, waardoor scenario's met weinig of geen connectiviteit worden ondersteund
* Gegevens synchroniseren op meerdere apparaten en conflicten detecteren wanneer dezelfde record door twee apparaten wordt gewijzigd
* Netwerkgebruik beperken op netwerken met hoge latentie of netwerken met een datalimiet

In de volgende zelfstudies wordt uitgelegd hoe u offline synchronisatie toevoegt aan uw mobiele clients met Azure Mobile Apps:

* [Android: offline synchronisatie inschakelen]
* [Apache Cordova: Offline synchronisatie inschakelen](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: offline synchronisatie inschakelen]
* [Xamarin iOS: Offline synchronisatie inschakelen]
* [Xamarin Android: Offline synchronisatie inschakelen]
* [Xamarin.Forms: Offline synchronisatie inschakelen](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Universeel Windows-platform: offline synchronisatie inschakelen]

## <a name="what-is-a-sync-table"></a>Wat is een synchronisatietabel?
Om toegang te krijgen tot het eindpunt "/tabellen", bieden de `IMobileServiceTable` SDK's van `MSTable` de Azure Mobile-client interfaces zoals (.NET-client SDK) of (iOS-client). Deze API's maken rechtstreeks verbinding met de back-end van de Azure Mobile App en mislukken als het clientapparaat geen netwerkverbinding heeft.

Als u offline gebruik wilt ondersteunen, moet uw app `IMobileServiceSyncTable` in plaats daarvan `MSSyncTable` de API's van de *synchronisatietabel* gebruiken, zoals (.NET-client SDK) of (iOS-client). Alle zelfde CRUD-bewerkingen (Maken, Lezen, Bijwerken, Verwijderen) werken tegen de API's van de synchronisatietabel, behalve nu ze lezen van of schrijven naar een *lokale winkel.* Voordat synchronisatietabelbewerkingen kunnen worden uitgevoerd, moet de lokale winkel worden geïnitialiseerd.

## <a name="what-is-a-local-store"></a>Wat is een lokale winkel?
Een lokale winkel is de gegevenspersistentielaag op het clientapparaat. De Azure Mobile Apps-client SDK's bieden een standaard implementatie van lokale opslag. Op Windows, Xamarin en Android, het is gebaseerd op SQLite. Op iOS is het gebaseerd op Core Data.

Als u de implementatie op basis van SQLite wilt gebruiken op Windows Phone of Microsoft Store, moet u een SQLite-extensie installeren. Zie [Universal Windows Platform: Offline synchronisatie inschakelen]voor meer informatie. Android en iOS worden geleverd met een versie van SQLite in het besturingssysteem zelf, dus het is niet nodig om naar uw eigen versie van SQLite te verwijzen.

Ontwikkelaars kunnen ook hun eigen lokale winkel implementeren. Als u bijvoorbeeld gegevens in een versleutelde indeling op de mobiele client wilt opslaan, u een lokale winkel definiëren die SQLCipher gebruikt voor versleuteling.

## <a name="what-is-a-sync-context"></a>Wat is een synchronisatiecontext?
Een *synchronisatiecontext* is gekoppeld aan een `IMobileServiceClient` mobiel `MSClient`clientobject (zoals of ) en houdt wijzigingen bij die zijn aangebracht met synchronisatietabellen. De synchronisatiecontext houdt een *bewerkingswachtrij*bij, die een geordende lijst met CUD-bewerkingen bijhoudt (Maken, Bijwerken, Verwijderen) die later naar de server wordt verzonden.

Een lokale winkel is gekoppeld aan de synchronisatiecontext `IMobileServicesSyncContext.InitializeAsync(localstore)` met behulp van een initialiserenmethode zoals in de [SDK .NET-client].

## <a name="how-offline-synchronization-works"></a><a name="how-sync-works"></a>Hoe offline synchronisatie werkt
Wanneer u synchronisatietabellen gebruikt, bepaalt uw clientcode wanneer lokale wijzigingen worden gesynchroniseerd met een back-end van Azure Mobile App. Niets wordt verzonden naar de backend totdat er een oproep om lokale veranderingen te *duwen.* Op dezelfde manier wordt de lokale winkel alleen gevuld met nieuwe gegevens wanneer er een oproep is om gegevens op te *halen.*

* **Push:** Push is een bewerking op de synchronisatiecontext en stuurt alle CUD-wijzigingen sinds de laatste push. Houd er rekening mee dat het niet mogelijk is om alleen de wijzigingen van een afzonderlijke tabel te verzenden, omdat anders bewerkingen buiten de orde kunnen worden verzonden. Push voert een reeks REST-oproepen uit naar uw Azure Mobile App-backend, die op zijn beurt uw serverdatabase wijzigt.
* **Pull:** Pull wordt uitgevoerd op basis van één tabel en kan worden aangepast met een query om alleen een subset van de servergegevens op te halen. De Azure Mobile-client-SDK's voegen de resulterende gegevens vervolgens in de lokale winkel in.
* **Impliciete pushes:** Als een pull wordt uitgevoerd tegen een tabel die in `push()` behandeling is met lokale updates, voert de pull eerst een uit op de synchronisatiecontext. Met deze push u conflicten tussen wijzigingen die al in de wachtrij staan en nieuwe gegevens van de server minimaliseren.
* **Incrementele synchronisatie:** de eerste parameter voor de pull-bewerking is een *querynaam* die alleen op de client wordt gebruikt. Als u een niet-null-querynaam gebruikt, voert de Azure Mobile SDK een *incrementele synchronisatie uit.* Elke keer dat een pull-bewerking een `updatedAt` set resultaten retourneert, wordt de nieuwste tijdstempel van die resultaatset opgeslagen in de lokale sdk-systeemtabellen. Latere pull-bewerkingen halen alleen records op na die tijdstempel.

  Als u incrementele synchronisatie wilt `updatedAt` gebruiken, moet uw server zinvolle waarden retourneren en moet het sorteren op dit veld ook worden ondersteund. Omdat de SDK echter een eigen sortering toevoegt aan het veld updatedAt, u geen pull-query gebruiken met een eigen `orderBy` component.

  De querynaam kan elke tekenreeks zijn die u kiest, maar deze moet uniek zijn voor elke logische query in uw app.
  Anders kunnen verschillende pull-bewerkingen dezelfde incrementele synchronisatietijdstempel overschrijven en kunnen uw query's onjuiste resultaten retourneren.

  Als de query een parameter heeft, u een unieke querynaam maken door de parameterwaarde op te nemen.
  Als u bijvoorbeeld filtert op userid, kan uw querynaam als volgt zijn (in C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Als u zich wilt afmelden voor `null` incrementele synchronisatie, geeft u de query-id door. In dit geval worden alle records opgehaald `PullAsync`bij elke oproep naar , wat mogelijk inefficiënt is.
* **Zuiveren**: U de inhoud van `IMobileServiceSyncTable.PurgeAsync`de lokale winkel wissen met behulp van.
  Herontwerpen kan nodig zijn als u verouderde gegevens in de clientdatabase hebt of als u alle wijzigingen in behandeling wilt verwijderen.

  Een zuivering wist een tafel van de lokale winkel. Als er bewerkingen zijn die wachten op synchronisatie met de serverdatabase, wordt met de zuivering een uitzondering gemaakt, tenzij de *parameter force purge* is ingesteld.

  Stel dat Apparaat1 in het voorbeeld 'todolijst' alleen items ophaalt die niet zijn voltooid. Een todoitem "Melk kopen" wordt gemarkeerd voltooid op de server door een ander apparaat. Apparaat1 heeft echter nog steeds het todoitem 'Melk kopen' in de lokale winkel, omdat het alleen items trekt die niet zijn gemarkeerd. Een zuivering wist dit verouderde item.

## <a name="next-steps"></a>Volgende stappen
* [iOS: offline synchronisatie inschakelen]
* [Xamarin iOS: Offline synchronisatie inschakelen]
* [Xamarin Android: Offline synchronisatie inschakelen]
* [Universeel Windows-platform: offline synchronisatie inschakelen]

<!-- Links -->
[.NET-client SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: offline synchronisatie inschakelen]: app-service-mobile-android-get-started-offline-data.md
[iOS: offline synchronisatie inschakelen]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Offline synchronisatie inschakelen]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Offline synchronisatie inschakelen]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Universeel Windows-platform: offline synchronisatie inschakelen]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
