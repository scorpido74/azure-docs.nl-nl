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
ms.date: 1/24/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 9cb13ea56c39f365ddb888a5d4e94228b1881fc4
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160357"
---
# <a name="whats-new-for-authentication"></a>Wat is er nieuw voor verificatie? 

>Ontvang een melding over updates op deze pagina. U kunt [deze URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) alleen toevoegen aan uw RSS-feed-lezer.

Het verificatie systeem wijzigt en voegt voortdurend functies toe om de naleving van de beveiliging en standaarden te verbeteren. Om up-to-date te blijven met de meest recente ontwikkelingen, vindt u in dit artikel informatie over de volgende details:

- Recentste functies
- Bekende problemen
- Protocol wijzigingen
- Afgeschafte functies

> [!TIP] 
> Deze pagina wordt regel matig bijgewerkt. Ga daarom vaak naar. Tenzij anders vermeld, worden deze wijzigingen alleen toegepast op nieuwe geregistreerde toepassingen.  

## <a name="upcoming-changes"></a>Aanstaande wijzigingen

Geen gepland op dit moment.  Hieronder vindt u een overzicht van de wijzigingen die in of beschikbaar zijn voor productie. 

## <a name="february-2020"></a>Februari 2020: 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Lege fragmenten worden toegevoegd aan elke HTTP-omleiding vanuit het aanmeldings-eind punt. 

**Ingangs datum**: 8 februari 2020

**Beïnvloede eind punten**: zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: OAuth-en OIDC-stromen die gebruikmaken van response_type = query-dit bevat de [autorisatie code stroom](v2-oauth2-auth-code-flow.md) in sommige gevallen en de [impliciete stroom](v2-oauth2-implicit-grant-flow.md). 

Wanneer een verificatie reactie vanuit login.microsoftonline.com naar een toepassing wordt verzonden via een HTTP-omleiding, wordt door de service een leeg fragment aan de antwoord-URL toegevoegd.  Dit voor komt een klasse van omleidings aanvallen door ervoor te zorgen dat de browser alle bestaande fragmenten in de verificatie aanvraag wist.  Geen enkele app moet afhankelijk zijn van dit gedrag. 


## <a name="august-2019"></a>Augustus 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>De semantiek van POST formulieren wordt strikt meer afgedwongen en aanhalings tekens worden genegeerd

**Ingangs datum**: 2 september 2019

**Beïnvloede eind punten**: zowel v 1.0 als v 2.0

**Getroffen protocol**: Anywhere post wordt gebruikt ([client referenties](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), inzending van [autorisatie code](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)en [vernieuwing](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)van het token)

