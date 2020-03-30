---
title: Verwijzing naar wijzigingen in Azure Active Directory | Microsoft Documenten
description: Meer informatie over wijzigingen die zijn aangebracht in de Azure AD-protocollen die van invloed kunnen zijn op uw toepassing.
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
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1248063c1b4c1b1e124ff671797450dd5c1b8727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050177"
---
# <a name="whats-new-for-authentication"></a>Wat is er nieuw voor authenticatie? 

>Ontvang een melding over updates op deze pagina. Voeg [deze URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) gewoon toe aan uw RSS-feedlezer.

Het verificatiesysteem wijzigt en voegt voortdurend functies toe om de naleving van beveiliging en standaarden te verbeteren. Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt dit artikel u informatie over de volgende details:

- Recentste functies
- Bekende problemen
- Protocolwijzigingen
- Afgeschafte functionaliteit

> [!TIP] 
> Deze pagina wordt regelmatig bijgewerkt, dus bezoek vaak. Tenzij anders vermeld, worden deze wijzigingen alleen doorgevoerd voor nieuw geregistreerde aanvragen.  

## <a name="upcoming-changes"></a>Aankomende wijzigingen

Geen gepland op dit moment.  Zie hieronder voor de wijzigingen die in of komen naar productie. 

## <a name="march-2020"></a>Maart 2020 

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Gebruikerswachtwoorden worden beperkt tot 256 tekens.

**Ingangsdatum**: 13 maart 2020

**Getroffen eindpunten**: Zowel v1.0 als v2.0

**Getroffen protocol**: Alle gebruikersstromen. 

Gebruikers met wachtwoorden langer dan 256 tekens die zich rechtstreeks aanmelden bij Azure AD (in tegenstelling tot een gefedereerde IDP zoals ADFS) kunnen zich vanaf 13 maart 2020 niet aanmelden en worden gevraagd om hun wachtwoord opnieuw in te stellen.  Beheerders kunnen verzoeken ontvangen om het gebruikerswachtwoord opnieuw in te stellen. 

De fout in de aanmeldingslogboeken is AADSTS 50052: InvalidPasswordExceedsMaxLength

Bericht:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Sanering:

De gebruiker kan niet inloggen omdat zijn wachtwoord de toegestane maximale lengte overschrijdt. Ze moeten contact opnemen met hun beheerder om het wachtwoord opnieuw in te stellen. Als SSPR is ingeschakeld voor hun tenant, kunnen ze hun wachtwoord opnieuw instellen door de link 'Uw wachtwoord vergeten' te volgen.



## <a name="february-2020"></a>Februari 2020 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Lege fragmenten worden toegevoegd aan elke HTTP-omleiding vanaf het aanmeldingseindpunt. 

**Ingangsdatum**: 8 februari 2020

**Getroffen eindpunten**: Zowel v1.0 als v2.0

**Getroffen protocol**: OAuth- en OIDC-stromen die response_type=query gebruiken - dit dekt in sommige gevallen de [autorisatiecodestroom](v2-oauth2-auth-code-flow.md) en de [impliciete stroom](v2-oauth2-implicit-grant-flow.md). 

Wanneer een verificatieantwoord wordt verzonden van login.microsoftonline.com naar een toepassing via HTTP-omleiding, zal de service een leeg fragment toevoegen aan de antwoord-URL.  Dit voorkomt een klasse van omleidingsaanvallen door ervoor te zorgen dat de browser elk bestaand fragment in het verificatieverzoek wegveegt.  Geen apps mogen afhankelijk zijn van dit gedrag. 


## <a name="august-2019"></a>Augustus 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST vorm semantiek zal strikter worden afgedwongen - spaties en citaten zullen worden genegeerd

**Ingangsdatum**: 2 september 2019

**Getroffen eindpunten**: Zowel v1.0 als v2.0

**Getroffen protocol**: Anywhere POST wordt gebruikt[(clientreferenties,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) [autorisatiecode inwisseling,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)en [vernieuwen token inwisseling)](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)

