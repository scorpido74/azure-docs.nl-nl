---
title: Geavanceerde aanvraagbeperking met Azure API Management
description: Meer informatie over het maken en toepassen van flexibel quota- en tariefbeperkende beleidsregels met Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 467d9cee74567fc0d19031773415675ae7c51818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71066757"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Geavanceerde aanvraagbeperking met Azure API Management
Het kunnen beperken van binnenkomende aanvragen is een belangrijke rol van Azure API Management. Door de snelheid van aanvragen te controleren of door de totale overdracht van aanvragen/gegevens te controleren, stelt API Management API-providers in staat om hun API's te beschermen tegen misbruik en waarde te creëren voor verschillende API-productlagen.

## <a name="product-based-throttling"></a>Productgebaseerde beperking
Tot op heden zijn de beperkingsmogelijkheden beperkt tot een bepaald productabonnement, gedefinieerd in de Azure-portal. Dit is handig voor de API-provider om limieten toe te passen op de ontwikkelaars die zich hebben aangemeld om hun API te gebruiken, maar het helpt niet, bijvoorbeeld bij het beperken van individuele eindgebruikers van de API. Het is mogelijk dat voor een gebruiker van de toepassing van de ontwikkelaar om het volledige quotum te verbruiken en vervolgens te voorkomen dat andere klanten van de ontwikkelaar in staat zijn om de toepassing te gebruiken. Ook kunnen verschillende klanten die een groot aantal aanvragen genereren, de toegang beperken tot incidentele gebruikers.

## <a name="custom-key-based-throttling"></a>Op aangepaste sleutel gebaseerde beperking

> [!NOTE]
> De `rate-limit-by-key` `quota-by-key` beleidsregels en het beleid zijn niet beschikbaar in de verbruikslaag van Azure API Management. 

Het nieuwe [beleid per sleutel voor tarieflimiet](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) en quota per [sleutel](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) biedt een flexibelere oplossing voor de verkeersleiding. Met dit nieuwe beleid u expressies definiëren om de sleutels te identificeren die worden gebruikt om het verkeersgebruik bij te houden. De manier waarop dit werkt is het gemakkelijkst geïllustreerd met een voorbeeld. 

## <a name="ip-address-throttling"></a>Beperking van IP-adres
Het volgende beleid beperkt een IP-adres van één client tot slechts 10 oproepen per minuut, met een totaal van 1.000.000 oproepen en 10.000 kilobytes bandbreedte per maand. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Als alle clients op het internet een uniek IP-adres hebben gebruikt, kan dit een effectieve manier zijn om het gebruik per gebruiker te beperken. Het is echter waarschijnlijk dat meerdere gebruikers één openbaar IP-adres delen omdat ze via een NAT-apparaat toegang hebben tot internet. Ondanks dit, voor API's die `IpAddress` niet-geverifieerde toegang mogelijk maken, is dit misschien de beste optie.

## <a name="user-identity-throttling"></a>Beperking van de identiteit van de gebruiker
Als een eindgebruiker is geverifieerd, kan een beperkingssleutel worden gegenereerd op basis van informatie die die gebruiker op unieke wijze identificeert.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

In dit voorbeeld ziet u hoe u `JWT` de kopautorisatie extraheren, converteren naar een object en het onderwerp van het token gebruiken om de gebruiker te identificeren en dat als de snelheidsbeperkende sleutel gebruiken. Als de gebruikersidentiteit wordt `JWT` opgeslagen in de als een van de andere claims, dan is die waarde kan worden gebruikt in de plaats.

## <a name="combined-policies"></a>Gecombineerd beleid
Hoewel het nieuwe beperkingsbeleid meer controle biedt dan het bestaande beperkingsbeleid, is er nog steeds waarde die beide mogelijkheden combineert. Beperking op productabonnementssleutel[(Gesprekstarief beperken per abonnement](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) en [Gebruiksquotum instellen per abonnement)](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)is een geweldige manier om inkomsten te genereren met een API door kosten in rekening te brengen op basis van gebruiksniveaus. De fijnere korrelcontrole van het kunnen gaspedaal door gebruiker is complementair en verhindert het gedrag van één gebruiker van het degraderen van de ervaring van een andere. 

## <a name="client-driven-throttling"></a>Clientgestuurde beperking
Wanneer de beperkingssleutel wordt gedefinieerd met behulp van een [beleidsexpressie,](/azure/api-management/api-management-policy-expressions)is het de API-provider die kiest hoe de beperking wordt gedefinieerd. Een ontwikkelaar kan echter bepalen hoe ze hun eigen klanten beperken. Dit kan worden ingeschakeld door de API-provider door de invoering van een aangepaste header om de clienttoepassing van de ontwikkelaar in staat te stellen de sleutel naar de API te communiceren.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Hierdoor kan de clienttoepassing van de ontwikkelaar kiezen hoe ze de snelheidsbeperkende sleutel willen maken. De clientontwikkelaars kunnen hun eigen tarieflagen maken door sets sleutels toe te wijzen aan gebruikers en het sleutelgebruik te roteren.

## <a name="summary"></a>Samenvatting
Azure API Management biedt tarief- en offertebeperking om zowel uw API-service te beschermen als waarde toe te voegen. Met het nieuwe beperkingsbeleid met aangepaste scopingregels u meer controle hebben over dit beleid, zodat uw klanten nog betere toepassingen kunnen bouwen. De voorbeelden in dit artikel tonen het gebruik van dit nieuwe beleid aan door sleutels te beperken met ip-adressen van klanten, gebruikersidentiteit en door de client gegenereerde waarden. Er zijn echter veel andere delen van het bericht die kunnen worden gebruikt, zoals user agent, URL-padfragmenten, berichtgrootte.

## <a name="next-steps"></a>Volgende stappen
Geef ons uw feedback als een GitHub probleem voor dit onderwerp. Het zou geweldig zijn om te horen over andere potentiële kernwaarden die een logische keuze zijn geweest in uw scenario's.

