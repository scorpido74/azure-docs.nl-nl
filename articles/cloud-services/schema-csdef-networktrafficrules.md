---
title: Azure Cloud Services def. NetworkTrafficRules-schema | Microsoft Docs
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
ms.openlocfilehash: e6d156810b9fdee69ddac122eec06db7267ddf36
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449040"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>NetworkTrafficRules-schema voor Azure Cloud Services definition
Het knoop punt `NetworkTrafficRules` is een optioneel element in het service definitie bestand dat aangeeft hoe rollen met elkaar communiceren. Hiermee wordt beperkt welke rollen toegang hebben tot de interne eind punten van de specifieke rol. De `NetworkTrafficRules` is geen zelfstandig element. het wordt gecombineerd met twee of meer rollen in een service definitie bestand.

De standaard extensie voor het service definitie bestand is. csdef.

> [!NOTE]
>  Het knoop punt `NetworkTrafficRules` is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Basis schema van de service definitie voor de regels voor netwerk verkeer
De basis indeling van een service definitie bestand met definities van netwerk verkeer is als volgt.

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
Het knoop punt `NetworkTrafficRules` van het service definitie bestand bevat deze elementen, zoals beschreven in de volgende secties in dit onderwerp:

[NetworkTrafficRules-element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Doel element](#Destinations)

[RoleEndpoint-element](#RoleEndpoint)

AllowAllTraffic-element

[WhenSource-element](#WhenSource)

[FromRole-element](#FromRole)

##  <a name="NetworkTrafficRules"></a>NetworkTrafficRules-element
Het `NetworkTrafficRules` element geeft aan welke rollen met welk eind punt met een andere rol kunnen communiceren. Een service kan een `NetworkTrafficRules` definitie bevatten.

##  <a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo-element
Het `OnlyAllowTrafficTo`-element beschrijft een verzameling bestemmings eindpunten en de rollen die met hen kunnen communiceren. U kunt meerdere `OnlyAllowTrafficTo` knooppunten opgeven.

##  <a name="Destinations"></a>Doel element
Het `Destinations`-element beschrijft een verzameling RoleEndpoints dan kan worden gecommuniceerd.

##  <a name="RoleEndpoint"></a>RoleEndpoint-element
Het `RoleEndpoint`-element beschrijft een eind punt voor een rol om communicatie met toe te staan. U kunt meerdere `RoleEndpoint` elementen opgeven als er meer dan één eind punt voor de rol is.

| Kenmerk      | Type     | Beschrijving |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Vereist. De naam van het eind punt waarnaar verkeer moet worden toegestaan.|
| `roleName`     | `string` | Vereist. De naam van de webrole waarmee communicatie moet worden toegestaan.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic-element
Het `AllowAllTraffic`-element is een regel waarmee alle rollen kunnen communiceren met de eind punten die in het `Destinations` knooppunt zijn gedefinieerd.

##  <a name="WhenSource"></a>WhenSource-element
Het `WhenSource`-element beschrijft een verzameling rollen dan kan communiceren met de eind punten die in het knoop punt `Destinations` zijn gedefinieerd.

| Kenmerk | Type     | Beschrijving |
| --------- | -------- | ----------- |
| `matches` | `string` | Vereist. Hiermee geeft u de regel op die moet worden toegepast bij het toestaan van communicaties. De enige geldige waarde is momenteel `AnyRule`.|
  
##  <a name="FromRole"></a>FromRole-element
Met het `FromRole` element worden de rollen opgegeven die kunnen communiceren met de eind punten die in het `Destinations` knooppunt zijn gedefinieerd. U kunt meerdere `FromRole` elementen opgeven als er meer dan één rol met de eind punten kan communiceren.

| Kenmerk  | Type     | Beschrijving |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Vereist. De naam van de rol waarmee communicatie moet worden toegestaan.|

## <a name="see-also"></a>Zie ook
[Schema voor Cloud service (klassiek)](schema-csdef-file.md)




