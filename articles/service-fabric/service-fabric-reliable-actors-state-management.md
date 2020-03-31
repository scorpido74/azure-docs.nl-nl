---
title: Betrouwbare actoren staatsbeheer
description: Beschrijft hoe de status Betrouwbare actoren wordt beheerd, volhardt en gerepliceerd voor hoge beschikbaarheid.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9962d4333e458243670d1005ad2ccfbc0bb7c92a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348917"
---
# <a name="reliable-actors-state-management"></a>Betrouwbare actoren staatsbeheer
Betrouwbare actoren zijn objecten met één thread die zowel logica als status kunnen inkapselen. Omdat actoren op Betrouwbare Services draaien, kunnen ze de status betrouwbaar handhaven met behulp van dezelfde persistentie- en replicatiemechanismen. Op deze manier verliezen acteurs hun status niet na fouten, bij reactivering na het ophalen van afval of wanneer ze worden verplaatst tussen knooppunten in een cluster als gevolg van resourcebalancing of upgrades.

## <a name="state-persistence-and-replication"></a>Staatspersistentie en replicatie
Alle betrouwbare actoren worden beschouwd als *stateful* omdat elke actor instantie kaarten aan een unieke ID. Dit betekent dat herhaalde oproepen naar dezelfde actor-ID worden doorgestuurd naar dezelfde actorinstantie. In een stateless systeem daarentegen worden clientoproepen niet gegarandeerd elke keer naar dezelfde server doorgestuurd. Om deze reden, actor diensten zijn altijd stateful diensten.

Hoewel acteurs als stateful worden beschouwd, betekent dat niet dat ze de staat betrouwbaar moeten opslaan. Actoren kunnen het niveau van statuspersistentie en replicatie kiezen op basis van hun vereisten voor gegevensopslag:

* **Aanhoudende status**: status wordt gehandhaafd op schijf en wordt gerepliceerd naar drie of meer replica's. De aanhoudende status is de meest duurzame statusopslagoptie, waarbij de status kan blijven bestaan door volledige clusteruitval.
* **Vluchtige status:** status wordt gerepliceerd naar drie of meer replica's en alleen bewaard in het geheugen. De vluchtige status biedt veerkracht tegen knooppuntfouten en actorfouten en tijdens upgrades en resourcebalancing. De status is echter niet aande schijf. Dus als alle replica's in een keer verloren gaan, is de staat ook verloren.
* **Geen aanhoudende status:** status wordt niet gerepliceerd of naar schijf geschreven, alleen gebruiken voor actoren die de status niet betrouwbaar hoeven te onderhouden.

Elk niveau van persistentie is gewoon een andere *statusprovider* en *replicatieconfiguratie* van uw service. Of de status is geschreven naar schijf is afhankelijk van de status provider - de component in een betrouwbare service die opslaat staat. Replicatie is afhankelijk van het aantal replica's waarmee een service is geïmplementeerd. Net als bij Reliable Services kunnen zowel de provider van de staat als het aantal replica's eenvoudig handmatig worden ingesteld. Het actorframework biedt een kenmerk dat, wanneer het wordt gebruikt op een actor, automatisch een standaardstatusprovider selecteert en automatisch instellingen genereert voor het aantal replica's om een van deze drie persistentie-instellingen te bereiken. Het kenmerk StatePersistence wordt niet overgenomen door afgeleide klasse, elk actortype moet het StatePersistence-niveau opgeven.

### <a name="persisted-state"></a>Aanhoudende status
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Deze instelling maakt gebruik van een statusprovider die gegevens op schijf opslaat en het aantal servicereplica's automatisch instelt op 3.

### <a name="volatile-state"></a>Vluchtige toestand
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Deze instelling maakt gebruik van een provider met alleen geheugenstatus en stelt het aantal replica's in op 3.

### <a name="no-persisted-state"></a>Geen aanhoudende status
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Deze instelling maakt gebruik van een provider met alleen geheugenstatus en stelt het aantal replica's in op 1.

### <a name="defaults-and-generated-settings"></a>Standaardinstellingen en gegenereerde instellingen
Wanneer u het `StatePersistence` kenmerk gebruikt, wordt een statusprovider automatisch voor u geselecteerd bij runtime wanneer de actorservice wordt gestart. Het aantal replica's wordt echter op het moment van compileren ingesteld door de tools voor het bouwen van de Visual Studio-actor. De buildtools genereren automatisch een *standaardservice* voor de actorservice in ApplicationManifest.xml. Er worden parameters gemaakt voor **de grootte van de minreplicaset** en de ingestelde grootte van de **doelreplica.**

