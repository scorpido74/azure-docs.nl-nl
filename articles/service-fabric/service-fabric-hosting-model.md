---
title: Azure Service Fabric-hostingmodel
description: Beschrijft de relatie tussen replica's (of instanties) van een geïmplementeerde Service Fabric-service en het service-hostproces.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 69c7edb08693937aad5a658e0b22b00cd2a81647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282391"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric-hostingmodel
In dit artikel vindt u een overzicht van toepassingshostingmodellen van Azure Service Fabric en worden de verschillen beschreven tussen de **modellen Gedeeld proces** en exclusief **proces.** Hierin wordt beschreven hoe een geïmplementeerde toepassing eruitziet op een servicefabricknooppunt en de relatie tussen replica's (of instanties) van de service en het servicehostproces.

Voordat u verder gaat, moet u de verschillende concepten en relaties begrijpen die zijn uitgelegd in [Model a application in Service Fabric.][a1] 

> [!NOTE]
> In dit artikel, tenzij expliciet genoemd als betekenis iets anders:
>
> - *Replica* verwijst naar zowel een replica van een stateful service als een instantie van een stateless service.
> - *CodePackage* wordt behandeld als gelijkwaardig aan een *ServiceHost-proces* dat een *ServiceType*registreert en replica's van services van dat *ServiceType*host .
>

Om het hostingmodel te begrijpen, laten we een voorbeeld nemen. Stel dat we een *ApplicationType* 'MyAppType' hebben, dat een *ServiceType* 'MyServiceType' heeft. 'MyServiceType' wordt geleverd door het *ServicePackage* 'MyServicePackage', dat een *CodePackage* 'MyCodePackage' heeft. 'MyCodePackage' registreert *ServiceType* 'MyServiceType' wanneer het wordt uitgevoerd.

Stel dat we een cluster met drie nodes hebben en we maken een *application* **toepassingsstructuur:/App1** van het type 'MyAppType'. Binnen deze applicatie **stof:/App1,** maken we een service **stof:/App1/ServiceA** van het type 'MyServiceType'. Deze service heeft twee partities (bijvoorbeeld **P1** en **P2)** en drie replica's per partitie. In het volgende diagram wordt de weergave van deze toepassing weergegeven wanneer deze wordt geïmplementeerd op een knooppunt.


![Diagram met knooppuntweergave van geïmplementeerde toepassing][node-view-one]


Service Fabric activeerde 'MyServicePackage', waarmee 'MyCodePackage' is gestart, dat replica's van beide partities host. Alle knooppunten in het cluster hebben dezelfde weergave, omdat we het aantal replica's per partitie hebben gekozen dat gelijk is aan het aantal knooppunten in het cluster. Laten we een andere service maken, **fabric:/App1/ServiceB**, in de **toepassingsstructuur:/App1**. Deze service heeft één partitie (bijvoorbeeld **P3)** en drie replica's per partitie. In het volgende diagram ziet u de nieuwe weergave op het knooppunt:


![Diagram met knooppuntweergave van geïmplementeerde toepassing][node-view-two]


Service Fabric plaatste de nieuwe replica voor partitie **P3** van **servicefabric:/App1/ServiceB** in de bestaande activering van 'MyServicePackage'. Nwo. laten we een andere **toepassingsstructuur maken:/App2** van het type 'MyAppType'. **Binnenstof:/App2**, maak een **servicefabric:/App2/ServiceA**. Deze service heeft twee partities **(P4** en **P5)** en drie replica's per partitie. In het volgende diagram wordt de nieuwe knooppuntweergave weergegeven:


![Diagram met knooppuntweergave van geïmplementeerde toepassing][node-view-three]


Service Fabric activeert een nieuw exemplaar van 'MyServicePackage', waarmee een nieuw exemplaar van 'MyCodePackage' wordt gestart. Replica's van beide partities van de service **fabric:/App2/ServiceA** **(P4** en **P5)** worden geplaatst in dit nieuwe exemplaar 'MyCodePackage'.

## <a name="shared-process-model"></a>Model voor gedeeld proces
In de vorige sectie wordt het standaardhostingmodel beschreven dat wordt geleverd door Service Fabric, het model Voor gedeeld proces. In dit model wordt voor een bepaalde toepassing slechts één exemplaar van een bepaald *ServicePackage* geactiveerd op een knooppunt (waarbij alle *CodePackages* in het pakket worden gestart). Alle replica's van alle services van een bepaalde *ServiceType* worden geplaatst in het *CodePackage* dat dat *ServiceType*registreert. Met andere woorden, alle replica's van alle services op een knooppunt van een bepaald *ServiceType* delen hetzelfde proces.

