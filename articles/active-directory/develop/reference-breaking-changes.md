---
title: Verwijzing naar Azure Active Directory breuk wijzigen | Microsoft Docs
description: Meer informatie over de wijzigingen die zijn aangebracht in de Azure AD-protocollen die van invloed kunnen zijn op uw toepassing.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd50aa00368469a9c5b42c41826da28566268d4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125422"
---
# <a name="whats-new-for-authentication"></a>Wat is er nieuw voor verificatie? 

>Ontvang een melding over updates op deze pagina. U kunt [deze URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) alleen toevoegen aan uw RSS-feed-lezer.

Het verificatie systeem wijzigt en voegt voortdurend functies toe om de naleving van de beveiliging en standaarden te verbeteren. Om up-to-date te blijven met de meest recente ontwikkelingen, vindt u in dit artikel informatie over de volgende details:

- Meest recente functies
- Bekende problemen
- Protocol wijzigingen
- Afgeschafte functies

> [!TIP] 
> Deze pagina wordt regel matig bijgewerkt. Ga daarom vaak naar. Tenzij anders vermeld, worden deze wijzigingen alleen toegepast op nieuwe geregistreerde toepassingen.  

## <a name="upcoming-changes"></a>Aanstaande wijzigingen

September 2019: Aanvullende afdwinging van POST semantiek volgens de regels voor het parseren van URL'S-dubbele para meters activeren een fout en [stuk lijst](https://www.w3.org/International/questions/qa-byte-order-mark) wordt genegeerd.

## <a name="august-2019"></a>2019 augustus

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>De semantiek van POST formulieren wordt strikt meer afgedwongen en aanhalings tekens worden genegeerd

**Ingangs datum**: 2 september 2019