U deze parameters handmatig wijzigen. Maar telkens `StatePersistence` wanneer het kenmerk wordt gewijzigd, worden de parameters ingesteld `StatePersistence` op de standaardwaarden voor de replicasetgrootte voor het geselecteerde kenmerk, waarin eventuele eerdere waarden worden overheid. Met andere woorden, de waarden die u *only* inServiceManifest.xml instelt, worden `StatePersistence` alleen overschreven op buildtijd wanneer u de kenmerkwaarde wijzigt.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Staatsmanager
Elke actorinstantie heeft zijn eigen statusmanager: een woordenboekachtige gegevensstructuur die op betrouwbare wijze sleutel-/waardeparen opslaat. De state manager is een wrapper rond een state provider. U deze gebruiken om gegevens op te slaan, ongeacht welke persistentieinstelling wordt gebruikt. Het biedt geen garanties dat een lopende actorservice kan worden gewijzigd van een vluchtige (alleen-geheugeninstelling) naar een aanhoudende statusinstelling via een rolling upgrade met behoud van gegevens. Het is echter mogelijk om het aantal replica's voor een lopende service te wijzigen.

De sleutels van de staatsmanager moeten tekenreeksen zijn. Waarden zijn generiek en kunnen elk type zijn, inclusief aangepaste typen. Waarden die zijn opgeslagen in de statusbeheerder, moeten serializable gegevenscontract zijn omdat ze tijdens de replicatie via het netwerk naar andere knooppunten kunnen worden verzonden en naar de schijf kunnen worden geschreven, afhankelijk van de instelling voor statuspersistentie van een actor.

De statusbeheerder legt algemene woordenboekmethoden bloot voor het beheren van de status, vergelijkbaar met die in Reliable Dictionary.

Voor voorbeelden van het beheren van de actorstatus, lees [toegang, opslaan en verwijderen van de status Betrouwbare actoren](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Aanbevolen procedures
Hier volgen enkele aanbevolen procedures en tips voor het oplossen van problemen voor het beheren van de actorstatus.

### <a name="make-the-actor-state-as-granular-as-possible"></a>De actorstatus zo gedetailleerd mogelijk maken
Dit is essentieel voor prestaties en resourcegebruik van uw toepassing. Wanneer er een schrijven / bijwerken naar de "benoemde status" van een actor, de hele waarde die overeenkomt met die "benoemde status" wordt geserialiseerd en verzonden via het netwerk naar de secundaire replica's.  De secondaries schrijven het naar de lokale schijf en antwoord terug op de primaire replica. Wanneer de primaire bevestigingen ontvangt van een quorum van secundaire replica's, schrijft het de status naar de lokale schijf. Stel dat de waarde een klasse is met 20 leden en een grootte van 1 MB. Zelfs als u slechts een van de klasseleden die van grootte 1 KB is gewijzigd, betaalt u uiteindelijk de kosten van serialisatie en netwerk- en schijfschrijft voor de volledige 1 MB. Als de waarde een verzameling is (zoals een lijst, array of woordenboek), betaalt u ook de kosten voor de volledige verzameling, zelfs als u een van de leden wijzigt. De StateManager-interface van de actorklasse is als een woordenboek. U moet altijd de gegevensstructuur modelleren die de actorstatus boven op dit woordenboek vertegenwoordigt.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>De levenscyclus van de acteur correct beheren
U moet een duidelijk beleid hebben over het beheren van de grootte van de status in elke partitie van een actorservice. Uw actor dienst moet een vast aantal acteurs en hergebruik ze zo veel mogelijk. Als u voortdurend nieuwe acteurs maakt, moet u ze verwijderen zodra ze klaar zijn met hun werk. Het actorframework slaat een aantal metagegevens op over elke actor die bestaat. Als u alle status van een actor verwijdert, worden geen metagegevens over die actor verwijderd. U moet de actor verwijderen (zie [het verwijderen van acteurs en hun status)](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)om alle informatie over het in het systeem opgeslagen informatie te verwijderen. Als een extra controle, moet u query de actor service (zie [opsomming van acteurs)](service-fabric-reliable-actors-enumerate.md)eens in de zoveel tijd om ervoor te zorgen dat het aantal acteurs binnen het verwachte bereik.
 
Als u ooit ziet dat de grootte van het databasebestand van een Actor-service groter is dan de verwachte grootte, moet u ervoor zorgen dat u de voorgaande richtlijnen volgt. Als u deze richtlijnen volgt en nog steeds problemen met de bestandsgrootte in de database hebt, moet u [een ondersteuningsticket openen](service-fabric-support.md) met het productteam om hulp te krijgen.

## <a name="next-steps"></a>Volgende stappen

Staat die is opgeslagen in betrouwbare actoren moet worden geserialiseerd voordat de naar schijf worden geschreven en gerepliceerd voor hoge beschikbaarheid. Meer informatie over [serie-serie actortype](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Lees vervolgens meer over [actordiagnostiek en prestatiebewaking.](service-fabric-reliable-actors-diagnostics.md)
