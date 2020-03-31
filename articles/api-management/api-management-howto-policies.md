---
title: Beleidsregels in Azure API-beheer | Microsoft Documenten
description: Meer informatie over het maken, bewerken en configureren van beleid in API-beheer.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: c10939b50a66cd608d27a71f02d959fbc2380f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072310"
---
# <a name="policies-in-azure-api-management"></a>Beleidsregels in Azure API Management

In Azure API Management (APIM) zijn beleidsregels een krachtige mogelijkheid van het systeem waarmee de uitgever het gedrag van de API kan wijzigen door middel van configuratie. Beleidsregels zijn een verzameling instructies die achtereenvolgens worden uitgevoerd op verzoek of antwoord van een API. Populaire instructies omvatten indelingconversie van XML naar JSON en gesprekssnelheid beperken om het aantal inkomende oproepen van een ontwikkelaar te beperken. Er zijn veel meer beleidsregels beschikbaar.

Beleidsregels worden toegepast binnen de gateway die zich bevindt tussen de API-consument en de beheerde API. De gateway ontvangt alle aanvragen en stuurt deze meestal ongewijzigd door naar de onderliggende API. Een beleid kan echter wijzigingen toepassen op zowel de binnenkomende aanvraag als het uitgaande antwoord.

Beleidsexpressies kunnen worden gebruikt als kenmerkwaarden of tekstwaarden in API Management-beleidsregels, tenzij het beleid iets anders aangeeft. Sommige beleidsregels, zoals de beleidsregels [Stroom controleren][Control flow] en [Variabele instellen][Set variable], zijn gebaseerd op beleidsexpressies. Zie [Geavanceerde beleidsregels][Advanced policies] en [Beleidsexpressies][Policy expressions] voor meer informatie.

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Beleidsconfiguratie begrijpen

De beleidsdefinitie is een eenvoudig XML-document dat een reeks binnenkomende en uitgaande instructies beschrijft. De XML kan rechtstreeks in het definitievenster worden bewerkt. Een lijst met verklaringen wordt aan de rechterkant verstrekt en verklaringen die van toepassing zijn op het huidige bereik worden ingeschakeld en gemarkeerd.

Als u op een ingeschakelde instructie klikt, wordt de juiste XML toegevoegd op de locatie van de cursor in de definitieweergave. 

> [!NOTE]
> Als het beleid dat u wilt toevoegen niet is ingeschakeld, moet u ervoor zorgen dat u zich binnen de juiste scope voor dat beleid bevindt. Elke beleidsverklaring is ontworpen voor gebruik in bepaalde scopes en beleidsonderdelen. Als u de beleidsonderdelen en -kaders voor een beleid wilt controleren, raadpleegt u de sectie **Gebruik** voor dat beleid in de [beleidsverwijzing][Policy Reference].
> 
> 

De configuratie is `inbound` `backend`onderverdeeld `outbound`in `on-error`, , en . De reeks opgegeven beleidsoverzichten wordt uitgevoerd om een aanvraag en een antwoord te kunnen ontvangen.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Als er een fout optreedt tijdens de verwerking van `inbound` `backend`een `outbound` aanvraag, worden alle resterende stappen in `on-error` de , of secties overgeslagen en springt de uitvoering naar de instructies in de sectie. Door beleidsoverzichten `on-error` in de sectie te plaatsen, u de fout bekijken door de `context.LastError` eigenschap te gebruiken, de foutfoutfout te inspecteren en aan te passen met behulp van het `set-body` beleid en configureren wat er gebeurt als er een fout optreedt. Er zijn foutcodes voor ingebouwde stappen en voor fouten die kunnen optreden tijdens de verwerking van beleidsinstructies. Zie [Foutafhandeling in API-beheerbeleid](/azure/api-management/api-management-error-handling-policies)voor meer informatie .

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Beleid configureren

Zie Beleid instellen of bewerken voor informatie over het configureren van [beleid.](set-edit-policies.md)

## <a name="policy-reference"></a>Beleidsreferentie

Zie de [beleidsverwijzing](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen.

## <a name="policy-samples"></a>Beleidsvoorbeelden

Zie [Beleidsvoorbeelden](policy-samples.md) voor meer codevoorbeelden.

## <a name="examples"></a>Voorbeelden

### <a name="apply-policies-specified-at-different-scopes"></a>Beleid toepassen dat op verschillende scopes is opgegeven

Als u een beleid op globaal niveau hebt en een beleid dat is geconfigureerd voor een API, worden beide beleidsregels toegepast wanneer die specifieke API wordt gebruikt. API Management maakt het mogelijk om gecombineerde beleidsoverzichten via het basiselement te bepalen. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

In de bovenstaande voorbeeldbeleidsdefinitie wordt de `cross-domain` instructie uitgevoerd voordat een hoger `find-and-replace` beleid wordt uitgevoerd dat op zijn beurt door het beleid zou worden gevolgd. 

### <a name="restrict-incoming-requests"></a>Binnenkomende aanvragen beperken

Als u een nieuwe instructie wilt toevoegen om binnenkomende aanvragen te beperken `inbound` tot opgegeven IP-adressen, plaatst u de cursor net binnen de inhoud van het XML-element en klikt u op de instructie **Beller-IP-adressen beperken.**

![Beperkingsbeleid][policies-restrict]

Hiermee wordt een XML-fragment toegevoegd aan het `inbound` element dat richtlijnen biedt voor het configureren van de instructie.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Als u binnenkomende aanvragen wilt beperken en alleen die van een IP-adres van 1.2.3.4 wilt accepteren, wijzigt u de XML als volgt:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beleid:

+ [API's transformeren](transform-api.md)
+ [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
+ [Beleidsvoorbeelden](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
