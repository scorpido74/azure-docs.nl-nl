---
title: Inleiding tot betrouw bare verzamelingen
description: Service Fabric stateful services bieden betrouw bare verzamelingen waarmee u Maxi maal beschik bare Cloud toepassingen kunt schrijven die Maxi maal beschikbaar zijn.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 78ecc57a4da43bf416839226253e6d0e2f4c1651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398434"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Inleiding tot betrouw bare verzamelingen in azure Service Fabric stateful Services

Met betrouw bare verzamelingen kunt u met hoge beschik bare, schaal bare en lage latentie Cloud toepassingen schrijven alsof u toepassingen met één computer schrijft. De klassen in de naam ruimte **micro soft. ServiceFabric. data. Collections** bieden een set verzamelingen die uw status automatisch Maxi maal beschikbaar maken. Ontwikkel aars moeten alleen Program meren met de betrouw bare verzamelings-Api's en kunnen beschikken over betrouw bare verzamelingen die de gerepliceerde en lokale status beheren.

Het belangrijkste verschil tussen betrouw bare verzamelingen en andere technologieën voor hoge Beschik baarheid (zoals redis, Azure Table service en Azure Queue-service) is dat de status lokaal in het service-exemplaar wordt bewaard en ook Maxi maal beschikbaar wordt gemaakt. Dit betekent dat:

* Alle Lees bewerkingen zijn lokaal, wat resulteert in Lees bewerkingen met lage latentie en hoge door voer.
* Bij alle schrijf bewerkingen wordt het minimale aantal netwerk-IOs-apparaten in rekening gebracht, wat leidt tot lage latentie en schrijf bewerkingen met hoge door voer.

![Afbeelding van de ontwikkeling van verzamelingen.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Betrouw bare verzamelingen kunnen worden beschouwd als de natuurlijke evolutie van de klassen **System. Collections** : een nieuwe set verzamelingen die zijn ontworpen voor de Cloud en toepassingen voor meerdere computers zonder de complexiteit van de ontwikkelaar te verg Roten. De betrouw bare verzamelingen zijn als volgt:

* Gerepliceerd: status wijzigingen worden gerepliceerd voor hoge Beschik baarheid.
* Asynchroon: Api's zijn asynchroon om ervoor te zorgen dat threads niet worden geblokkeerd wanneer er IO wordt gemaakt.
* Transactioneel: Api's gebruiken de abstractie van trans acties, zodat u meerdere betrouw bare verzamelingen binnen een service eenvoudig kunt beheren.
* Persistent of vluchtig: gegevens kunnen op schijf worden bewaard voor duurzaamheid tegen grootschalige storingen (bijvoorbeeld een stroom storing in een Data Center). Sommige betrouw bare verzamelingen bieden ook ondersteuning voor een veranderlijke modus (met [aanvullende opmerkingen](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)) waarbij alle gegevens in het geheugen worden bewaard, zoals een gerepliceerde in-memory cache.

Betrouw bare verzamelingen bieden sterke consistentie garanties van de doos om redenen in de status van toepassingen gemakkelijker te maken.
Sterke consistentie wordt bereikt door trans acties alleen te volt ooien nadat de volledige trans actie is geregistreerd op een meerderheid van de replica's, inclusief de primaire.
Om ervoor te zorgen dat de consistentie zwakkerer is, kunnen toepassingen terugsturen naar de client/aanvrager voordat de asynchrone door Voer retourneert.

De betrouw bare verzamelingen-Api's zijn een evolutie van gelijktijdige verzamelingen Api's (gevonden in de naam ruimte **System. Collections. gelijktijdige** ):

* Asynchroon: retourneert een taak omdat de bewerkingen, in tegens telling tot gelijktijdige verzamelingen, worden gerepliceerd en bewaard.
* Geen para meters: `ConditionalValue<T>` gebruikt om een `bool` en een waarde te retour neren in plaats van out-para meters. `ConditionalValue<T>`is net `Nullable<T>` als geen struct vereist.
* Trans acties: een transactie object wordt gebruikt om de gebruiker in staat te stellen acties op meerdere betrouw bare verzamelingen in een trans actie te groeperen.

Vandaag, **micro soft. ServiceFabric. data. Collections** bevat drie verzamelingen:

* [Betrouw bare woorden lijst](https://msdn.microsoft.com/library/azure/dn971511.aspx): vertegenwoordigt een gerepliceerde, transactionele en asynchrone verzameling sleutel-waardeparen. Net als bij **ConcurrentDictionary**kunnen zowel de sleutel als de waarde van elk type zijn.
* [Betrouw bare wachtrij](https://msdn.microsoft.com/library/azure/dn971527.aspx): vertegenwoordigt een FIFO-wachtrij (een gerepliceerde, transactionele en asynchrone strikte first-out). Net als bij **ConcurrentQueue**kan de waarde van elk type zijn.
* [Betrouw bare gelijktijdige wachtrij](service-fabric-reliable-services-reliable-concurrent-queue.md): vertegenwoordigt een gerepliceerde, transactionele en asynchrone bestel wachtrij voor Best effort voor hoge door voer. Net als bij de **ConcurrentQueue**kan de waarde van elk type zijn.

## <a name="next-steps"></a>Volgende stappen

* [Betrouw bare verzamelings richtlijnen & aanbevelingen](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Trans acties en vergren delingen](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gegevens beheren
  * [Back-ups maken en herstellen](service-fabric-reliable-services-backup-restore.md)
  * [Meldingen](service-fabric-reliable-services-notifications.md)
  * [Betrouwbare verzamelingserialisatie](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialisatie en upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Configuratie van betrouw bare status Manager](service-fabric-reliable-services-configuration.md)
* Andere
  * [Snelstartgids Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Naslag informatie voor ontwikkel aars voor betrouw bare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
