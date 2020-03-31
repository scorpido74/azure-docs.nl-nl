---
title: Transactie- en vergrendelingsmodi in betrouwbare verzamelingen
description: Azure Service Fabric Reliable State Manager en Betrouwbare incassotransacties en vergrendeling.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938914"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transactie- en vergrendelingsmodi in betrouwbare verzamelingen van Azure Service Fabric

## <a name="transaction"></a>Transactie

Een transactie is een reeks bewerkingen die worden uitgevoerd als één logische eenheid van het werk. Het vertoont de gemeenschappelijke [ACID](https://en.wikipedia.org/wiki/ACID) (*atomiciteit*, *consistentie*, *isolatie*, *duurzaamheid*) eigenschappen van database transacties:

* **Atomiciteit**: Een transactie moet een atoomeenheid van het werk zijn. Met andere woorden, ofwel alle gegevens wijzigingen worden uitgevoerd, of geen van hen wordt uitgevoerd.
* **Consistentie:** Wanneer een transactie is voltooid, moet alle gegevens consistent blijven. Alle interne gegevensstructuren moeten aan het einde van de transactie correct zijn.
* **Isolatie**: Wijzigingen die door gelijktijdige transacties worden aangebracht, moeten worden geïsoleerd van de wijzigingen die door andere gelijktijdige transacties zijn aangebracht. Het isolatieniveau dat wordt gebruikt voor een bewerking binnen een [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) wordt bepaald door de [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) die de bewerking uitvoert.
* **Duurzaamheid**: Nadat een transactie is voltooid, zijn de effecten permanent aanwezig in het systeem. De wijzigingen blijven bestaan, zelfs in het geval van een systeemstoring.

### <a name="isolation-levels"></a>Isolatieniveaus

Isolatieniveau bepaalt de mate waarin de transactie moet worden geïsoleerd van wijzigingen die door andere transacties worden aangebracht.
Er zijn twee isolatieniveaus die worden ondersteund in betrouwbare verzamelingen:

* **Herhaalbaar lezen**: geeft aan dat overzichten geen gegevens kunnen lezen die zijn gewijzigd, maar nog niet zijn vastgelegd door andere transacties en dat geen andere transacties gegevens kunnen wijzigen die door de huidige transactie zijn gelezen totdat de huidige transactie is voltooid.
* **Momentopname:** hiermee geeft u op dat gegevens die door een instructie in een transactie worden gelezen, de transactioneel consistente versie is van de gegevens die aan het begin van de transactie bestonden.
  De transactie kan alleen gegevenswijzigingen herkennen die vóór het begin van de transactie zijn vastgelegd.
  Gegevenswijzigingen die na het begin van de huidige transactie door andere transacties zijn aangebracht, zijn niet zichtbaar voor overzichten die in de huidige transactie worden uitgevoerd.
  Het effect is alsof de overzichten in een transactie een momentopname krijgen van de vastgelegde gegevens zoals deze bestonden aan het begin van de transactie.
  Momentopnamen zijn consistent in betrouwbare verzamelingen.

Betrouwbare verzamelingen kiezen automatisch het isolatieniveau dat moet worden gebruikt voor een bepaalde leesbewerking, afhankelijk van de bewerking en de rol van de replica op het moment van de creatie van de transactie.
Hieronder volgt de tabel met standaardinstellingen voor isolatieniveau voor bewerkingen betrouwbaar woordenboek en wachtrij.

| Bewerking \ Rol | Primair | Secundair |
| --- |:--- |:--- |
| Eén entiteit gelezen |Herhaalbaar lezen |Momentopname |
| Opsomming, Graaf |Momentopname |Momentopname |

> [!NOTE]
> Veelvoorkomende voorbeelden voor bewerkingen van één entiteit zijn `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Zowel het betrouwbare woordenboek als de ondersteuning voor betrouwbare wachtrij *lees uw schrijft.*
Met andere woorden, elke schrijfplaats binnen een transactie zal zichtbaar zijn voor een volgende lezing die tot dezelfde transactie behoort.

## <a name="locks"></a>Vergrendelingen

In Betrouwbare verzamelingen implementeren alle transacties rigoureuze vergrendeling in twee fasen: een transactie geeft de vergrendelingen die het heeft verkregen niet vrij totdat de transactie eindigt met een mislukte of een commit.

Reliable Dictionary gebruikt vergrendeling op rijniveau voor alle bewerkingen van één entiteit.
Reliable Queue verhandelt gelijktijdigheid voor strikte transactionele FIFO-eigendommen.
Reliable Queue maakt gebruik van vergrendelingen `TryPeekAsync` op `TryDequeueAsync` bedrijfsniveau waarmee `EnqueueAsync` één transactie met en/of één transactie tegelijk mogelijk is.
Houd er rekening mee dat `TryPeekAsync` `TryDequeueAsync` om FIFO te behouden, als een `EnqueueAsync`of ooit merkt dat de betrouwbare wachtrij leeg is, ze ook worden vergrendeld.

Schrijfbewerkingen nemen altijd exclusieve sloten in beslag.
Voor leesbewerkingen is de vergrendeling afhankelijk van een aantal factoren:

- Elke leesbewerking die wordt uitgevoerd met snapshotisolatie is lock-free.
- Elke herhaalbare leesbewerking neemt standaard gedeelde vergrendelingen.
- Voor elke leesbewerking die Herhaalbaar lezen ondersteunt, kan de gebruiker echter om een updatevergrendeling vragen in plaats van de gedeelde vergrendeling.
Een updatevergrendeling is een asymmetrische vergrendeling die wordt gebruikt om een veelvoorkomende vorm van impasse te voorkomen die optreedt wanneer meerdere transacties resources vergrendelen voor potentiële updates op een later tijdstip.

De compatibiliteitsmatrix voor vergrendeling vindt u in de volgende tabel:

| Aanvraag \ Verleend | Geen | Gedeeld | Update | Exclusieve |
| --- |:--- |:--- |:--- |:--- |
| Gedeeld |Geen conflict |Geen conflict |Conflict |Conflict |
| Update |Geen conflict |Geen conflict |Conflict |Conflict |
| Exclusieve |Geen conflict |Conflict |Conflict |Conflict |

Het argument time-out in API's voor betrouwbare verzamelingen wordt gebruikt voor impassedetectie.
Twee transacties (T1 en T2) proberen bijvoorbeeld K1 te lezen en bij te werken.
Het is mogelijk voor hen om impasse, omdat ze allebei eindigen met de Gedeelde slot.
In dit geval krijgen een of beide bewerkingen een time-out. Ik dit scenario, een Update slot kan een dergelijke impasse te voorkomen.

## <a name="next-steps"></a>Volgende stappen

* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Meldingen van betrouwbare services](service-fabric-reliable-services-notifications.md)
* [Back-up en herstel van betrouwbare services (herstel na noodgevallen)](service-fabric-reliable-services-backup-restore.md)
* [Betrouwbare configuratie van State Manager](service-fabric-reliable-services-configuration.md)
* [Ontwikkelaarsreferentie voor betrouwbare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
