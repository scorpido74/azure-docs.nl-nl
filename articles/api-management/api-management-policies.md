---
title: Azure API-beheerbeleid | Microsoft Documenten
description: Meer informatie over het beschikbare beleid voor gebruik in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71219464"
---
# <a name="api-management-policies"></a>API Management-beleidsregels
In deze sectie vindt u een verwijzing naar het volgende API-beheerbeleid. Zie [Beleid in API-beheer](api-management-howto-policies.md)voor informatie over het toevoegen en configureren van beleidsregels.  
  
 Beleidsregels zijn een krachtige mogelijkheid van het systeem waarmee de uitgever het gedrag van de API kan wijzigen door middel van configuratie. Beleidsregels zijn een verzameling instructies die achtereenvolgens worden uitgevoerd op verzoek of antwoord van een API. Populaire instructies omvatten indelingconversie van XML naar JSON en gesprekssnelheid beperken om het aantal inkomende oproepen van een ontwikkelaar te beperken. Er zijn veel meer beleidsregels beschikbaar.  
  
 Beleidsexpressies kunnen worden gebruikt als kenmerkwaarden of tekstwaarden in API Management-beleidsregels, tenzij het beleid iets anders aangeeft. Sommige beleidsregels, zoals de beleidsregels [Stroom controleren](api-management-advanced-policies.md#choose) en [Variabele instellen](api-management-advanced-policies.md#set-variable), zijn gebaseerd op beleidsexpressies. Zie [Geavanceerde beleidsregels](api-management-advanced-policies.md#AdvancedPolicies) en [Beleidsexpressies](api-management-policy-expressions.md) voor meer informatie.  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a>Beleid  
  
-   [Beleid voor toegangsbeperking](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Http-header controleren](api-management-access-restriction-policies.md#CheckHTTPHeader) - Hiermee wordt het bestaan en/of de waarde van een HTTP-koptekst afgedwongen.  
    -   [Belsnelheid per abonnement beperken](api-management-access-restriction-policies.md#LimitCallRate) - Voorkomt pieken in API-gebruik door de gesprekssnelheid per abonnement te beperken.  
    -   [Beperk de aanroepsnelheid per sleutel](api-management-access-restriction-policies.md#LimitCallRateByKey) - Voorkomt pieken in API-gebruik door de gesprekssnelheid per sleutel te beperken.  
    -   [Beller-IP's beperken](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filters (staat/weigert) oproepen van specifieke IP-adressen en/of adresbereiken.  
    -   [Gebruiksquotum instellen op abonnementsbasis](api-management-access-restriction-policies.md#SetUsageQuota) - Hiermee u een contractvolume voor hernieuwbare of levenslange oproepen en/of bandbreedtequota per abonnement afdwingen.  
    -   [Gebruiksquotum instellen op sleutel](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - Hiermee u een contractvolume voor hernieuwbare of levenslange oproepen en/of bandbreedtequota per sleutel afdwingen.  
    -   [JWT valideren](api-management-access-restriction-policies.md#ValidateJWT) - Dwingt het bestaan en de geldigheid van een JWT af die is geëxtraheerd uit een opgegeven HTTP-koptekst of een opgegeven queryparameter.  
-   [Geavanceerde beleidsregels](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Controlestroom](api-management-advanced-policies.md#choose) - Past beleidsoverzichten voorwaardelijk toe op basis van de evaluatie van Booleaanse expressies.  
    -   [Aanvraag doorsturen](api-management-advanced-policies.md#ForwardRequest) : doorstuurt het verzoek naar de backendservice.
    -   [Gelijktijdigheid beperken](api-management-advanced-policies.md#LimitConcurrency) : voorkomt dat gesloten beleid wordt uitgevoerd met meer dan het opgegeven aantal aanvragen tegelijk.
    -   [Log naar gebeurtenishub](api-management-advanced-policies.md#log-to-eventhub) - Verzendt berichten in de opgegeven indeling naar een berichtdoel dat is gedefinieerd door een loggerentiteit.
    -   [Mock response](api-management-advanced-policies.md#mock-response) - Hiermee wordt de uitvoering van de pijplijn afgebroken en wordt een bespot antwoord rechtstreeks naar de beller geretourneerd.
    -   [Opnieuw proberen](api-management-advanced-policies.md#Retry) - Herprobeert de uitvoering van de bijgevoegde beleidsoverzichten opnieuw, als en totdat aan de voorwaarde is voldaan. De uitvoering wordt herhaald met de opgegeven tijdsintervallen en tot het opgegeven aantal opnieuw proberen.  
    -   [Retourrespons](api-management-advanced-policies.md#ReturnResponse) - Hiermee wordt de uitvoering van de pijplijn afgebroken en wordt het opgegeven antwoord rechtstreeks naar de beller geretourneerd.  
    -   [Eenenkele reisaanvraag verzenden](api-management-advanced-policies.md#SendOneWayRequest) - Stuurt een aanvraag naar de opgegeven URL zonder te wachten op een antwoord.  
    -   [Aanvraag verzenden](api-management-advanced-policies.md#SendRequest) - Stuurt een aanvraag naar de opgegeven URL.
    -   [HTTP-proxy instellen](api-management-advanced-policies.md#SetHttpProxy) - Hiermee u doorgestuurde aanvragen doorsturen via een HTTP-proxy.
    -   [Variabele instellen](api-management-advanced-policies.md#set-variable) : een waarde in een benoemde contextvariabele voor latere toegang blijven behouden.  
    -   [Aanvraagmethode instellen](api-management-advanced-policies.md#SetRequestMethod) - Hiermee u de HTTP-methode voor een aanvraag wijzigen.  
    -   [Statuscode instellen](api-management-advanced-policies.md#SetStatus) : hiermee wijzigt u de HTTP-statuscode in de opgegeven waarde.  
    -   [Trace](api-management-advanced-policies.md#Trace) - Voegt aangepaste traces toe aan de [API Inspector-uitvoer,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) Application Insights-telemetrieën en diagnostische logboeken.  
    -   [Wacht](api-management-advanced-policies.md#Wait) - Wacht op ingesloten [aanvraag verzenden](api-management-advanced-policies.md#SendRequest), Waarde ophalen [uit cache](api-management-caching-policies.md#GetFromCacheByKey)of [Voerbeleid beheren](api-management-advanced-policies.md#choose) voordat u verdergaat.  
-   [Verificatiebeleid](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Authenticeer met Basic](api-management-authentication-policies.md#Basic) - Verifieer met een backendservice met basisverificatie.  
    -   [Authenticeer met clientcertificaat](api-management-authentication-policies.md#ClientCertificate) - Verifieer met een backendservice met clientcertificaten.  
    -   [Verifiëren met beheerde identiteit](api-management-authentication-policies.md#ManagedIdentity) - Verifiëren met een backendservice met een [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Cachebeleidsregels](api-management-caching-policies.md#CachingPolicies)  
    -   [Haal uit de cache](api-management-caching-policies.md#GetFromCache) - Voer cache opzoeken uit en retourneer een geldig antwoord in de cache indien beschikbaar.  
    -   [Store to cache](api-management-caching-policies.md#StoreToCache) - Caches response according to the specified cache control configuration.  
    -   [Haal waarde uit cache](api-management-caching-policies.md#GetFromCacheByKey) - Haal een item in de cache op per sleutel.  
    -   [Winkelwaarde in cache](api-management-caching-policies.md#StoreToCacheByKey) - Sla een item op de cache op per sleutel.  
    -   [Waarde uit cache verwijderen](api-management-caching-policies.md#RemoveCacheByKey) - Een item in de cache per sleutel verwijderen.  
-   [Beleid voor meerdere domeinen](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Cross-domain calls toestaan](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Maakt de API toegankelijk vanaf Adobe Flash- en Microsoft Silverlight-browsergebaseerde clients.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) - Voegt CORS-ondersteuning (cross-origin resource sharing) toe aan een bewerking of een API om cross-domain calls van browsergebaseerde clients toe te staan.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - Voegt JSON toe met ondersteuning voor opvulling (JSONP) aan een bewerking of een API om cross-domain calls van JavaScript-browsergebaseerde clients toe te staan.  
-   [Transformatiebeleid](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Json converteren naar XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converteert aanvraag- of antwoordtekst van JSON naar XML.  
    -   [XML converteren naar JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converteert aanvraag- of antwoordtekst van XML naar JSON.  
    -   [String zoeken en vervangen in de hoofdtekst](api-management-transformation-policies.md#Findandreplacestringinbody) - Hiermee zoekt en vervangt u een aanvraag- of antwoordsubtekenreeks en vervangt u deze door een andere subtekenreeks.  
    -   [Url's in inhoud maskeren](api-management-transformation-policies.md#MaskURLSContent) - Koppelingen (maskers) opnieuw schrijven in de reactiebody, zodat ze via de gateway naar de gelijkwaardige koppeling verwijzen.  
    -   [Backendservice instellen](api-management-transformation-policies.md#SetBackendService) : wijzigt de backendservice voor een binnenkomende aanvraag.  
    -   [Hoofdtekst instellen](api-management-transformation-policies.md#SetBody) : hiermee stelt u de berichttekst in voor binnenkomende en uitgaande aanvragen.  
    -   [HTTP-header instellen](api-management-transformation-policies.md#SetHTTPheader) : hiermee wijst u een waarde toe aan een bestaande antwoord- en/of aanvraagheader of voegt u een nieuwe reactie- en/of aanvraagkop toe.  
    -   [Querytekenreeksparameter instellen](api-management-transformation-policies.md#SetQueryStringParameter) - Hiermee wordt de waarde van de querytekenreeksparameter van queryreeksen toegevoegd, vervangen of verwijderd.  
    -   [URL herschrijven](api-management-transformation-policies.md#RewriteURL) - Converteert een aanvraag-URL van het openbare formulier naar het formulier dat wordt verwacht door de webservice.  
    -   [XML transformeren met een XSLT](api-management-transformation-policies.md#XSLTransform) - Past een XSL-transformatie toe op XML in de aanvraag- of antwoordtekst.  



## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over beleid:

+ [Beleid in API-beheer](api-management-howto-policies.md)
+ [API's transformeren](transform-api.md)
+ [Beleidsvoorbeelden](policy-samples.md)   