Vanaf de week van 9/2 worden verificatieverzoeken die gebruikmaken van de POST-methode gevalideerd met strengere HTTP-normen.  Met name spaties en dubbele aanhalingstekens (") worden niet langer verwijderd uit de waarden van het aanvraagformulier. Deze wijzigingen zullen naar verwachting geen bestaande clients verbreken en zorgen ervoor dat aanvragen die naar Azure AD worden verzonden, elke keer betrouwbaar worden verwerkt. In de toekomst (zie hierboven) zijn we van plan om bovendien dubbele parameters af te wijzen en de bom binnen aanvragen te negeren. 

Voorbeeld:

Vandaag, `?e=    "f"&g=h` is identiek `?e=f&g=h` ontleed `e`  ==  `f`als - dus .  Met deze wijziging, zou het nu `e`  ==  `    "f"` worden ontleed, zodat - dit is waarschijnlijk geen geldig argument, en het verzoek zou nu mislukken. 


## <a name="july-2019"></a>Juli 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>App-only tokens voor toepassingen met één tenant worden alleen uitgegeven als de client-app bestaat in de resourcetenant

**Ingangsdatum**: 26 juli 2019

**Getroffen eindpunten**: Zowel [v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) als [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocol beïnvloed:** [Clientreferenties (alleen app-tokens)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Een beveiligingswijziging is live gegaan op 26 juli, dat de manier verandert waarop app-only tokens (via de clientreferenties grant) worden uitgegeven. Voorheen mochten toepassingen tokens krijgen om een andere app te bellen, ongeacht de aanwezigheid in de tenant of de rollen waarmee voor die toepassing is ingestemd.  Dit gedrag is zo bijgewerkt dat voor resources (ook wel Web API's genoemd) zijn ingesteld op single-tenant (de standaardinstelling), de clienttoepassing moet bestaan binnen de resourcetenant.  Houd er rekening mee dat bestaande toestemming tussen de client en de API nog steeds `roles` niet vereist is en dat apps nog steeds hun eigen autorisatiecontroles moeten uitvoeren om ervoor te zorgen dat een claim aanwezig is en de verwachte waarde voor de API bevat.

In het foutbericht voor dit scenario staat momenteel: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Als u dit probleem wilt verhelpen, gebruikt u de ervaring Beheerderstoestemming om de clienttoepassingsserviceprincipal in uw tenant te maken of handmatig te maken.  Deze vereiste zorgt ervoor dat de tenant de aanvraag toestemming heeft gegeven om binnen de tenant te werken.  

#### <a name="example-request"></a>Voorbeeldaanvraag

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`In dit voorbeeld wordt de resourcetenant (autoriteit) contoso.com, is de `gateway.contoso.com/api` resource-app een app met één `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`tenant die wordt aangeroepen voor de Contoso-tenant en is de client-app .  Als de client-app een serviceprincipal binnen Contoso.com heeft, kan deze aanvraag worden voortgezet.  Als dit echter niet het niet gebeurt, mislukt het verzoek met de bovenstaande fout.  

Als de Contoso-gateway-app echter een multi-tenanttoepassing was, wordt het verzoek voortgezet, ongeacht de client-app met een serviceprincipal binnen Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Omleidings-URI's kunnen nu querytekenreeksparameters bevatten

**Ingangsdatum**: 22 juli 2019

**Getroffen eindpunten**: Zowel v1.0 als v2.0

**Getroffen protocol**: Alle stromen

Per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)kunnen Azure AD-toepassingen nu omleidings-(antwoord)URI's `https://contoso.com/oauth2?idp=microsoft`met statische queryparameters (zoals ) registreren en gebruiken voor OAuth 2.0-aanvragen.  Dynamische omleidings-URI's zijn nog steeds verboden omdat ze een beveiligingsrisico vormen en dit kan `state` niet worden gebruikt om staatsinformatie te bewaren voor een verificatieverzoek - gebruik daarvoor de parameter.

De statische queryparameter is onderhevig aan tekenreeksmatching voor omleidings-URI's zoals elk ander deel van de omleidings-URI - als er geen tekenreeks is geregistreerd die overeenkomt met de URI-gedecodeerde redirect_uri, wordt de aanvraag afgewezen.  Als de URI wordt gevonden in de app-registratie, wordt de hele tekenreeks gebruikt om de gebruiker om te leiden, inclusief de statische queryparameter. 

Houd er rekening mee dat op dit moment (eind juli 2019) de UX voor app-registratie in Azure-portal queryparameters nog steeds blokkeert.  U het toepassingsmanifest echter handmatig bewerken om queryparameters toe te voegen en dit in uw app te testen.  


## <a name="march-2019"></a>Maart 2019

### <a name="looping-clients-will-be-interrupted"></a>Looping clients worden onderbroken

**Ingangsdatum**: 25 maart 2019

**Getroffen eindpunten**: Zowel v1.0 als v2.0

**Getroffen protocol**: Alle stromen

Clienttoepassingen kunnen zich soms misdragen en gedurende korte tijd honderden dezelfde inlogverzoeken indienen.  Deze verzoeken kunnen wel of niet succesvol zijn, maar ze dragen allemaal bij aan een slechte gebruikerservaring en verhoogde workloads voor het IDP, waardoor de latentie voor alle gebruikers toeneemt en de beschikbaarheid van het IDP wordt verminderd.  Deze toepassingen werken buiten de grenzen van normaal gebruik en moeten worden bijgewerkt om zich correct te gedragen.  

Clients die meerdere keren dubbele aanvragen `invalid_grant` uitgeven, krijgen een foutmelding: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

De meeste clients hoeven het gedrag niet te wijzigen om deze fout te voorkomen.  Alleen verkeerd geconfigureerde clients (clients zonder tokencaching of die al snelle lussen vertonen) worden beïnvloed door deze fout.  Klanten worden per instantie lokaal (via cookie) gevolgd op de volgende factoren:

* Gebruiker hint, indien aanwezig

* Scopes of resource die worden aangevraagd

* Client-id

* Omleidings-URI

* Reactietype en -modus

Apps die meerdere aanvragen (15+) indienen in een korte `invalid_grant` periode (5 minuten) ontvangen een foutmelding waarin wordt uitgelegd dat ze aan het lussen zijn.  De tokens die worden aangevraagd hebben voldoende lange levensduur (minimaal 10 minuten, standaard 60 minuten), dus herhaalde verzoeken in deze periode zijn onnodig.  

Alle apps `invalid_grant` moeten worden verwerkt door een interactieve prompt weer te geven, in plaats van in stilte een token aan te vragen.  Om deze fout te voorkomen, moeten klanten ervoor zorgen dat ze de tokens die ze ontvangen correct in cache plaatsen.


## <a name="october-2018"></a>Oktober 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Autorisatiecodes kunnen niet meer worden hergebruikt

**Ingangsdatum**: 15 november 2018

**Getroffen eindpunten**: Zowel v1.0 als v2.0

**Getroffen protocol**: [Codestroom](v2-oauth2-auth-code-flow.md)

Vanaf 15 november 2018 stopt Azure AD met het accepteren van eerder gebruikte verificatiecodes voor apps. Deze beveiligingswijziging helpt azure AD in overeenstemming te brengen met de OAuth-specificatie en wordt afgedwongen op zowel de v1- als de V2-eindpunten.

Als uw app autorisatiecodes opnieuw gebruikt om tokens voor meerdere bronnen te krijgen, raden we u aan de code te gebruiken om een vernieuwingstoken te ontvangen en vervolgens dat vernieuwingstoken te gebruiken om extra tokens voor andere bronnen te verkrijgen. Autorisatiecodes kunnen slechts één keer worden gebruikt, maar vernieuwingstokens kunnen meerdere keren worden gebruikt in meerdere bronnen. Elke nieuwe app die probeert een verificatiecode opnieuw te gebruiken tijdens de OAuth-codestroom, krijgt een invalid_grant fout.

Zie [De toegangstokens vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token)voor meer informatie over vernieuwingstokens.  Als u ADAL of MSAL gebruikt, wordt dit voor u afgehandeld door de bibliotheek - vervang de tweede instantie van 'AcquireTokenByAuthorizationCodeAsync' door 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Mei 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID-tokens kunnen niet worden gebruikt voor de OBO-stroom

**Datum**: 1 mei 2018

**Getroffen eindpunten**: Zowel v1.0 als v2.0

**Getroffen protocollen**: Impliciete stroom en [rekening houden met stroom](v2-oauth2-on-behalf-of-flow.md)

Na 1 mei 2018 kan id_tokens niet meer worden gebruikt als bewering in een OBO-stroom voor nieuwe toepassingen. Toegangstokens moeten in plaats daarvan worden gebruikt om API's te beveiligen, zelfs tussen een client en de middelste laag van dezelfde toepassing. Apps die vóór 1 mei 2018 zijn geregistreerd, blijven werken en kunnen id_tokens inwisselen voor een toegangstoken; Dit patroon wordt echter niet beschouwd als een beste praktijk.

Als u deze wijziging wilt omzeilen, u het volgende doen:

1. Maak een web-API voor uw toepassing, met een of meer scopes. Dit expliciete toegangspunt zal fijnere korrelige controle en veiligheid toestaan.
1. Zorg er in het manifest van uw app, in de [Azure-portal](https://portal.azure.com) of de [app-registratieportal,](https://apps.dev.microsoft.com)voor dat de app toegangstokens mag uitgeven via de impliciete stroom. Dit wordt gecontroleerd `oauth2AllowImplicitFlow` door de sleutel.
1. Wanneer uw clienttoepassing een `response_type=id_token`id_token aanvraagt via, vraagt u ook een access token aan (`response_type=token`) voor de web-API die hierboven is gemaakt. Bij het gebruik van het v2.0-eindpunt moet de `scope` parameter er dus hetzelfde uitzien als `api://GUID/SCOPE`. Op het v1.0-eindpunt `resource` moet de parameter de app URI van de web-API zijn.
1. Geef dit toegangstoken door aan de middelste laag in plaats van de id_token.  
