---
title: Azure Cloud Services definition-schema (csdef-bestand) | Microsoft Docs
description: Een bestand met een service definitie (. csdef) definieert een service model voor een toepassing, met beschik bare rollen, eind punten en configuratie waarden voor de service.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528367"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services definition-schema (csdef-bestand)
Het service definitie bestand definieert het service model voor een toepassing. Het bestand bevat de definities voor de functies die beschikbaar zijn voor een Cloud service, geeft de service-eind punten op en stelt configuratie-instellingen voor de service in. Waarden voor configuratie-instellingen worden ingesteld in het service configuratie bestand, zoals beschreven in het [Configuratie schema van de Cloud service (klassiek)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Het Azure Diagnostics-configuratie schema bestand wordt standaard geïnstalleerd in de `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` map. Vervang `<version>` door de geïnstalleerde versie van de [Azure SDK](https://www.windowsazure.com/develop/downloads/).

De standaard extensie voor het service definitie bestand is. csdef.

## <a name="basic-service-definition-schema"></a>Basis schema voor service definitie
Het service definitie bestand moet één `ServiceDefinition` element bevatten. De service definitie moet ten minste één Role-element`WebRole` ( `WorkerRole`of) bevatten. Het kan Maxi maal 25 rollen bevatten die in één definitie zijn gedefinieerd, en u kunt typen van rollen combi neren. De service definitie bevat ook het optionele `NetworkTrafficRules` element waarmee wordt beperkt welke rollen kunnen communiceren met opgegeven interne eind punten. De service definitie bevat ook het optionele `LoadBalancerProbes` element dat door de klant gedefinieerde status controles van eind punten bevat.

De basis indeling van het service definitie bestand is als volgt.

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

## <a name="schema-definitions"></a>Schema definities
In de volgende onderwerpen wordt het schema beschreven:

- [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)
- [WebRole-schema](schema-csdef-webrole.md)
- [WorkerRole-schema](schema-csdef-workerrole.md)
- [NetworkTrafficRules-schema](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>ServiceDefinition-element
Het `ServiceDefinition` element is het element op het hoogste niveau van het service definitie bestand.

In de volgende tabel worden de kenmerken van `ServiceDefinition` het element beschreven.

| Kenmerk               | Beschrijving |
| ----------------------- | ----------- |
| name                    |Vereist. De naam van de service. De naam moet uniek zijn binnen het service account.|
| topologyChangeDiscovery | Optioneel. Hiermee geeft u het type melding voor de topologie wijziging op. Mogelijke waarden zijn:<br /><br /> -   `Blast`-Hiermee wordt de update zo snel mogelijk verzonden naar alle rolinstanties. Als u optie kiest, moet de rol de update van de topologie kunnen afhandelen zonder opnieuw te worden opgestart.<br />-   `UpgradeDomainWalk`– Verzendt de update naar elke rolinstantie op een opeenvolgende manier nadat het vorige exemplaar de update heeft geaccepteerd.|
| schemaVersion           | Optioneel. Hiermee geeft u de versie van het service definitie schema op. Met de schema versie kan Visual Studio de juiste SDK-hulpprogram ma's selecteren die voor schema validatie moeten worden gebruikt als meer dan één versie van de SDK naast elkaar is geïnstalleerd.|
| upgradeDomainCount      | Optioneel. Hiermee geeft u het aantal upgrade domeinen op waarmee rollen in deze service worden toegewezen. Rolinstanties worden toegewezen aan een upgrade domein wanneer de service wordt geïmplementeerd. Zie [Update a Cloud Service Role of Deployment](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment)( [de beschik baarheid van virtuele machines beheren](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) en [Wat is een Cloud service model](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package)) voor meer informatie.<br /><br /> U kunt Maxi maal 20 upgrade domeinen opgeven. Als u niets opgeeft, is het standaard aantal upgrade domeinen 5.|
