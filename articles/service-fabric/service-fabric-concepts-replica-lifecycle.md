---
title: Replica's en instanties in Azure Service Fabric
description: Meer informatie over replica's en exemplaren in Service Fabric, inclusief een overzicht van hun levenscyclus en functies.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258562"
---
# <a name="replicas-and-instances"></a>Replica's en exemplaren 
Dit artikel geeft een overzicht van de levenscyclus van replica's van stateful services en exemplaren van stateless services.

## <a name="instances-of-stateless-services"></a>Gevallen van staatloze services
Een instantie van een statusloze service is een kopie van de servicelogica die wordt uitgevoerd op een van de knooppunten van het cluster. Een instantie binnen een partitie wordt uniek geïdentificeerd door de **InstanceId**. De levenscyclus van een instantie is gemodelleerd naar het volgende diagram:

![Levenscyclus van instantie](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Nadat clusterbronbeheer een plaatsing voor de instantie heeft bepaald, wordt deze levenscyclusstatus invoert. De instantie wordt gestart op het knooppunt. De toepassingshost wordt gestart, de instantie wordt gemaakt en vervolgens geopend. Nadat het opstarten is voltooid, wordt de instantie overgezet naar de gereedstaat. 

Als de toepassingshost of -knooppunt voor dit exemplaar vastloopt, wordt deze overgezet naar de status gevallen.

### <a name="ready-rd"></a>Klaar (RD)
In de gereedstaat is de instantie up-and-running op het knooppunt. Als dit exemplaar een betrouwbare service is, is **RunAsync** aangeroepen. 

Als de toepassingshost of -knooppunt voor dit exemplaar vastloopt, wordt deze overgezet naar de status gevallen.

### <a name="closing-cl"></a>Sluiting (CL)
In de afsluitstatus is Azure Service Fabric bezig met het afsluiten van de instantie op dit knooppunt. Deze afsluiting kan te wijten zijn aan vele redenen, bijvoorbeeld een upgrade van een toepassing, taakverdeling of het verwijderen van de service. Nadat de shutdown is voltooid, wordt deze overgezet naar de gedaalde status.

### <a name="dropped-dd"></a>Dropped (DD)
In de status dropped wordt de instantie niet meer op het knooppunt uitgevoerd. Op dit moment onderhoudt Service Fabric de metadata over dit exemplaar, dat uiteindelijk ook wordt verwijderd.

> [!NOTE]
> Het is mogelijk om over te schakelen van elke status `Remove-ServiceFabricReplica`naar de gelatenvallen met behulp van de optie **ForceRemove** op .
>

## <a name="replicas-of-stateful-services"></a>Replica's van statige diensten
Een replica van een stateful service is een kopie van de servicelogica die wordt uitgevoerd op een van de knooppunten van het cluster. Bovendien behoudt de replica een kopie van de status van die service. Twee verwante concepten beschrijven de levenscyclus en het gedrag van stateful replica's:
- Levenscyclus van replica's
- Replicarol

De volgende bespreking beschrijft voortdurende stateful diensten. Voor vluchtige (of in-memory) stateful services zijn de down- en dropped-statussen gelijkwaardig.

![Levenscyclus van replica's](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Een InBuild-replica is een replica die is gemaakt of voorbereid om deel te nemen aan de replicaset. Afhankelijk van de replicarol heeft de IB verschillende semantiek. 

Als de toepassingshost of het knooppunt voor een InBuild-replica vastloopt, wordt deze overgezet naar de downstatus.

   - **Primaire InBuild-replica's:** Primary InBuild zijn de eerste replica's voor een partitie. Deze replica gebeurt meestal wanneer de partitie wordt gemaakt. Primaire InBuild-replica's ontstaan ook wanneer alle replica's van een partitie opnieuw worden opgestart of worden verwijderd.

   - **IdleSecondary InBuild replica's:** Dit zijn nieuwe replica's die zijn gemaakt door de Cluster Resource Manager, of bestaande replica's die naar beneden ging en moeten worden toegevoegd terug in de set. Deze replica's worden gezaaid of gebouwd door de primaire voordat ze kunnen deelnemen aan de replica set als ActiveSecondary en deelnemen aan quorum bevestiging van operaties.

   - **ActiveSecondary InBuild replica's:** Deze status wordt waargenomen in sommige query's. Het is een optimalisatie waarbij de replicaset niet verandert, maar een replica moet worden gebouwd. De replica zelf volgt de normale status machine overgangen (zoals beschreven in de sectie over replica rollen).

### <a name="ready-rd"></a>Klaar (RD)
Een replica klaar is een replica die deelneemt aan replicatie en quorumbevestiging van bewerkingen. De gereedstaande status is van toepassing op primaire en actieve secundaire replica's.

Als de toepassingshost of het knooppunt voor een gereedreplica vastloopt, wordt deze overgezet naar de downstatus.

### <a name="closing-cl"></a>Sluiting (CL)
Een replica voert de afsluitingsstatus in de volgende scenario's in:

- **De code voor de replica afsluiten:** Servicefabric moet mogelijk de bedrijfscode voor een replica afsluiten. Deze shutdown kan om vele redenen zijn. Dit kan bijvoorbeeld gebeuren vanwege een upgrade van een toepassing, fabric of infrastructuur of als gevolg van een fout die door de replica wordt gerapporteerd. Wanneer de replica bijna klaar is, wordt de replica overgezet naar de downstatus. De status die is ingeschakeld voor deze replica die op schijf is opgeslagen, wordt niet opgeschoond.

- **De replica uit het cluster verwijderen:** Servicefabric moet mogelijk de status voortduren verwijderen en de loopcode voor een replica afsluiten. Deze shutdown kan om vele redenen zijn, bijvoorbeeld het balanceren van de last.

### <a name="dropped-dd"></a>Dropped (DD)
In de status dropped wordt de instantie niet meer op het knooppunt uitgevoerd. Er is ook geen staat meer op het knooppunt. Op dit moment onderhoudt Service Fabric de metadata over dit exemplaar, dat uiteindelijk ook wordt verwijderd.

### <a name="down-d"></a>Omlaag (D)
In de downstatus wordt de replicacode niet uitgevoerd, maar de status voor die replica bestaat op dat knooppunt. Een replica kan naar beneden om vele redenen - bijvoorbeeld, het knooppunt wordt naar beneden, een crash in de replica code, een applicatie-upgrade, of replica fouten.

Een down replica wordt geopend door Service Fabric zoals vereist, bijvoorbeeld wanneer de upgrade eindigt op het knooppunt.

De replicarol is niet relevant in de down-status.

### <a name="opening-op"></a>Opening (OP)
Een down replica komt in de openingsstatus wanneer Service Fabric de replica weer omhoog moet brengen. Deze status kan bijvoorbeeld zijn nadat een code-upgrade voor de toepassing is voltooid op een knooppunt. 

Als de toepassingshost of het knooppunt voor een openingsreplica vastloopt, wordt deze overgezet naar de downstatus.

De replicarol is niet relevant in de openingsstatus.

### <a name="standby-sb"></a>Stand-by (SB)
Een stand-by replica is een replica van een voortdurende service die ging naar beneden en werd vervolgens geopend. Deze replica kan worden gebruikt door Service Fabric als deze een andere replica aan de replicaset moet toevoegen (omdat de replica al een deel van de status heeft en het bouwproces sneller is). Nadat de StandByReplicaKeepDuration is verlopen, wordt de stand-byreplica verwijderd.

Als de toepassingshost of het knooppunt voor een stand-byreplica vastloopt, wordt deze overgezet naar de downstatus.

De replicarol is niet relevant in de stand-bystatus.

> [!NOTE]
> Elke replica die niet naar beneden of gedaald wordt beschouwd als *up*.
>

> [!NOTE]
> Het is mogelijk om over te schakelen van elke status `Remove-ServiceFabricReplica`naar de gelatenvallen met behulp van de optie **ForceRemove** op .
>

## <a name="replica-role"></a>Replicarol 
De rol van de replica bepaalt de functie in de replicaset:

- **Primair (P)**: Er is één primaire in de replicaset die verantwoordelijk is voor het uitvoeren van lees- en schrijfbewerkingen. 
- **ActiveSecondary (S)**: Dit zijn replica's die statusupdates van de primaire ontvangen, deze toepassen en vervolgens bevestigingen terugsturen. Er zijn meerdere actieve secondaries in de replicaset. Het aantal van deze actieve secondaries bepaalt het aantal fouten dat de service kan verwerken.
- **IdleSecondary (I)**: Deze replica's worden gebouwd door de primaire. Ze ontvangen status van de primaire voordat ze kunnen worden bevorderd tot actieve secundaire. 
- **None**
- **Onbekend (U):** dit is de eerste rol van een replica voordat deze een **ChangeRole** API-aanroep van Service Fabric ontvangt.

In het volgende diagram worden de overgangen van replicarollen en enkele voorbeeldscenario's geïllustreerd waarin ze kunnen optreden:

![Replicarol](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Het maken van een nieuwe primaire replica.
- U -> I: Het maken van een nieuwe niet-actieve replica.
- U -> N: Schrapping van een stand-by replica.
- I -> S: Bevordering van de niet-actieve secundaire tot actieve secundaire, zodat de erkenningen bijdragen aan quorum.
- I -> P: Promotie van de niet-actieve secundaire naar primaire. Dit kan gebeuren onder speciale herconfiguraties wanneer de niet-actieve secundaire is de juiste kandidaat om primaire.
- Ik -> N: Schrapping van de niet-actieve secundaire replica.
- S -> P: Promotie van de actieve secundaire naar primaire. Dit kan te wijten zijn aan failover van de primaire of primaire beweging geïnitieerd door de Cluster Resource Manager. Het kan bijvoorbeeld een reactie zijn op een upgrade van een toepassing of taakverdeling.
- S -> N: Verwijderen van de actieve secundaire replica.
- P-> S: Degradatie van de primaire replica. Dit kan te wijten zijn aan een primaire beweging geïnitieerd door de Cluster Resource Manager. Het kan bijvoorbeeld een reactie zijn op een upgrade van een toepassing of taakverdeling.
- P -> N: Verwijderen van de primaire replica.

> [!NOTE]
> Programmeermodellen op hoger niveau, zoals [Betrouwbare Actoren](service-fabric-reliable-actors-introduction.md) en [Betrouwbare Diensten,](service-fabric-reliable-services-introduction.md)verbergen het concept van replicarollen voor de ontwikkelaar. In Acteurs is het begrip van een rol niet nodig. In Services is het grotendeels vereenvoudigd voor de meeste scenario's.
>

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over servicefabric-concepten:

[Levenscyclus van Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