Met ingang van de week van 9/2 worden verificatie aanvragen die gebruikmaken van de POST-methode gevalideerd met striktere HTTP-standaarden.  In het bijzonder worden spaties en dubbele aanhalings tekens (") niet meer verwijderd uit de waarden van het aanvraag formulier. Deze wijzigingen worden niet verwacht om bestaande clients te verstoren, en zorgen ervoor dat aanvragen die naar Azure AD worden verzonden, elke keer betrouwbaar worden afgehandeld. In de toekomst (zie hierboven) gaan we ook dubbele para meters afwijzen en de stuk lijst in aanvragen negeren. 

Voorbeeld:

Momenteel wordt `?e=    "f"&g=h` hetzelfde geparseerd als `?e=f&g=h`-zo `e` == `f`.  Met deze wijziging wordt deze nu geparseerd, zodat `e` == `    "f"`-dit is waarschijnlijk geen geldig argument. de aanvraag zou nu niet kunnen worden uitgevoerd. 


## <a name="july-2019"></a>Juli 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Alleen app-tokens voor toepassingen met één Tenant worden alleen uitgegeven als de client-app bestaat in de resource Tenant

**Ingangs datum**: 26 juli 2019

**Beïnvloede eind punten**: zowel [v 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) als [v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Beïnvloede protocol**: [client referenties (tokens van app-only)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Een beveiligings wijziging heeft Live juli 26 die de manier waarop alleen app-tokens (via de client referenties verlenen) wordt uitgegeven, gewijzigd. Voorheen konden toepassingen tokens ophalen voor het aanroepen van andere apps, ongeacht de aanwezigheid van de Tenant of rollen die zijn gemachtigd voor die toepassing.  Dit gedrag is bijgewerkt, zodat voor resources (ook wel web-Api's genoemd) zijn ingesteld op één Tenant (de standaard instelling), de client toepassing moet bestaan in de resource Tenant.  Houd er rekening mee dat bestaande toestemming tussen de client en de API nog steeds niet is vereist en dat apps nog steeds hun eigen autorisatie controles moeten uitvoeren om ervoor te zorgen dat een `roles` claim aanwezig is en de verwachte waarde voor de API bevat.

In het fout bericht voor dit scenario staat momenteel het volgende: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

U kunt dit probleem oplossen door de beheerder toestemming te geven om de service-principal van de client toepassing te maken in uw Tenant, of deze hand matig te maken.  Deze vereiste zorgt ervoor dat de Tenant toestemming heeft gegeven voor het uitvoeren van de toepassing in de Tenant.  

#### <a name="example-request"></a>Voorbeeldaanvraag

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` in dit voor beeld is de resource Tenant (instantie) contoso.com, de resource-app is een app met één Tenant met de naam `gateway.contoso.com/api` voor de contoso-Tenant, en de client-app is `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`.  Als de client-app een Service-Principal in Contoso.com heeft, kan deze aanvraag door gaan.  Als dit echter niet het geval is, mislukt de aanvraag met de bovenstaande fout.  

Als de contoso gateway-app echter een multi tenant toepassing is, wordt de aanvraag wel door gaan, ongeacht de client-app met een Service-Principal in Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Omleidings-Uri's kunnen nu query reeks parameters bevatten

**Ingangs datum**: 22 juli 2019

**Beïnvloede eind punten**: zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: alle stromen

Per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)kunnen Azure AD-toepassingen nu uri's voor omleiden (beantwoorden) registreren en gebruiken met statische query parameters (zoals https://contoso.com/oauth2?idp=microsoft) voor OAuth 2,0-aanvragen.  Dynamische omleidings-Uri's zijn nog niet toegestaan omdat ze een beveiligings risico vormen. Dit kan niet worden gebruikt om status informatie over een verificatie aanvraag te bewaren. Gebruik hiervoor de para meter `state`.

De para meter static query is onderhevig aan teken reeksen die overeenkomen met omleidings-Uri's zoals andere delen van de omleidings-URI. als er geen teken reeks is geregistreerd die overeenkomt met de door de URI gedecodeerde redirect_uri, wordt de aanvraag geweigerd.  Als de URI wordt gevonden in de app-registratie, wordt de hele teken reeks gebruikt om de gebruiker om te leiden, met inbegrip van de statische query parameter. 

Houd er rekening mee dat op dit moment (einde van 2019 juli) de app registratie UX in Azure Portal nog steeds blok keren query parameters.  U kunt het toepassings manifest echter hand matig bewerken om query parameters toe te voegen en dit in uw app te testen.  


## <a name="march-2019"></a>Maart 2019

### <a name="looping-clients-will-be-interrupted"></a>Lussen van clients worden onderbroken

**Ingangs datum**: 25 maart 2019

**Beïnvloede eind punten**: zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: alle stromen

Client toepassingen kunnen zich soms niet gedraagt, maar het uitgeven van honderden dezelfde aanmeldings aanvraag duurt een korte periode.  Deze aanvragen kunnen al dan niet slagen, maar ze dragen bij aan een slechte gebruikers ervaring en verhoogde werk belastingen voor de IDP, verg root de latentie voor alle gebruikers en de beschik baarheid van de IDP te verminderen.  Deze toepassingen bevinden zich buiten de grenzen van het normale gebruik en moeten worden bijgewerkt om correct te werken.  

Clients die dubbele aanvragen verzenden, krijgen meerdere keren een `invalid_grant` fout: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

De meeste clients hoeven het gedrag niet te wijzigen om deze fout te voor komen.  Deze fout heeft alleen invloed op onjuist geconfigureerde clients (die geen token caching hebben of de prompt lussen al).  Clients worden op basis van de volgende factoren getraceerd per exemplaar (via cookie):

* Gebruikers Hint, indien van toepassing

* Bereiken of bronnen die worden aangevraagd

* Client-id

* Omleidings-URI

* Antwoord type en modus

Apps die in een korte periode (5 minuten) meerdere aanvragen maken, ontvangen een `invalid_grant` fout, waarin wordt uitgelegd dat ze worden herhaald.  De tokens die worden aangevraagd, hebben voldoende levens duur (10 minuten mini maal, 60 minuten standaard), zodat herhaalde aanvragen gedurende deze periode onnodig zijn.  

Alle apps moeten `invalid_grant` afhandelen door een interactieve prompt weer te geven in plaats van een token op de achtergrond te aanvragen.  Om deze fout te voor komen, moeten clients ervoor zorgen dat de tokens die ze ontvangen op de juiste wijze in de cache worden geplaatst.


## <a name="october-2018"></a>Oktober 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Autorisatie codes kunnen niet langer opnieuw worden gebruikt

**Ingangs datum**: 15 november 2018

**Beïnvloede eind punten**: zowel v 1.0 als v 2.0

**Beïnvloede protocol**: [code stroom](v2-oauth2-auth-code-flow.md)

Vanaf 15 November 2018, Azure AD wordt niet meer accepteren van eerder gebruikte verificatie codes voor apps. Deze wijziging in de beveiliging zorgt ervoor dat Azure AD in overeenstemming met de OAuth-specificatie brengen en worden afgedwongen op de v1- en v2-eindpunten.

Als uw app wordt gebruikgemaakt van autorisatiecodes om op te halen van tokens voor meerdere bronnen, raden wij u gebruik van de code om op te halen van een vernieuwingstoken en gebruikt vervolgens die vernieuwingstoken om te verkrijgen van aanvullende tokens voor andere resources. Autorisatiecodes kunnen slechts eenmaal worden gebruikt, maar vernieuwen van tokens kunnen meerdere keren worden gebruikt in meerdere resources. Elke nieuwe app die probeert een verificatie code opnieuw te gebruiken tijdens de OAuth-code stroom krijgt een invalid_grant fout.

Zie [de toegangs tokens vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token)voor meer informatie over het vernieuwen van tokens.  Als u ADAL of MSAL gebruikt, wordt dit door de bibliotheek verwerkt: Vervang het tweede exemplaar van ' AcquireTokenByAuthorizationCodeAsync ' door ' AcquireTokenSilentAsync '. 

## <a name="may-2018"></a>Mei 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID-tokens kunnen niet worden gebruikt voor de OBO-stroom

**Datum**: 1 mei 2018

**Beïnvloede eind punten**: zowel v 1.0 als v 2.0

**Beïnvloede protocollen**: impliciete stroom en [namens-stroom](v2-oauth2-on-behalf-of-flow.md)

Na 1 mei 2018 kan id_tokens niet worden gebruikt als de bewering in een OBO-stroom voor nieuwe toepassingen. In plaats daarvan moeten toegangs tokens worden gebruikt om Api's te beveiligen, zelfs tussen een client en een middelste laag van dezelfde toepassing. Apps die zijn geregistreerd vóór 1 mei 2018 blijven werken en kunnen id_tokens voor een toegangs token uitwisselen. Dit patroon wordt echter niet beschouwd als een best practice.

Als u deze wijziging wilt omzeilen, kunt u het volgende doen:

1. Een web-API maken voor uw toepassing, met een of meer scopes. Dit expliciete ingangs punt staat nauw keurig gekorrelig beheer en beveiliging toe.
1. Controleer in het manifest van uw app, in de [Azure Portal](https://portal.azure.com) of de [app registratie Portal](https://apps.dev.microsoft.com), of de app toegangs tokens mag uitgeven via de impliciete stroom. Dit wordt bepaald door de `oauth2AllowImplicitFlow` sleutel.
1. Als uw client toepassing een id_token aanvraagt via `response_type=id_token`, moet u ook een toegangs token (`response_type=token`) aanvragen voor de Web-API die hierboven is gemaakt. Wanneer u het v 2.0-eind punt gebruikt, moet de para meter `scope` er als volgt uitzien: `api://GUID/SCOPE`. Op het eind punt v 1.0 moet de para meter `resource` de app-URI zijn van de Web-API.
1. Geef dit toegangs token door aan de middelste laag in plaats van de id_token.  
