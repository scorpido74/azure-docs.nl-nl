---
title: Azure ServiceFabric-diagnose en -bewaking
description: In dit artikel worden de functies voor prestatiebewaking beschreven in de servicefabric betrouwbare serviceremoting-runtime, zoals prestatiemeteritems die door de service worden uitgezonden.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282274"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnose- en prestatiebewaking voor Betrouwbare Service Remoting
De Reliable ServiceRemoting runtime zendt [prestatiemeteritems](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)uit. Deze bieden inzicht in de werking van de ServiceRemoting en helpen bij het oplossen van problemen en prestatiebewaking.


## <a name="performance-counters"></a>Prestatiemeteritems
Met de runtime Reliable ServiceRemoting worden de volgende prestatiemetercategorieën gedefinieerd:

| Categorie | Beschrijving |
| --- | --- |
| Service Fabric Service |Tellers die specifiek zijn voor Azure Service Fabric Service Remoting, bijvoorbeeld de gemiddelde tijd die wordt genomen om aanvraag te verwerken |
| Servicefabric-servicemethode |Tellers die specifiek zijn voor methoden die zijn geïmplementeerd door Service Fabric Remoting Service, bijvoorbeeld hoe vaak een servicemethode wordt ingeroepen |

Elk van de voorgaande categorieën heeft een of meer tellers.

De [Windows Performance Monitor-toepassing](https://technet.microsoft.com/library/cc749249.aspx) die standaard beschikbaar is in het Windows-besturingssysteem, kan worden gebruikt om prestatiemetergegevens te verzamelen en weer te geven. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) is een andere optie voor het verzamelen van prestatiemetergegevens en het uploaden naar Azure-tabellen.

### <a name="performance-counter-instance-names"></a>Instantienamen voor prestatiemeter
Een cluster met een groot aantal ServiceRemoting-services of -partities heeft een groot aantal prestatiemeterinstanties. De instantienamen van de prestatieteller kunnen helpen bij het identificeren van de specifieke partitie- en servicemethode (indien van toepassing) waarmee de instantie van het prestatiemeterrecht is gekoppeld.

#### <a name="service-fabric-service-category"></a>Categorie Service Fabric Service
Voor de `Service Fabric Service`categorie bevinden de namen van de tellerinstantie zich in de volgende indeling:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* is de tekenreeksweergave van de servicefabricpartitie-id waarmee de instantie van de prestatieteller is gekoppeld. De partitie-id is een GUID en de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) tekenreeksweergave wordt gegenereerd via de methode met indelingsaanduiding "D".

*ServiceReplicaOrInstanceId* is de tekenreeksweergave van de replica-/instantie-id van de servicestructuur waarmee de prestatiemeterinstantie is gekoppeld.

*ServiceRuntimeInternalID* is de tekenreeksweergave van een 64-bits geheelgetal dat wordt gegenereerd door de runtime van de Fabric Service voor intern gebruik. Dit is opgenomen in de naam van de instantie van de prestatieteller om de uniciteit ervan te garanderen en conflicten met andere namen van prestatiemeteritems te voorkomen. Gebruikers moeten niet proberen om dit gedeelte van de naam van de prestatiemeterinstantie te interpreteren.

Het volgende is een voorbeeld van een naam van `Service Fabric Service` een tellerinstantie voor een teller die tot de categorie behoort:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

In het voorgaande `2740af29-78aa-44bc-a20b-7e60fb783264` voorbeeld is de tekenreeksweergave van `635650083799324046` de servicefabricpartitie-id, `5008379932` is tekenreeksweergave van Replica/InstanceId en is het 64-bits ID dat wordt gegenereerd voor intern gebruik van de runtime.

#### <a name="service-fabric-service-method-category"></a>Categorie Service Fabric-servicemethode
Voor de `Service Fabric Service Method`categorie bevinden de namen van de tellerinstantie zich in de volgende indeling:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* is de naam van de servicemethode waar de prestatiemeterinstantie aan is gekoppeld. De indeling van de methodenaam wordt bepaald op basis van een logica in de runtime van de Fabric Service die de leesbaarheid van de naam in evenwicht brengt met beperkingen op de maximale lengte van de instantienamen van de prestatiemeter in Windows.

