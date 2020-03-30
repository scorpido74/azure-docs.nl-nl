---
title: Azure Service Fabric-toepassingsmodel
description: Toepassingen en services in Azure Service Fabric modelleren en beschrijven met behulp van toepassings- en servicemanifestbestanden.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7179686b7d4ef2df267cb95ece8f83d5fb7682b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551876"
---
# <a name="model-an-application-in-service-fabric"></a>Een toepassing modelleren in Service Fabric
In dit artikel vindt u een overzicht van het Azure Service Fabric-toepassingsmodel en hoe u een toepassing en service definiëren via manifeste bestanden.

## <a name="understand-the-application-model"></a>Het toepassingsmodel begrijpen
Een toepassing is een verzameling samenstellende services die een bepaalde functie of functies uitvoeren. Een service voert een volledige en zelfstandige functie uit en kan onafhankelijk van andere services worden gestart en uitgevoerd.  Een service bestaat uit code, configuratie en gegevens. Voor elke service bestaat code uit de uitvoerbare binaire bestanden, bestaat de configuratie uit service-instellingen die kunnen worden geladen tijdens uitvoering en gegevens bestaan uit willekeurige statische gegevens die door de service moeten worden verbruikt. Elk onderdeel in dit hiërarchische toepassingsmodel kan onafhankelijk van elkaar worden geüpgraded en geüpgraded.

![Het servicefabric-toepassingsmodel][appmodel-diagram]

Een toepassingstype is een categorisering van een toepassing en bestaat uit een bundel servicetypen. Een servicetype is een categorisering van een service. De categorisatie kan verschillende instellingen en configuraties hebben, maar de kernfunctionaliteit blijft hetzelfde. De exemplaren van een service zijn de verschillende serviceconfiguratievariaties van hetzelfde servicetype.  

Klassen (of 'typen') van toepassingen en services worden beschreven via XML-bestanden (toepassingsmanifesten en servicemanifesten).  De manifesten beschrijven toepassingen en services en zijn de sjablonen waartegen toepassingen kunnen worden geinstantieerd vanuit de afbeeldingswinkel van het cluster.  Manifesten worden in detail behandeld in [toepassings- en servicemanifesten](service-fabric-application-and-service-manifests.md). De schemadefinitie voor het bestand ServiceManifest.xml en ApplicationManifest.xml is geïnstalleerd met de Service Fabric SDK en de hulpprogramma's voor *C:\Program Files\Microsoft SDKs\Service Fabric\schema's\ServiceFabricServiceModel.xsd*. Het XML-schema is gedocumenteerd in [de schemadocumentatie servicefabricservicemodel.xsd.](service-fabric-service-model-schema.md)

De code voor verschillende toepassingsinstanties wordt uitgevoerd als afzonderlijke processen, zelfs wanneer deze worden gehost door hetzelfde Service Fabric-knooppunt. Bovendien kan de levenscyclus van elke toepassingsinstantie onafhankelijk worden beheerd (bijvoorbeeld geüpgraded). In het volgende diagram ziet u hoe toepassingstypen bestaan uit servicetypen, die op hun beurt bestaan uit code, configuratie en gegevenspakketten. Om het diagram te vereenvoudigen, worden alleen `ServiceType4` de code/config/gegevenspakketten voor weergegeven, hoewel elk servicetype sommige of al die pakkettypen zou bevatten.

![Service Fabric-toepassingstypen en servicetypen][cluster-imagestore-apptypes]

Er kunnen een of meer exemplaren van een servicetype actief zijn in het cluster. Stateful service-exemplaren of replica's bereiken bijvoorbeeld een hoge betrouwbaarheid door de status te repliceren tussen replica's op verschillende knooppunten in het cluster. Replicatie biedt in wezen redundantie voor de service beschikbaar te zijn, zelfs als een knooppunt in een cluster mislukt. Een [partitieservice](service-fabric-concepts-partitioning.md) verdeelt de status (en toegangspatronen tot die status) verder tussen knooppunten in het cluster.

In het volgende diagram ziet u de relatie tussen toepassingen en service-exemplaren, partities en replica's.

![Partities en replica's binnen een service][cluster-application-instances]

> [!TIP]
> U de indeling van toepassingen in een cluster bekijken&lt;met&gt;het hulpprogramma Service Fabric Explorer dat beschikbaar is op http:// yourclusteraddress :19080/Explorer. Zie [Uw cluster visualiseren met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)voor meer informatie.
> 
> 


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [schaalbaarheid van toepassingen](service-fabric-concepts-scalability.md).
- Meer informatie over [servicestatus,](service-fabric-concepts-state.md) [partitionering](service-fabric-concepts-partitioning.md)en [beschikbaarheid](service-fabric-availability-services.md).
- Lees hoe toepassingen en services worden gedefinieerd in [toepassings- en servicemanifesten.](service-fabric-application-and-service-manifests.md)
- [Toepassingshostingmodellen](service-fabric-hosting-model.md) beschrijven de relatie tussen replica's (of instanties) van een geïmplementeerdservice- en servicehostproces.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


