---
title: Een bestaande uitvoerbare voor Azure Service Fabric verpakken
description: Meer informatie over het verpakken van een bestaande toepassing als een gast uitvoerbaar, zodat deze kan worden geïmplementeerd in een Service Fabric-cluster.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3d7aab28a32effa2caf7b04b830d72e5e3dfda56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457830"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Een bestaande uitvoerbare fabric implementeren
U elk type code, zoals Node.js, Java of C++, uitvoeren in Azure Service Fabric as a service. Service Fabric verwijst naar dit soort services als gastexecutables.

Gastexecutables worden behandeld door Service Fabric als stateless services. Als gevolg hiervan worden ze op knooppunten in een cluster geplaatst, op basis van beschikbaarheid en andere statistieken. In dit artikel wordt beschreven hoe u een gast verpakken en implementeren die kan worden uitgevoerd in een Cluster van Servicefabric, met behulp van Visual Studio of een command-line hulpprogramma.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Voordelen van het uitvoeren van een gast die uitvoerbaar is in Service Fabric
Er zijn verschillende voordelen aan het uitvoeren van een gast die uitvoerbaar is in een cluster van Service Fabric:

* Hoge beschikbaarheid. Toepassingen die in Service Fabric worden uitgevoerd, worden in hoge mate beschikbaar gesteld. Service Fabric zorgt ervoor dat exemplaren van een toepassing worden uitgevoerd.
* Gezondheidsmonitoring. Service Fabric health monitoring detecteert of een toepassing wordt uitgevoerd en biedt diagnostische informatie als er een storing optreedt.   
* Beheer van de levenscyclus van toepassingen. Naast het verstrekken van upgrades zonder downtime, Service Fabric biedt automatische rollback naar de vorige versie als er een slechte gezondheid gebeurtenis gemeld tijdens een upgrade.    
* Dichtheid. U meerdere toepassingen in een cluster uitvoeren, waardoor elke toepassing niet meer op zijn eigen hardware hoeft te worden uitgevoerd.
* Vindbaarheid: met REST u de servicefabricnaamgevingsservice bellen om andere services in het cluster te vinden. 

## <a name="samples"></a>Voorbeelden
* [Monster voor het verpakken en implementeren van een gast uitvoerbaar](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Voorbeeld van twee gastexecutables (C# en nodejs) die communiceren via de naamgevingsservice via REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Overzicht van toepassings- en servicemanifestbestanden
Als onderdeel van het implementeren van een gast uitvoerbaar, is het handig om het Service Fabric-verpakkings- en implementatiemodel te begrijpen zoals beschreven in [het toepassingsmodel.](service-fabric-application-model.md) Het Service Fabric-verpakkingsmodel is gebaseerd op twee XML-bestanden: de toepassing en service manifesteert zich. De schemadefinitie voor de bestanden ApplicationManifest.xml en ServiceManifest.xml is met de Service Fabric SDK geïnstalleerd in *C:\Program Files\Microsoft SDKs\Service Fabric\schema's\ServiceFabricServiceModel.xsd*.

* **Aanvraagmanifest** Het toepassingsmanifest wordt gebruikt om de toepassing te beschrijven. Het bevat de services die deze samenstellen en andere parameters die worden gebruikt om te definiëren hoe een of meer services moeten worden geïmplementeerd, zoals het aantal exemplaren.

  In Service Fabric is een toepassing een eenheid van implementatie en upgrade. Een toepassing kan worden geüpgraded als één eenheid waar potentiële fouten en potentiële rollbacks worden beheerd. Service Fabric garandeert dat het upgradeproces succesvol is of, als de upgrade mislukt, de toepassing niet in een onbekende of instabiele toestand verlaat.
* **Servicemanifest** Het servicemanifest beschrijft de onderdelen van een service. Het bevat gegevens, zoals de naam en het type service, en de code en configuratie ervan. Het servicemanifest bevat ook enkele aanvullende parameters die kunnen worden gebruikt om de service te configureren zodra deze is geïmplementeerd.

## <a name="application-package-file-structure"></a>Bestandsstructuur van toepassingspakket
Als u een toepassing wilt implementeren in Service Fabric, moet de toepassing een vooraf gedefinieerde directorystructuur volgen. Het volgende is een voorbeeld van die structuur.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

De ApplicationPackageRoot bevat het bestand ApplicationManifest.xml dat de toepassing definieert. Een submap voor elke service die in de toepassing is opgenomen, wordt gebruikt om alle artefacten te bevatten die de service vereist. Deze submappen zijn servicemanifest.xml en, typisch, de volgende:

* *Code*. Deze map bevat de servicecode.
* *Config*. Deze map bevat een Bestand Settings.xml (en indien nodig andere bestanden) dat de service tijdens runtime kan openen om specifieke configuratie-instellingen op te halen.
* *Gegevens*. Dit is een extra map om extra lokale gegevens op te slaan die de service mogelijk nodig heeft. Gegevens moeten worden gebruikt om alleen kortstondige gegevens op te slaan. Service Fabric kopieert of repliceert geen wijzigingen in de gegevensmap als de service moet worden verplaatst (bijvoorbeeld tijdens failover).

> [!NOTE]
> Je hoeft niet om `config` de `data` en mappen te maken als je ze niet nodig hebt.
>
>

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor gerelateerde informatie en taken.
* [Een toepassing implementeren die door een gast kan worden uitgevoerd](service-fabric-deploy-existing-app.md)
* [Meerdere toepassingen implementeren die door gasten kunnen worden uitgevoerd](service-fabric-deploy-multiple-apps.md)
* [Maak uw eerste gast uitvoerbare toepassing met Visual Studio](quickstart-guest-app.md)
* [Monster voor het verpakken en implementeren van een gast uitvoerbaar,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)inclusief een link naar de prerelease van de verpakkingstool
* [Voorbeeld van twee gastexecutables (C# en nodejs) die communiceren via de naamgevingsservice via REST](https://github.com/Azure-Samples/service-fabric-containers)

