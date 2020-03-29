---
title: 'Testability: Servicecommunicatie'
description: Service-to-service communicatie is een essentieel integratiepunt van een Service Fabric-toepassing. Dit artikel bespreekt ontwerpoverwegingen en testtechnieken.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 87b922cb9655588a22c739d26c9ce9e49d35781a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465562"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric testability scenario's: Service communicatie
Microservices en servicegerichte architecturale stijlen komen op natuurlijke wijze voorin Azure Service Fabric. In dit soort gedistribueerde architecturen bestaan componentized microservice-toepassingen meestal uit meerdere services die met elkaar moeten praten. In zelfs de eenvoudigste gevallen hebt u over het algemeen ten minste een stateless webservice en een stateful data-opslagservice die moet communiceren.

Service-to-service communicatie is een essentieel integratiepunt van een toepassing, omdat elke service een externe API blootstelt aan andere services. Werken met een set API-grenzen waarbij I/O betrokken is, vereist over het algemeen enige zorg, met een goede hoeveelheid testen en validatie.

Er zijn tal van overwegingen te maken wanneer deze service grenzen zijn verbonden met elkaar in een gedistribueerd systeem:

* *Vervoersprotocol*. Gebruikt u HTTP voor meer interoperabiliteit of een aangepast binair protocol voor maximale doorvoer?
* *Foutafhandeling*. Hoe worden permanente en tijdelijke fouten verwerkt? Wat gebeurt er als een service naar een ander knooppunt wordt verplaatst?
* *Time-outs en latentie.* Hoe kan elke servicelaag in multitiered-toepassingen latentie verwerken via de stack en de gebruiker?

Of u nu een van de ingebouwde servicecommunicatiecomponenten van Service Fabric gebruikt of zelf bouwt, het testen van de interacties tussen uw services is essentieel om tolerantie in uw toepassing te garanderen.

## <a name="prepare-for-services-to-move"></a>Voorbereiden op services om te verplaatsen
Service-exemplaren kunnen zich in de loop van de tijd verplaatsen. Dit geldt vooral wanneer ze zijn geconfigureerd met load metrics voor op maat gemaakte optimale resource balancing. Service Fabric verplaatst uw service-exemplaren om hun beschikbaarheid te maximaliseren, zelfs tijdens upgrades, failovers, scale-out sen en andere situaties die zich voordoen gedurende de levensduur van een gedistribueerd systeem.

Terwijl services zich verplaatsen in het cluster, moeten uw klanten en andere services bereid zijn om twee scenario's te verwerken wanneer ze met een service praten:

* De service-instantie of partitiereplica is verplaatst sinds de laatste keer dat u ermee hebt gesproken. Dit is een normaal onderdeel van een servicelevenscyclus en dit moet naar verwachting gebeuren tijdens de levensduur van uw toepassing.
* De service-instantie of partitiereplica is in beweging. Hoewel failover van een service van het ene knooppunt naar het andere zeer snel optreedt in Service Fabric, kan er een vertraging in de beschikbaarheid optreden als de communicatiecomponent van uw service traag wordt gestart.

Het is belangrijk om deze scenario's sierlijk te hanteren voor een soepel draaiend systeem. Houd er om dit te doen in gedachten dat:

* Elke service die kan worden aangesloten op heeft een *adres* dat het luistert op (bijvoorbeeld, HTTP of WebSockets). Wanneer een serviceinstantie of partitie wordt verplaatst, wordt het adreseindpunt gewijzigd. (Het gaat naar een ander knooppunt met een ander IP-adres.) Als u de ingebouwde communicatiecomponenten gebruikt, worden deze serviceadressen voor u opnieuw opgelost.
* Er kan een tijdelijke toename van de servicelatentie zijn als de service-instantie de listener opnieuw opstart. Dit hangt af van hoe snel de service de listener opent nadat de service-instantie is verplaatst.
* Bestaande verbindingen moeten worden gesloten en heropend nadat de service wordt geopend op een nieuw knooppunt. Een sierlijke node afsluiten of opnieuw opstarten maakt het mogelijk de tijd voor bestaande verbindingen te worden afgesloten sierlijk.

### <a name="test-it-move-service-instances"></a>Testen: Service-exemplaren verplaatsen
Door de testability tools van Service Fabric te gebruiken, u een testscenario maken om deze situaties op verschillende manieren te testen:

1. De primaire replica van een stateful service verplaatsen.
   
    De primaire replica van een stateful servicepartitie kan om verschillende redenen worden verplaatst. Gebruik dit om de primaire replica van een specifieke partitie te targeten om te zien hoe uw services op een zeer gecontroleerde manier reageren op de verplaatsing.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Stop een knooppunt.
   
    Wanneer een knooppunt wordt gestopt, verplaatst Service Fabric alle service-exemplaren of partities die zich op dat knooppunt bevonden naar een van de andere beschikbare knooppunten in het cluster. Gebruik dit om een situatie te testen waarin een knooppunt verloren gaat van uw cluster en alle service-exemplaren en replica's op dat knooppunt moeten worden verplaatst.
   
    U een knooppunt stoppen met de PowerShell **Stop-ServiceFabricNode-cmdlet:**
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Servicebeschikbaarheid behouden
Als platform is Service Fabric ontworpen om een hoge beschikbaarheid van uw diensten te bieden. Maar in extreme gevallen kunnen onderliggende infrastructuurproblemen nog steeds leiden tot onbeschikbaarheid. Het is belangrijk om te testen voor deze scenario's, ook.

Stateful services gebruiken een op quorum gebaseerd systeem om de status te repliceren voor hoge beschikbaarheid. Dit betekent dat een quorum van replica's beschikbaar moet zijn om schrijfbewerkingen uit te voeren. In zeldzame gevallen, zoals een wijdverbreide hardwarefout, is een quorum van replica's mogelijk niet beschikbaar. In deze gevallen u geen schrijfbewerkingen uitvoeren, maar u wel leesbewerkingen uitvoeren.

### <a name="test-it-write-operation-unavailability"></a>Testen: Schrijfbewerking onbeschikbaarheid
Door de testabiliteitstools in Service Fabric te gebruiken, u een fout injecteren die quorumverlies als test veroorzaakt. Hoewel een dergelijk scenario zeldzaam is, is het belangrijk dat klanten en services die afhankelijk zijn van een stateful service bereid zijn om situaties te behandelen waarin ze er geen schrijfverzoeken aan kunnen doen. Het is ook belangrijk dat de statige dienst zelf zich bewust is van deze mogelijkheid en deze op sierlijke wijze kan communiceren met bellers.

U quorumverlies veroorzaken met de cmdlet PowerShell **Invoke-ServiceFabricPartitionQuorumLoss:**

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

In dit voorbeeld `QuorumLossMode` willen `QuorumReplicas` we aangeven dat we quorumverlies willen veroorzaken zonder alle replica's te nemen. Op deze manier zijn leesbewerkingen nog steeds mogelijk. Als u een scenario wilt testen waarbij een volledige `AllReplicas`partitie niet beschikbaar is, u deze schakelaar instellen op.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over testabiliteitsacties](service-fabric-testability-actions.md)

[Meer informatie over testabiliteitsscenario's](service-fabric-testability-scenarios.md)

