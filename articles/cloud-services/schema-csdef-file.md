---
title: Azure Cloud Services Definition Schema (.csdef-bestand) | Microsoft Documenten
description: Een servicedefinitiebestand (.csdef) definieert een servicemodel voor een toepassing met beschikbare rollen, eindpunten en configuratiewaarden voor de service.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528367"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services-definitieschema (.csdef-bestand)
Het servicedefinitiebestand definieert het servicemodel voor een toepassing. Het bestand bevat de definities voor de rollen die beschikbaar zijn voor een cloudservice, geeft de serviceeindpunten op en stelt configuratie-instellingen voor de service in. De configuratie-instellingswaarden worden ingesteld in het serviceconfiguratiebestand, zoals beschreven in het [Configuratieschema (Cloud Service).](/previous-versions/azure/reference/ee758710(v=azure.100))

Standaard is het configuratieschemabestand azure diagnostics `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` geïnstalleerd in de map. Vervang `<version>` door de geïnstalleerde versie van de [Azure SDK](https://www.windowsazure.com/develop/downloads/).

De standaardextensie voor het servicedefinitiebestand is .csdef.

## <a name="basic-service-definition-schema"></a>Basisservicedefinitieschema
Het servicedefinitiebestand moet `ServiceDefinition` één element bevatten. De servicedefinitie moet ten minste`WebRole` `WorkerRole`één rol (of ) element bevatten. Het kan maximaal 25 rollen bevatten die in één definitie zijn gedefinieerd en u roltypen combineren. De servicedefinitie bevat `NetworkTrafficRules` ook het optionele element dat beperkt welke rollen kunnen communiceren met opgegeven interne eindpunten. De servicedefinitie bevat `LoadBalancerProbes` ook het optionele element dat door de klant gedefinieerde statussondes van eindpunten bevat.

De basisindeling van het servicedefinitiebestand is als volgt.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Schemadefinities
De volgende onderwerpen beschrijven het schema:

- [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)
- [WebRole-schema](schema-csdef-webrole.md)
- [WorkerRole-schema](schema-csdef-workerrole.md)
- [NetworkTrafficRules-schema](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>Element ServiceDedefinitie
Het `ServiceDefinition` element is het element op het hoogste niveau van het servicedefinitiebestand.

In de volgende tabel worden `ServiceDefinition` de kenmerken van het element beschreven.

| Kenmerk               | Beschrijving |
| ----------------------- | ----------- |
| name                    |Vereist. De naam van de service. De naam moet uniek zijn binnen het serviceaccount.|
| topologyChangeDiscovery | Optioneel. Hiermee geeft u het type topologiewijzigingsmelding op. Mogelijke waarden zijn:<br /><br /> -   `Blast`- Stuurt de update zo snel mogelijk naar alle rolinstanties. Als u de optie kiest, moet de rol de topologie-update kunnen verwerken zonder opnieuw te worden gestart.<br />-   `UpgradeDomainWalk`– Hiermee verzendt u de update op een sequentiële manier naar elke rolinstantie nadat de vorige instantie de update heeft geaccepteerd.|
| schemaVersie           | Optioneel. Hiermee geeft u de versie van het servicedefinitieschema op. Met de schemaversie kan Visual Studio de juiste SDK-hulpprogramma's selecteren die u gebruiken voor schemavalidatie als er meer dan één versie van de SDK naast elkaar is geïnstalleerd.|
| upgradeDomainCount      | Optioneel. Hiermee geeft u het aantal upgradedomeinen op over welke rollen in deze service worden toegewezen. Rolexemplaren worden toegewezen aan een upgradedomein wanneer de service wordt geïmplementeerd. Zie [Een cloudservicerol of -implementatie bijwerken](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [De beschikbaarheid van virtuele machines beheren](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) en Wat is een Cloud Service [Model](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package)voor meer informatie.<br /><br /> U maximaal 20 upgradedomeinen opgeven. Als dit niet is opgegeven, is het standaardaantal upgradedomeinen 5.|
