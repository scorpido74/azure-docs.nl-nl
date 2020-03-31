---
title: Toegangsbeleid toewijzen aan serviceeindpunten
description: Meer informatie over het toewijzen van beveiligingstoegangsbepolitieagenten aan HTTP- of HTTPS-eindpunten in uw Service Fabric-service.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614652"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Een beveiligingstoegangsbeleid toewijzen voor HTTP- en HTTPS-eindpunten
Als u een run-as-beleid toepast en het servicemanifest HTTP-eindpuntbronnen declareert, moet u een **SecurityAccessPolicy**opgeven.  **SecurityAccessPolicy** zorgt ervoor dat poorten die aan deze eindpunten zijn toegewezen, correct zijn beperkt tot het gebruikersaccount dat de service als wordt uitgevoerd. Anders heeft **http.sys** geen toegang tot de service en krijg je fouten met oproepen van de client. In het volgende voorbeeld wordt het Customer1-account gebruikt op een eindpunt genaamd **EndpointName**, waardoor het volledige toegangsrechten krijgt.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Geef voor een HTTPS-eindpunt ook de naam aan van het certificaat om terug te keren naar de client. U verwijst naar het certificaat met **endpointbindingbeleid**.  Het certificaat wordt gedefinieerd in de sectie **Certificaten** van het aanvraagmanifest.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> Gebruik bij het gebruik van HTTPS niet dezelfde poort en hetzelfde certificaat voor verschillende service-exemplaren (onafhankelijk van de toepassing) die naar hetzelfde knooppunt worden geïmplementeerd. Als u twee verschillende services met dezelfde poort in verschillende toepassingsinstanties upgradet, wordt een upgradefout uitgevoerd. Zie [Meerdere toepassingen upgraden met HTTPS-eindpunten ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)voor meer informatie.
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Lees voor de volgende stappen de volgende artikelen:
* [Het toepassingsmodel begrijpen](service-fabric-application-model.md)
* [Resources opgeven in een servicemanifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
