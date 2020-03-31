---
title: Inleiding tot betrouwbare collecties
description: Service Fabric stateful services bieden betrouwbare collecties waarmee u zeer beschikbare, schaalbare en low-latency cloudtoepassingen schrijven.
ms.topic: conceptual
ms.date: 1/3/2019
ms.openlocfilehash: 48fa682f4c017f66911729e1f581f3aa91cdc28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609720"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Inleiding tot betrouwbare verzamelingen in stateful services van Azure Service Fabric

Met betrouwbare verzamelingen u zeer beschikbare, schaalbare en low-latency cloudtoepassingen schrijven alsof u afzonderlijke computertoepassingen schrijft. De klassen in de naamruimte **Microsoft.ServiceFabric.Data.Collections** bieden een reeks verzamelingen die uw status automatisch zeer beschikbaar maken. Ontwikkelaars hoeven alleen te programmeren op de Reliable Collection API's en betrouwbare verzamelingen de gerepliceerde en lokale status te laten beheren.

Het belangrijkste verschil tussen betrouwbare verzamelingen en andere technologieën met hoge beschikbaarheid (zoals Redis, Azure Table-service en Azure Queue-service) is dat de status lokaal in de service-instantie wordt bewaard, terwijl deze ook zeer beschikbaar wordt gesteld. Dit betekent dat:

* Alle reads zijn lokaal, wat resulteert in lage latentie en hoge doorvoer leest.
* Alle schrijfbewerkingen hebben het minimum aantal netwerk-IOs, wat resulteert in een lage latentie en een hoge doorvoer.

![Beeld van evolutie van inzamelingen.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Betrouwbare collecties kunnen worden gezien als de natuurlijke evolutie van de **System.Collections-klassen:** een nieuwe reeks collecties die zijn ontworpen voor de cloud- en multicomputertoepassingen zonder de complexiteit voor de ontwikkelaar te vergroten. Als zodanig zijn betrouwbare collecties:

* Gerepliceerd: statuswijzigingen worden gerepliceerd voor hoge beschikbaarheid.
* Volhard: gegevens blijven bestaan op schijf voor duurzaamheid tegen grootschalige uitval (bijvoorbeeld een stroomuitval in datacenters).
* Omdat schrijft wordt volgehouden en gerepliceerd, u geen vluchtige ReliableDictionary, ReliableQueue of andere betrouwbare verzameling maken die alleen gegevens in het geheugen blijft bestaan.
* Asynchroon: API's zijn asynchroon om ervoor te zorgen dat threads niet worden geblokkeerd bij het oplopen van IO.
* Transactioneel: API's maken gebruik van de abstractie van transacties, zodat u meerdere betrouwbare verzamelingen binnen een service eenvoudig beheren.

Betrouwbare collecties bieden sterke consistentiegaranties uit de doos om het redeneren over toepassingsstatus gemakkelijker te maken.
Sterke consistentie wordt bereikt door ervoor te zorgen dat transactiecommits pas worden voltooid nadat de hele transactie is aangemeld op een meerderheidsquorum van replica's, inclusief de primaire.
Om een zwakkere consistentie te bereiken, kunnen toepassingen de client/aanvrager bevestigen voordat de asynchrone commit retouren.

De API's voor betrouwbare verzamelingen zijn een evolutie van gelijktijdige verzamelingen API's (gevonden in de **naamruimte System.Collections.Concurrent):**

* Asynchrone: geeft als resultaat een taak, omdat de bewerkingen, in tegenstelling tot gelijktijdige verzamelingen, worden gerepliceerd en gehandhaafd.
* Geen uit parameters: `ConditionalValue<T>` Gebruikt `bool` om een en een waarde terug te geven in plaats van uit parameters. `ConditionalValue<T>`is `Nullable<T>` als, maar vereist niet Dat T een struct is.
* Transacties: gebruikt een transactieobject om de gebruiker in staat te stellen acties te groeperen op meerdere betrouwbare verzamelingen in een transactie.

Vandaag bevat **Microsoft.ServiceFabric.Data.Collections** drie verzamelingen:

* [Betrouwbaar woordenboek:](https://msdn.microsoft.com/library/azure/dn971511.aspx)vertegenwoordigt een gerepliceerde, transactionele en asynchrone verzameling sleutel-/waardeparen. Net als **bij ConcurrentDictionary**kunnen zowel de sleutel als de waarde van elk type zijn.
* [Betrouwbare wachtrij:](https://msdn.microsoft.com/library/azure/dn971527.aspx)vertegenwoordigt een gerepliceerde, transactionele en asynchrone strikte first-in-, first-out (FIFO)-wachtrij. Net als **bij ConcurrentQueue**kan de waarde van elk type zijn.
* [Betrouwbare gelijktijdige wachtrij:](service-fabric-reliable-services-reliable-concurrent-queue.md)vertegenwoordigt een gerepliceerde, transactionele en asynchrone wachtrij voor het bestellen van de beste inspanning voor een hoge doorvoer. Net als bij de **ConcurrentQueue**kan de waarde van elk type zijn.

## <a name="next-steps"></a>Volgende stappen

* [Richtlijnen voor betrouwbare verzamelingen & aanbevelingen](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Transacties en vergrendelingen](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gegevens beheren
  * [Back-up en herstel](service-fabric-reliable-services-backup-restore.md)
  * [Meldingen](service-fabric-reliable-services-notifications.md)
  * [Betrouwbare verzamelingserialisatie](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialisatie en upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Betrouwbare configuratie van State Manager](service-fabric-reliable-services-configuration.md)
* Andere
  * [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start.md)
  * [Ontwikkelaarsreferentie voor betrouwbare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