**Beïnvloede eind punten**: Zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: Een wille keurige POST wordt gebruikt ([client referenties](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), inzending van [autorisatie code](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)en vernieuwing van het [token](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

Met ingang van de week van 9/2 worden verificatie aanvragen die gebruikmaken van de POST-methode gevalideerd met striktere HTTP-standaarden.  In het bijzonder worden spaties en dubbele aanhalings tekens (") niet meer verwijderd uit de waarden van het aanvraag formulier. Deze wijzigingen worden niet verwacht om bestaande clients te verstoren, en zorgen ervoor dat aanvragen die naar Azure AD worden verzonden, elke keer betrouwbaar worden afgehandeld. In de toekomst (zie hierboven) gaan we ook dubbele para meters afwijzen en de stuk lijst in aanvragen negeren. 

Voorbeeld:

`?e=f&g=h` `e` Dehuidige == datum wordtidentiek`f`geparseerd. `?e=    "f"&g=h`  Met deze wijziging zou het nu worden geparseerd, zodat `e`  ==  `    "f"` dit niet waarschijnlijk een geldig argument is en de aanvraag nu mislukt. 


## <a name="july-2019"></a>2019 juli

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Alleen app-tokens voor toepassingen met één Tenant worden alleen uitgegeven als de client-app bestaat in de resource Tenant

**Ingangs datum**: 26 juli 2019

**Beïnvloede eind punten**: Zowel [v 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) als [v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Beïnvloede protocollen**: [Client referenties (alleen-app-tokens)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Een beveiligings wijziging heeft Live juli 26 die de manier waarop alleen app-tokens (via de client referenties verlenen) wordt uitgegeven, gewijzigd. Voorheen konden toepassingen tokens ophalen voor het aanroepen van andere apps, ongeacht de aanwezigheid van de Tenant of rollen die zijn gemachtigd voor die toepassing.  Dit gedrag is bijgewerkt, zodat voor resources (ook wel web-Api's genoemd) zijn ingesteld op één Tenant (de standaard instelling), de client toepassing moet bestaan in de resource Tenant.  Houd er rekening mee dat bestaande toestemming tussen de client en de API nog steeds niet is vereist en dat apps nog steeds hun eigen autorisatie controles moeten uitvoeren `roles` om ervoor te zorgen dat er een claim aanwezig is en de verwachte waarde voor de API bevat.

In het fout bericht voor dit scenario staat momenteel het volgende: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

U kunt dit probleem oplossen door de beheerder toestemming te geven om de service-principal van de client toepassing te maken in uw Tenant, of deze hand matig te maken.  Deze vereiste zorgt ervoor dat de Tenant toestemming heeft gegeven voor het uitvoeren van de toepassing in de Tenant.  

#### <a name="example-request"></a>Voorbeeldaanvraag

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`In dit voor beeld is de resource Tenant (instantie) contoso.com, de resource-app is een app met één Tenant `gateway.contoso.com/api` die is aangeroepen voor de contoso-Tenant en de `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`client-app.  Als de client-app een Service-Principal in Contoso.com heeft, kan deze aanvraag door gaan.  Als dit echter niet het geval is, mislukt de aanvraag met de bovenstaande fout.  

Als de contoso gateway-app echter een multi tenant toepassing is, wordt de aanvraag wel door gaan, ongeacht de client-app met een Service-Principal in Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Omleidings-Uri's kunnen nu query reeks parameters bevatten

**Ingangs datum**: 22 juli 2019

**Beïnvloede eind punten**: Zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: Alle stromen

Per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)kunnen Azure AD-toepassingen nu uri's https://contoso.com/oauth2?idp=microsoft) voor omleiden (beantwoorden) registreren en gebruiken met statische query parameters (zoals voor OAuth 2,0-aanvragen.  Dynamische omleidings-uri's zijn nog niet toegestaan omdat ze een beveiligings risico vormen. Dit kan niet worden gebruikt om status informatie over een verificatie aanvraag te bewaren. `state` gebruik hiervoor de para meter.

De para meter static query is onderhevig aan teken reeksen die overeenkomen met omleidings-Uri's zoals andere delen van de omleidings-URI. als er geen teken reeks is geregistreerd die overeenkomt met de door de URI gedecodeerde redirect_uri, wordt de aanvraag geweigerd.  Als de URI wordt gevonden in de app-registratie, wordt de hele teken reeks gebruikt om de gebruiker om te leiden, met inbegrip van de statische query parameter. 

Houd er rekening mee dat op dit moment (einde van 2019 juli) de app registratie UX in Azure Portal nog steeds blok keren query parameters.  U kunt het toepassings manifest echter hand matig bewerken om query parameters toe te voegen en dit in uw app te testen.  


## <a name="march-2019"></a>2019 maart

### <a name="looping-clients-will-be-interrupted"></a>Lussen van clients worden onderbroken

**Ingangs datum**: 25 maart 2019

**Beïnvloede eind punten**: Zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: Alle stromen

Client toepassingen kunnen zich soms niet gedraagt, maar het uitgeven van honderden dezelfde aanmeldings aanvraag duurt een korte periode.  Deze aanvragen kunnen al dan niet slagen, maar ze dragen bij aan een slechte gebruikers ervaring en verhoogde werk belastingen voor de IDP, verg root de latentie voor alle gebruikers en de beschik baarheid van de IDP te verminderen.  Deze toepassingen bevinden zich buiten de grenzen van het normale gebruik en moeten worden bijgewerkt om correct te werken.  

Clients die dubbele aanvragen verzenden, krijgen meerdere keren een `invalid_grant` fout:. `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 

De meeste clients hoeven het gedrag niet te wijzigen om deze fout te voor komen.  Deze fout heeft alleen invloed op onjuist geconfigureerde clients (die geen token caching hebben of de prompt lussen al).  Clients worden op basis van de volgende factoren getraceerd per exemplaar (via cookie):

* Gebruikers Hint, indien van toepassing

* Bereiken of bronnen die worden aangevraagd

* Client-id

* Omleidings-URI

* Antwoord type en modus

Apps die in korte tijd (5 minuten) meerdere aanvragen maken, krijgen een `invalid_grant` fout melding waarin wordt uitgelegd dat ze worden herhaald.  De tokens die worden aangevraagd, hebben voldoende levens duur (10 minuten mini maal, 60 minuten standaard), zodat herhaalde aanvragen gedurende deze periode onnodig zijn.  

Alle apps moeten worden `invalid_grant` afgehandeld door een interactieve prompt weer te geven in plaats van een token op de achtergrond te aanvragen.  Om deze fout te voor komen, moeten clients ervoor zorgen dat de tokens die ze ontvangen op de juiste wijze in de cache worden geplaatst.


## <a name="october-2018"></a>Oktober 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Autorisatie codes kunnen niet langer opnieuw worden gebruikt

**Ingangs datum**: 15 november 2018

**Beïnvloede eind punten**: Zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: [Code stroom](v2-oauth2-auth-code-flow.md)

Vanaf 15 November 2018, Azure AD wordt niet meer accepteren van eerder gebruikte verificatie codes voor apps. Deze wijziging in de beveiliging zorgt ervoor dat Azure AD in overeenstemming met de OAuth-specificatie brengen en worden afgedwongen op de v1- en v2-eindpunten.

Als uw app wordt gebruikgemaakt van autorisatiecodes om op te halen van tokens voor meerdere bronnen, raden wij u gebruik van de code om op te halen van een vernieuwingstoken en gebruikt vervolgens die vernieuwingstoken om te verkrijgen van aanvullende tokens voor andere resources. Autorisatiecodes kunnen slechts eenmaal worden gebruikt, maar vernieuwen van tokens kunnen meerdere keren worden gebruikt in meerdere resources. Elke nieuwe app die probeert een verificatie code opnieuw te gebruiken tijdens de OAuth-code stroom krijgt een invalid_grant-fout.

Zie [de toegangs tokens vernieuwen](v1-protocols-oauth-code.md#refreshing-the-access-tokens)voor meer informatie over het vernieuwen van tokens.  Als u ADAL of MSAL gebruikt, wordt dit door de bibliotheek verwerkt: Vervang het tweede exemplaar van ' AcquireTokenByAuthorizationCodeAsync ' door ' AcquireTokenSilentAsync '. 

## <a name="may-2018"></a>Mei 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID-tokens kunnen niet worden gebruikt voor de OBO-stroom

**Datum**: 1 mei 2018

**Beïnvloede eind punten**: Zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: Impliciete stroom en [OBO-stroom](v1-oauth2-on-behalf-of-flow.md)

Na 1 mei 2018 kan id_tokens niet worden gebruikt als de bewering in een OBO-stroom voor nieuwe toepassingen. In plaats daarvan moeten toegangs tokens worden gebruikt om Api's te beveiligen, zelfs tussen een client en een middelste laag van dezelfde toepassing. Apps die zijn geregistreerd vóór 1 mei 2018 blijven werken en kunnen id_tokens voor een toegangs token uitwisselen. Dit patroon wordt echter niet beschouwd als een best practice.

Als u deze wijziging wilt omzeilen, kunt u het volgende doen:

1. Een web-API maken voor uw toepassing, met een of meer scopes. Dit expliciete ingangs punt staat nauw keurig gekorrelig beheer en beveiliging toe.
1. Controleer in het manifest van uw app, in de [Azure Portal](https://portal.azure.com) of de [app registratie Portal](https://apps.dev.microsoft.com), of de app toegangs tokens mag uitgeven via de impliciete stroom. Dit wordt bepaald door de `oauth2AllowImplicitFlow` sleutel.
1. Als uw client toepassing een id_token aanvraagt via `response_type=id_token`, moet u ook een toegangs token (`response_type=token`) aanvragen voor de Web-API die hierboven is gemaakt. Wanneer u het v 2.0-eind punt gebruikt `scope` , moet de para meter `api://GUID/SCOPE`er ongeveer als volgt uitzien. Op het eind punt v 1.0 moet `resource` de para meter de app-URI van de Web-API zijn.
1. Geef dit toegangs token door aan de middelste laag in plaats van de id_token.  