## <a name="exclusive-process-model"></a>Exclusief procesmodel
Het andere hostingmodel van Service Fabric is het Exclusive Process-model. In dit model, op een bepaald knooppunt, elke replica leeft in zijn eigen specifieke proces. Service Fabric activeert een nieuwe kopie van *ServicePackage* (waarbij alle *CodePackages* in het programma worden gestart). Replica's worden geplaatst in het *CodePackage* dat het *ServiceType* van de service heeft geregistreerd waartoe de replica behoort. 

Als u Service Fabric-versie 5.6 of hoger gebruikt, u het exclusieve procesmodel kiezen op het moment dat u een service maakt (met [PowerShell,][p1] [REST][r1]of [FabricClient).][c1] Geef **ServicePackageActivationMode** op als 'ExclusiveProcess'.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Als u een standaardservice in uw toepassingsmanifest hebt, u het exclusieve procesmodel kiezen door het kenmerk **ServicePackageActivationMode** op te geven:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Laten we nu een andere service maken, **fabric:/App1/ServiceC**, in **toepassingsstructuur:/App1**. Deze service heeft twee partities (bijvoorbeeld **P6** en **P7)** en drie replica's per partitie. U stelt **ServicePackageActivationMode** in op 'ExclusiveProcess'. In het volgende diagram ziet u een nieuwe weergave op het knooppunt:


![Diagram met knooppuntweergave van geïmplementeerde toepassing][node-view-four]


Zoals u zien, Service Fabric geactiveerd twee nieuwe exemplaren van 'MyServicePackage' (een voor elke replica van partitie **P6** en **P7**). Service Fabric plaatste elke replica in zijn speciale exemplaar van *CodePackage.* Wanneer u het Model Exclusief proces gebruikt, kunnen voor een bepaalde toepassing meerdere exemplaren van een bepaald *ServicePackage* actief zijn op een knooppunt. In het voorgaande voorbeeld zijn drie exemplaren van 'MyServicePackage' actief voor **fabric:/App1**. Aan elk van deze actieve exemplaren van 'MyServicePackage' is een **ServicePackageActivationId** gekoppeld. Deze id identificeert die kopie binnen de **toepassingsstructuur:/App1**.

Wanneer u alleen het model Gedeeld proces voor een toepassing gebruikt, is er slechts één actieve kopie van *ServicePackage* op een knooppunt. De **ServicePackageActivationId** voor deze activering van *ServicePackage* is een lege tekenreeks. Dit is bijvoorbeeld het geval met **stof:/App2**.

> [!NOTE]
>- Het hostingmodel Voor gedeeld proces komt overeen met **ServicePackageActivationMode** is gelijk aan **SharedProcess.** Dit is het standaardhostingmodel en **ServicePackageActivationMode** hoeft niet te worden opgegeven op het moment van het maken van de service.
>
>- Het exclusieve proceshostingmodel komt overeen met **ServicePackageActivationMode** is gelijk aan **ExclusiveProcess.** Als u deze instelling wilt gebruiken, moet u deze expliciet opgeven op het moment dat u de service maakt. 
>
>- Als u het hostingmodel van een service wilt bekijken, stelt u de [servicebeschrijving][p2]op en kijkt u naar de waarde van **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Werken met een geïmplementeerd servicepakket
Een actieve kopie van een *ServicePackage* op een knooppunt wordt een [geïmplementeerd servicepakket][p3]genoemd. Wanneer u het model Exclusive Process gebruikt voor het maken van services, kunnen er voor een bepaalde toepassing meerdere geïmplementeerde servicepakketten zijn voor hetzelfde *ServicePackage.* Als u bewerkingen uitvoert die specifiek zijn voor een geïmplementeerd servicepakket, moet u **ServicePackageActivationId** leveren om een specifiek geïmplementeerd servicepakket te identificeren. Geef bijvoorbeeld de id op als u [de status van een geïmplementeerd servicepakket rapporteert][p4] of het [codepakket van een geïmplementeerd servicepakket opnieuw start.][p5]

U de **ServicePackageActivationId** van een geïmplementeerd servicepakket achterhalen door de lijst [met geïmplementeerde servicepakketten][p3] op een knooppunt op te vragen. Wanneer u query's opvraagt voor de [geïmplementeerde servicetypen,][p6] [geïmplementeerde replica's][p7]en [geïmplementeerde codepakketten][p8] op een knooppunt, bevat het queryresultaat ook het **ServicePackageActivationId** van het bovenliggende geïmplementeerde servicepakket.