*ServiceRuntimeMethodId* is de tekenreeksweergave van een 32-bits geheelgetal dat wordt gegenereerd door de runtime van de Fabric Service voor intern gebruik. Dit is opgenomen in de naam van de instantie van de prestatieteller om de uniciteit ervan te garanderen en conflicten met andere namen van prestatiemeteritems te voorkomen. Gebruikers moeten niet proberen om dit gedeelte van de naam van de prestatiemeterinstantie te interpreteren.

*ServiceFabricPartitionID* is de tekenreeksweergave van de servicefabricpartitie-id waarmee de instantie van de prestatieteller is gekoppeld. De partitie-id is een GUID en de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) tekenreeksweergave wordt gegenereerd via de methode met indelingsaanduiding "D".

*ServiceReplicaOrInstanceId* is de tekenreeksweergave van de replica-/instantie-id van de servicestructuur waarmee de prestatiemeterinstantie is gekoppeld.

*ServiceRuntimeInternalID* is de tekenreeksweergave van een 64-bits geheelgetal dat wordt gegenereerd door de runtime van de Fabric Service voor intern gebruik. Dit is opgenomen in de naam van de instantie van de prestatieteller om de uniciteit ervan te garanderen en conflicten met andere namen van prestatiemeteritems te voorkomen. Gebruikers moeten niet proberen om dit gedeelte van de naam van de prestatiemeterinstantie te interpreteren.

Het volgende is een voorbeeld van een naam van `Service Fabric Service Method` een tellerinstantie voor een teller die tot de categorie behoort:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

In het voorgaande `ivoicemailboxservice.leavemessageasync` voorbeeld is `2` de methodenaam, is de 32-bits ID `89383d32-e57e-4a9b-a6ad-57c6792aa521` die wordt gegenereerd voor het interne`635650083804480486` gebruik van de runtime, is `5008380` de tekenreeksweergave van de servicefabricpartitie-id, is de tekenreeksweergave van de replica-/instantie-id van de servicestructuur en is het 64-bits-id dat is gegenereerd voor intern gebruik van de runtime.

## <a name="list-of-performance-counters"></a>Lijst met prestatiemeteritems
### <a name="service-method-performance-counters"></a>Prestatiemeteritems voor servicemethoden

De runtime van de betrouwbare service publiceert de volgende prestatiemeteritems met betrekking tot de uitvoering van servicemethoden.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Servicefabric-servicemethode |Aanroepingen/Sec |Aantal keren dat de servicemethode per seconde wordt ingeroepen |
| Servicefabric-servicemethode |Gemiddelde milliseconden per aanroep |Tijd die nodig is om de servicemethode in milliseconden uit te voeren |
| Servicefabric-servicemethode |Uitzonderingen gegooid/Sec |Aantal keren dat de servicemethode een uitzondering per seconde heeft gemaakt |

### <a name="service-request-processing-performance-counters"></a>Prestatiemeteritems voor het verwerken van serviceaanvragen
Wanneer een client een methode aanroept via een serviceproxyobject, resulteert dit in een verzoekbericht dat via het netwerk naar de remoting-service wordt verzonden. De service verwerkt het verzoekbericht en stuurt een antwoord terug naar de client. De runtime reliable serviceremoting publiceert de volgende prestatiemeteritems met betrekking tot de verwerking van serviceaanvragen.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Service Fabric Service |Aantal openstaande verzoeken |Aantal aanvragen dat in de service wordt verwerkt |
| Service Fabric Service |Gemiddelde milliseconden per aanvraag |Tijd die de service nodig heeft om een aanvraag te verwerken (in milliseconden) |
| Service Fabric Service |Gemiddelde milliseconden voor aanvraagdeserialisatie |Tijd die nodig is (in milliseconden) om serviceaanvraagbericht te deserialiseren wanneer het wordt ontvangen bij de service |
| Service Fabric Service |Gemiddelde milliseconden voor responsserialisatie |Tijd die nodig is (in milliseconden) om het serviceresponsbericht bij de service te serialiseren voordat het antwoord naar de client wordt verzonden |

## <a name="next-steps"></a>Volgende stappen
* [Voorbeeldcode](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [EventSource-providers in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
