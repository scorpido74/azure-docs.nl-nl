---
title: Richtlijnen voor betrouwbare collecties
description: Richtlijnen en aanbevelingen voor het gebruik van betrouwbare servicesverzamelingen in een Azure Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: db37067069b2a9eb08009eb6bb373f6fce1cafa9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398527"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Richtlijnen en aanbevelingen voor betrouwbare verzamelingen in Azure Service Fabric
In deze sectie vindt u richtlijnen voor het gebruik van Reliable State Manager en Betrouwbare Collecties. Het doel is om gebruikers te helpen voorkomen dat veelvoorkomende valkuilen.

De richtlijnen zijn georganiseerd als eenvoudige aanbevelingen vooraf vastgesteld met de termen *Do*, *Consider*, *Avoid* and *Do not*.

* Wijzig een object van aangepast type dat wordt `TryPeekAsync` geretourneerd `TryGetValueAsync`door leesbewerkingen (bijvoorbeeld of ). Betrouwbare verzamelingen geven, net als Gelijktijdige verzamelingen, een verwijzing naar de objecten en niet een kopie.
* Kopieer het geretourneerde object van een aangepast type voordat u het wijzigt. Aangezien structen en ingebouwde typen door gegeven worden, hoeft u er geen diepe kopie op te maken, tenzij ze referentievelden of eigenschappen bevatten die u wilt wijzigen.
* Niet gebruiken `TimeSpan.MaxValue` voor time-outs. Time-outs moeten worden gebruikt om impasses op te sporen.
* Gebruik een transactie niet nadat deze is vastgelegd, afgebroken of verwijderd.
* Gebruik geen opsomming buiten de transactiescope waarin het is gemaakt.
* Maak geen transactie binnen de `using` verklaring van een andere transactie omdat dit impasses kan veroorzaken.
* Maak geen betrouwbare `IReliableStateManager.GetOrAddAsync` status met en gebruik de betrouwbare status in dezelfde transactie. Dit resulteert in een InvalidOperationException.
* Zorg ervoor `IComparable<TKey>` dat uw implementatie correct is. Het systeem is `IComparable<TKey>` afhankelijk van voor het samenvoegen van controlepunten en rijen.
* Gebruik Update-vergrendeling bij het lezen van een item met de bedoeling om het bij te werken om een bepaalde klasse van impasses te voorkomen.
* Overweeg om het aantal betrouwbare verzamelingen per partitie op minder dan 1000 te houden. Geef de voorkeur aan betrouwbare collecties met meer items dan meer betrouwbare collecties met minder items.
* Overweeg om uw items (bijvoorbeeld TKey + TValue for Reliable Dictionary) onder de 80 KBytes te houden: hoe kleiner hoe beter. Dit vermindert de hoeveelheid large object heap-gebruik en de IO-vereisten voor schijf en netwerk. Vaak vermindert het repliceren van dubbele gegevens wanneer slechts een klein deel van de waarde wordt bijgewerkt. Veel voorkomende manier om dit te bereiken in Reliable Dictionary, is om uw rijen te breken in meerdere rijen.
* Overweeg back-up- en herstelfunctionaliteit te gebruiken om herstel na noodgevallen te hebben.
* Vermijd het mengen van bewerkingen van één `GetCountAsync` `CreateEnumerableAsync`entiteit en bewerkingen met meerdere entiteiten (bijvoorbeeld ) in dezelfde transactie vanwege de verschillende isolatieniveaus.
* Doe afhandelen InvalidOperationException. Gebruikerstransacties kunnen om verschillende redenen door het systeem worden afgebroken. Bijvoorbeeld wanneer de Reliable State Manager zijn rol uit primair verandert of wanneer een langlopende transactie de afgekapte transactievan het transactionele logboek blokkeert. In dergelijke gevallen kan de gebruiker InvalidOperationException ontvangen, wat aangeeft dat zijn transactie al is beëindigd. Ervan uitgaande dat de beëindiging van de transactie niet door de gebruiker is aangevraagd, is de beste manier om deze uitzondering af te handelen door de transactie af te handelen, te controleren of het annuleringstoken is gesignaleerd (of de rol van de replica is gewijzigd) en zo niet een nieuwe transactie te maken en opnieuw te proberen.  

Hier zijn een aantal dingen om in gedachten te houden:

* De standaardtime-out is vier seconden voor alle RELIABLE Collection API's. De meeste gebruikers moeten de standaard time-out gebruiken.
* Het standaard annuleringstoken bevindt zich `CancellationToken.None` in alle API's voor betrouwbare verzamelingen.
* De parameter key type *(TKey)* voor een `GetHashCode()` `Equals()`betrouwbaar woordenboek moet correct implementeren en . Sleutels moeten onveranderlijk zijn.
* Om een hoge beschikbaarheid voor de betrouwbare verzamelingen te bereiken, moet elke service ten minste een doel- en minimale replicasetgrootte van 3 hebben.
* Lees bewerkingen op de secundaire kan lezen versies die niet quorum gepleegd.
  Dit betekent dat een versie van gegevens die wordt gelezen van een enkele secundaire kan worden valse vooruitgang.
  Leest van Primary zijn altijd stabiel: kan nooit worden valse vooruitgang geboekt.
* Beveiliging/privacy van de gegevens die door uw toepassing in een betrouwbare verzameling worden gehandhaafd, is uw beslissing en onderworpen aan de bescherming die door uw opslagbeheer wordt geboden; D.w.z. Schijfversleuteling van het besturingssysteem kan worden gebruikt om uw gegevens in rust te beschermen.  

## <a name="volatile-reliable-collections"></a>Vluchtige betrouwbare collecties
Wanneer u besluit vluchtige betrouwbare verzamelingen te gebruiken, moet u rekening houden met het volgende:

* ```ReliableDictionary```heeft volatiele ondersteuning
* ```ReliableQueue```heeft volatiele ondersteuning
* ```ReliableConcurrentQueue```heeft GEEN vluchtige ondersteuning
* Aanhoudende diensten kunnen niet volatiel worden gemaakt. Als ```HasPersistedState``` u ```false``` de vlag wijzigt, moet u de hele service opnieuw maken.
* Vluchtige diensten kunnen niet worden gemaakt volgehouden. Als ```HasPersistedState``` u ```true``` de vlag wijzigt, moet u de hele service opnieuw maken.
* ```HasPersistedState```is een service level config. Dit betekent dat **ALLE** collecties blijven bestaan of volatiel zijn. U vluchtige en volhoudende verzamelingen niet mengen
* Quorumverlies van een vluchtige partitie resulteert in volledig gegevensverlies
* Back-up en herstel is NIET beschikbaar voor vluchtige services

## <a name="next-steps"></a>Volgende stappen
* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Transacties en vergrendelingen](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gegevens beheren
  * [Back-up maken en herstellen](service-fabric-reliable-services-backup-restore.md)
  * [Meldingen](service-fabric-reliable-services-notifications.md)
  * [Serialisatie en upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Betrouwbare configuratie van State Manager](service-fabric-reliable-services-configuration.md)
* Andere
  * [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start.md)
  * [Ontwikkelaarsreferentie voor betrouwbare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