> [!NOTE]
>- Onder het hostingmodel Voor gedeeld proces, op een bepaald knooppunt, wordt voor een bepaalde toepassing slechts één exemplaar van een *ServicePackage* geactiveerd. Het heeft een **ServicePackageActivationId** die gelijk is aan *lege tekenreeks*en hoeft niet te worden opgegeven tijdens het uitvoeren van bewerkingen met betrekking tot het geïmplementeerde servicepakket. 
>
> - Onder het Exclusieve Proces hosting model, op een bepaald knooppunt, voor een bepaalde toepassing, een of meer exemplaren van een *ServicePackage* actief kunnen zijn. Elke activering heeft een *niet-lege* **ServicePackageActivationId**, opgegeven tijdens het uitvoeren van bewerkingen met betrekking tot het geïmplementeerde servicepakket. 
>
> - Als **ServicePackageActivationId** wordt weggelaten, wordt tekenreeks standaard *leeggemaakt.* Als er een geïmplementeerd servicepakket aanwezig is dat is geactiveerd onder het model Gedeeld proces, wordt de bewerking uitgevoerd. Anders mislukt de bewerking.
>
> - Vraag niet één keer en cache de **ServicePackageActivationId**. De ID wordt dynamisch gegenereerd en kan om verschillende redenen worden gewijzigd. Voordat u een bewerking uitvoert die **ServicePackageActivationId**nodig heeft, moet u eerst de lijst met [geïmplementeerde servicepakketten][p3] op een knooppunt opvragen. Gebruik vervolgens de **ServicePackageActivationId** uit het queryresultaat om de oorspronkelijke bewerking uit te voeren.
>
>

## <a name="guest-executable-and-container-applications"></a>Gast uitvoerbare en containertoepassingen
Service Fabric behandelt [gastuitvoerbare][a2] en [containertoepassingen][a3] als stateloze services, die op zichzelf staan. Er is geen runtime van Service Fabric in *ServiceHost* (een proces of container). Omdat deze services op zichzelf staan, is het aantal replica's per *ServiceHost* niet van toepassing op deze services. De meest voorkomende configuratie die bij deze services wordt gebruikt, is single-partition, waarbij [InstanceCount][c2] gelijk is aan -1 (één kopie van de servicecode die op elk knooppunt van het cluster wordt uitgevoerd). 

De standaard **ServicePackageActivationMode** voor deze services is **SharedProcess**, in welk geval Service Fabric slechts één kopie van *ServicePackage* activeert op een knooppunt voor een bepaalde toepassing.  Dit betekent dat slechts één kopie van de servicecode een knooppunt zal uitvoeren. Als u wilt dat meerdere kopieën van uw servicecode op een knooppunt worden uitgevoerd, geeft u **ServicePackageActivationMode** op als **ExclusiveProcess** op het moment van het maken van de service. U dit bijvoorbeeld doen wanneer u meerdere services *(Service1* naar *ServiceN)* van *ServiceType* maakt (opgegeven in *ServiceManifest)* of wanneer uw service multi-partitioned is. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Het hostingmodel van een bestaande service wijzigen
Op dit moment u het hostingmodel van een bestaande service niet wijzigen van Gedeeld proces naar exclusief proces (of vice versa).

## <a name="choose-between-the-hosting-models"></a>Kies tussen de hostingmodellen
U moet evalueren welk hostingmodel het beste aan uw eisen voldoet. Het share process-model maakt beter gebruik van bronnen van besturingssystemen, omdat er minder processen worden voortgebracht en meerdere replica's in hetzelfde proces poorten kunnen delen. Als een van de replica's echter een fout heeft waarbij de servicehost moet worden neergehaald, heeft dit gevolgen voor alle andere replica's in hetzelfde proces.

 Het Exclusive Process-model zorgt voor een betere isolatie, met elke replica in zijn eigen proces. Als een van de replica's een fout heeft, heeft dit geen invloed op andere replica's. Dit model is handig voor gevallen waarin het delen van poorten niet wordt ondersteund door het communicatieprotocol. Het vergemakkelijkt de mogelijkheid om resource governance toe te passen op replica niveau. Het exclusieve proces verbruikt echter meer bronnen van het besturingssysteem, omdat het één proces voorelke replica op het knooppunt spawnt.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Exclusieve procesmodel- en toepassingsmodeloverwegingen
Voor de meeste toepassingen u uw toepassing in Service Fabric modelleren door één *ServiceType* per *ServicePackage te behouden.* 

In bepaalde gevallen staat Service Fabric ook meer dan één *ServiceType* per *ServicePackage* toe (en één *CodePackage* kan meer dan één *ServiceType*registreren). Hieronder volgen enkele scenario's waarin deze configuraties nuttig kunnen zijn:

