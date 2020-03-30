---
title: Voorbeelden van azure Service Fabric-toepassingsmanifesten
description: Meer informatie over het configureren van toepassings- en servicemanifestinstellingen voor een Service Fabric-toepassing.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451645"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Voorbeelden van servicefabric-toepassingen en servicemanifesten
Deze sectie bevat voorbeelden van toepassings- en servicemanifesten. Deze voorbeelden zijn niet bedoeld om belangrijke scenario's weer te geven, maar om de verschillende instellingen weer te geven die beschikbaar zijn en hoe ze te gebruiken. 

Het volgende is een index van de getoonde functies en het voorbeeldmanifest(en) waar ze deel van uitmaken.

|Functie|Manifest|
|---|---|
|[Resourcebeheer](service-fabric-resource-governance.md)|[Reliable Services Application manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [Container Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Een service uitvoeren als een lokaal beheerdersaccount](service-fabric-application-runas-security.md)|[Manifest van de toepassing van betrouwbare services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Een standaardbeleid toepassen op alle servicecodepakketten](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifest van de toepassing van betrouwbare services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Gebruikers- en groepsprincipals maken](service-fabric-application-runas-security.md)|[Manifest van de toepassing van betrouwbare services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|een gegevenspakket delen tussen service-exemplaren|[Manifest van de toepassing van betrouwbare services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Serviceeindpunten overschrijven](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifest van de toepassing van betrouwbare services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Een script uitvoeren bij het opstarten van de service](service-fabric-run-script-at-service-startup.md)|[VotingWeb service manifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Een HTTPS-eindpunt definiëren](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb service manifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Een config-pakket declareren](service-fabric-application-and-service-manifests.md)|[VotingData service manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Een gegevenspakket declareren](service-fabric-application-and-service-manifests.md)|[VotingData service manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Omgevingsvariabelen overschrijven](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Containertoepassingsmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Toewijzing van containerpoort naar host configureren](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Containertoepassingsmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Verificatie containerregister configureren](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Containertoepassingsmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Isolatiemodus instellen](service-fabric-get-started-containers.md#configure-isolation-mode)|[Containertoepassingsmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Os-buildspecifieke containerafbeeldingen opgeven](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Containertoepassingsmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Omgevingsvariabelen instellen](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Container FrontEndService-servicemanifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Container BackEndService-servicemanifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Een eindpunt configureren](service-fabric-get-started-containers.md#configure-communication)|[Container FrontEndService service manifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Container BackEndService service manifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData service manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|opdrachten doorgeven aan de container|[Container FrontEndService-servicemanifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Een certificaat importeren in een container](service-fabric-securing-containers.md)|[Container FrontEndService-servicemanifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Volumestuurprogramma configureren](service-fabric-containers-volume-logging-drivers.md)|[Container BackEndService-servicemanifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

