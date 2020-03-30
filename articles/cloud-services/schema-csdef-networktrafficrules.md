---
title: Azure Cloud Services Def. NetworkTrafficRules-schema | Microsoft Documenten
description: Meer informatie over NetworkTrafficRules, die de rollen beperkt die toegang hebben tot de interne eindpunten van een rol. Het combineert met rollen in een servicedefinitiebestand.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534725"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules-schema
Het `NetworkTrafficRules` knooppunt is een optioneel element in het servicedefinitiebestand dat aangeeft hoe rollen met elkaar communiceren. Het beperkt welke rollen toegang hebben tot de interne eindpunten van de specifieke rol. Het `NetworkTrafficRules` is geen standalone element; het wordt gecombineerd met twee of meer rollen in een servicedefinitiebestand.

De standaardextensie voor het servicedefinitiebestand is .csdef.

> [!NOTE]
>  Het `NetworkTrafficRules` knooppunt is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Basisservicedefinitieschema voor de netwerkverkeersregels
De basisindeling van een servicedefinitiebestand met definities van netwerkverkeer is als volgt.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema-elementen
Het `NetworkTrafficRules` knooppunt van het servicedefinitiebestand bevat deze elementen, die in de volgende secties in dit onderwerp in detail worden beschreven:

[Element Netwerkverkeersregels](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Bestemmingselement](#Destinations)

[RoleEndpoint-element](#RoleEndpoint)

AllowAllTraffic Element

[WanneerBron-element](#WhenSource)

[FromRole-element](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>Element Netwerkverkeersregels
Het `NetworkTrafficRules` element geeft aan met welk eindpunt op een andere rol kan worden gecommuniceerd. Een service kan `NetworkTrafficRules` één definitie bevatten.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo Element
Het `OnlyAllowTrafficTo` element beschrijft een verzameling bestemmingseindpunten en de rollen die met hen kunnen communiceren. U `OnlyAllowTrafficTo` meerdere knooppunten opgeven.

##  <a name="destinations-element"></a><a name="Destinations"></a>Bestemmingselement
Het `Destinations` element beschrijft een verzameling RoleEndpoints dan kan worden gecommuniceerd.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>RoleEndpoint-element
Het `RoleEndpoint` element beschrijft een eindpunt voor een rol waarmee communicatie kan worden toegestaan. U `RoleEndpoint` meerdere elementen opgeven als er meer dan één eindpunt in de rol is.

| Kenmerk      | Type     | Beschrijving |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Vereist. De naam van het eindpunt om verkeer toe te staan.|
| `roleName`     | `string` | Vereist. De naam van de webrol om communicatie toe te staan.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
Het `AllowAllTraffic` element is een regel waarmee alle rollen kunnen communiceren `Destinations` met de eindpunten die in het knooppunt zijn gedefinieerd.

##  <a name="whensource-element"></a><a name="WhenSource"></a>WanneerBron-element
Het `WhenSource` element beschrijft een verzameling rollen dan kan communiceren `Destinations` met de eindpunten die in het knooppunt zijn gedefinieerd.

| Kenmerk | Type     | Beschrijving |
| --------- | -------- | ----------- |
| `matches` | `string` | Vereist. Hiermee geeft u de regel op die moet worden toegepast bij het toestaan van communicatie. De enige geldige `AnyRule`waarde is momenteel .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>FromRole-element
Het `FromRole` element geeft de rollen op die kunnen `Destinations` communiceren met de eindpunten die in het knooppunt zijn gedefinieerd. U `FromRole` meerdere elementen opgeven als er meer dan één rol is die met de eindpunten kan communiceren.

| Kenmerk  | Type     | Beschrijving |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Vereist. De naam voor rol van waaruit communicatie mogelijk te maken.|

## <a name="see-also"></a>Zie ook
[Cloud Service (klassiek) definitieschema](schema-csdef-file.md)