- U wilt het gebruik van resources optimaliseren door minder processen voort te brengen en een hogere replicadichtheid per proces te hebben.
- Replica's van verschillende *ServiceTypes* moeten een aantal algemene gegevens delen die hoge initialisatie- of geheugenkosten hebben.
- U hebt een gratis serviceaanbod en u wilt een limiet stellen aan het gebruik van resources door alle replica's van de service in hetzelfde proces te plaatsen.

Het exclusieve proceshostingmodel is niet consistent met een toepassingsmodel met meerdere *ServiceTypes* per *ServicePackage.* Dit komt omdat meerdere *ServiceTypes* per *ServicePackage* zijn ontworpen om een hogere resourcesharing tussen replica's te bereiken en een hogere replicadichtheid per proces mogelijk maakt. Het Exclusive Process-model is ontworpen om verschillende resultaten te bereiken.

Houd rekening met het geval van meerdere *ServiceTypes* per *ServicePackage,* waarbij elk ServiceType met een ander *CodePackage* wordt *geregistreerd.* Laten we zeggen dat we een *ServicePackage* 'MultiTypeServicePackage' hebben, dat twee *CodePackages*heeft:

- 'MyCodePackageA', dat *servicetype* 'MyServiceTypeA' registreert.
- 'MyCodePackageB', dat *servicetype* 'MyServiceTypeB' registreert.

Nu, laten we zeggen dat we een applicatie, **stof:/SpecialApp**. Inside **fabric:/SpecialApp**, maken we volgende twee diensten met het Exclusieve Proces model:

- **Servicefabric:/SpecialApp/ServiceA** van het type 'MyServiceTypeA', met twee partities (bijvoorbeeld **P1** en **P2)** en drie replica's per partitie.
- **Servicefabric:/SpecialApp/ServiceB** van type 'MyServiceTypeB', met twee partities **(P3** en **P4)** en drie replica's per partitie.

Op een bepaald knooppunt hebben beide services elk twee replica's. Omdat we het Exclusieve Proces-model hebben gebruikt om de services te maken, activeert Service Fabric een nieuwe kopie van 'MyServicePackage' voor elke replica. Elke activering van 'MultiTypeServicePackage' start een kopie van 'MyCodePackageA' en 'MyCodePackageB'. Echter, slechts een van 'MyCodePackageA' of 'MyCodePackageB' hosts de replica waarvoor 'MultiTypeServicePackage' werd geactiveerd. In het volgende diagram wordt de knooppuntweergave weergegeven:


![Diagram van de knooppuntweergave van geïmplementeerde toepassing][node-view-five]


In de activering van 'MultiTypeServicePackage' voor de replica van partitie **P1** van service **fabric:/SpecialApp/ServiceA,**'MyCodePackageA' is het hosten van de replica. 'MyCodePackageB' draait. Ook bij de activering van 'MultiTypeServicePackage' voor de replica van partitie **P3** van **servicefabric:/SpecialApp/ServiceB,** host 'MyCodePackageB' de replica. 'MyCodePackageA' draait. Vandaar, hoe groter het aantal *CodePackages* (het registreren van verschillende *ServiceTypes)* per *ServicePackage,* hoe hoger het redundante resourcegebruik. 
 
 Als we echter de **servicesfabric:/SpecialApp/ServiceA** en **fabric:/SpecialApp/ServiceB** met het Shared Process-model maken, activeert Service Fabric slechts één exemplaar van 'MultiTypeServicePackage' voor de **toepassingsstructuur:/SpecialApp**. 'MyCodePackageA' host alle replica's voor de service **fabric:/SpecialApp/ServiceA**. 'MyCodePackageB' host alle replica's voor de service **fabric:/SpecialApp/ServiceB**. In het volgende diagram wordt de knooppuntweergave in deze instelling weergegeven: 


![Diagram van de knooppuntweergave van geïmplementeerde toepassing][node-view-six]


In het voorgaande voorbeeld zou je kunnen denken dat als 'MyCodePackageA' zowel 'MyServiceTypeA' als 'MyServiceTypeB' registreert, en er geen 'MyCodePackageB' is, er geen redundante *CodePackage* wordt uitgevoerd. Hoewel dit juist is, sluit dit toepassingsmodel niet aan bij het exclusieve proceshostingmodel. Als het doel is om elke replica in zijn eigen specifieke proces te plaatsen, hoeft u niet beide *ServiceTypes* van hetzelfde *CodePackage*te registreren. In plaats daarvan plaatst u gewoon elk *ServiceType* in zijn eigen *ServicePackage.*

## <a name="next-steps"></a>Volgende stappen
[Pakket een applicatie][a4] en maak deze klaar om te implementeren.

[Toepassingen implementeren en verwijderen][a5]. In dit artikel wordt beschreven hoe u PowerShell gebruiken om toepassingsinstanties te beheren.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
