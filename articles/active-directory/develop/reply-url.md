---
title: Redirect URI & antwoord URL beperkingen - Microsoft identity platform | Azure
description: Beperkingen voor het beantwoorden van URL's/omleidingen & beperkingen
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656735"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Beperkingen voor omleidings-URI en antwoord-URL

Een omleidingsURI of een url voor het beantwoorden is de locatie waarnaar de autorisatieserver de gebruiker stuurt zodra de app is geautoriseerd en een autorisatiecode of toegangstoken heeft gekregen. De code of token is opgenomen in de omleiding URI of antwoord token dus het is belangrijk dat u de juiste locatie te registreren als onderdeel van de app registratieproces.

 De volgende beperkingen zijn van toepassing op het beantwoorden van URL's:

    * De url van het `https`antwoord moet beginnen met de regeling .
    * De antwoord-URL is hoofdlettergevoelig. De case moet overeenkomen met het geval van het URL-pad van uw lopende toepassing. Als uw toepassing bijvoorbeeld als onderdeel `.../abc/response-oidc`van het `.../ABC/response-oidc` pad wordt weergegeven, geeft u dit niet op in de antwoord-URL. Omdat de webbrowser paden als hoofdlettergevoelig behandelt, `.../abc/response-oidc` kunnen cookies die zijn gekoppeld aan `.../ABC/response-oidc` cookies worden uitgesloten als ze worden doorgestuurd naar de URL die niet op de plaats van de aanvraag is gekoppeld.
    
## <a name="maximum-number-of-redirect-uris"></a>Maximaal aantal omleidings-URI's

In de volgende tabel ziet u het maximum aantal omleidings-URI's dat u toevoegen wanneer u uw app registreert.

| Accounts die zijn ondertekend | Maximaal aantal omleidings-URI's | Beschrijving |
|--------------------------|---------------------------------|-------------|
| Microsoft-werk- of schoolaccounts in de Azure Active Directory-tenant (Azure AD) van een organisatie | 256 | `signInAudience`veld in het toepassingsmanifest is ingesteld op *AzureADMyOrg* of *AzureADMultipleOrgs* |
| Persoonlijke Microsoft-accounts en werk- en schoolaccounts | 100 | `signInAudience`veld in het toepassingsmanifest is ingesteld op *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximale URI-lengte

U maximaal 256 tekens gebruiken voor elke omleidingsURI die u toevoegt aan een app-registratie.

## <a name="supported-schemes"></a>Ondersteunde regelingen
Het Azure AD-toepassingsmodel ondersteunt vandaag zowel HTTP- als HTTPS-schema's voor apps die zich aanmelden bij Microsoft-werk- of schoolaccounts in de Azure Active Directory -tenant (Azure AD) van een organisatie. Dat `signInAudience` is veld in het toepassingsmanifest is ingesteld op *AzureADMyOrg* of *AzureADMultipleOrgs*. Voor de apps die zich aanmelden bij Persoonlijke Microsoft-accounts en werk- en schoolaccounts (die is `signInAudience` ingesteld op *AzureADandPersonalMicrosoftAccount)* is alleen https-schema toegestaan.

> [!NOTE]
> De nieuwe ervaring [met app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) staat ontwikkelaars niet toe om URI's toe te voegen met HTTP-schema op de gebruikersinterface. Het toevoegen van HTTP-URL's voor apps die zich aanmelden voor werk- of schoolaccounts wordt alleen ondersteund via de manifesteditor van de app. In de toekomst kunnen nieuwe apps http-schema's niet gebruiken in de omleidings-URI. Oudere apps die HTTP-schema's bevatten in omleidings-URI's blijven echter werken. Ontwikkelaars moeten HTTPS-schema's gebruiken in de omleidings-URI's.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Beperkingen met een wildcard in URI's

Wildcard URI's, `https://*.contoso.com`zoals , zijn handig, maar moeten worden vermeden. Het gebruik van wildcards in de omleiding URI heeft gevolgen voor de beveiliging. Volgens de OAuth 2.0 specificatie[(punt 3.1.2 van RFC 6749)](https://tools.ietf.org/html/rfc6749#section-3.1.2)moet een omleidingseindpunt URI een absolute URI zijn. 

Het Azure AD-toepassingsmodel biedt geen ondersteuning voor urinoir-URI's voor apps die zijn geconfigureerd om persoonlijke Microsoft-accounts en werk- of schoolaccounts aan te melden. Er zijn echter wildcard-URI's toegestaan voor apps die zijn geconfigureerd om zich vandaag aan te melden bij werk- of schoolaccounts in de Azure AD-tenant van een organisatie. 
 
> [!NOTE]
> Met de nieuwe ervaring [met app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) kunnen ontwikkelaars geen wildcard-URI's toevoegen aan de gebruikersinterface. Het toevoegen van wilcard URI voor apps die zich aanmelden voor werk- of schoolaccounts wordt alleen ondersteund via de manifesteditor van de app. In de toekomst kunnen nieuwe apps geen wildcards gebruiken in de omleiding uri. Oudere apps die wildcards bevatten in omleidings-URI's blijven echter werken.

Als voor uw scenario meer omleidings-URI's nodig zijn dan de maximaal toegestane limiet, moet u in plaats van een wildcard-omleidinguri toevoegen, rekening houden met de volgende benadering.

### <a name="use-a-state-parameter"></a>Een parameter status gebruiken

Als u een aantal subdomeinen hebt en als u in uw scenario vereist dat u gebruikers doorverwijst naar succesvolle verificatie naar dezelfde pagina waar ze zijn begonnen, kan het nuttig zijn om een statusparameter te gebruiken. 

In deze benadering:

1. Maak een 'gedeelde' omleiding uri per toepassing om de beveiligingstokens te verwerken die u ontvangt van het autorisatieeindpunt.
1. Uw toepassing kan toepassingsspecifieke parameters (zoals een URL van het subdomein waar de gebruiker vandaan komt of iets dergelijks merkinformatie) in de parameter status verzenden. Bescherm bij het gebruik van een parameter in de toestand tegen de bescherming van CSRF zoals gespecificeerd in [punt 10.12 van RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. De toepassingsspecifieke parameters bevatten alle informatie die nodig is voor de toepassing om de juiste ervaring voor de gebruiker weer te geven, dat wil zeggen de juiste toepassingsstatus te construeren. Het eindpunt van de Azure AD-autorisatie stript HTML van de parameter status, dus zorg ervoor dat u geen HTML-inhoud in deze parameter doorgeeft.
1. Wanneer Azure AD een antwoord stuurt op de 'gedeelde' omleidinguri, wordt de parameter status teruggestuurd naar de toepassing.
1. De toepassing kan vervolgens de waarde in de parameter status gebruiken om te bepalen naar welke URL de gebruiker verder moet worden verzonden. Zorg ervoor dat u valideert voor CSRF-bescherming.

> [!NOTE]
> Met deze benadering kan een gecompromitteerde client de extra parameters wijzigen die in de parameter status worden verzonden, waardoor de gebruiker wordt omgeleid naar een andere URL, de [open redirector-bedreiging](https://tools.ietf.org/html/rfc6819#section-4.2.4) die wordt beschreven in RFC 6819. Daarom moet de client deze parameters beschermen door de status te versleutelen of deze op een andere manier te verifiëren, zoals het valideren van domeinnaam in de omleiding uri tegen het token.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [toepassingsmanifest](reference-app-manifest.md)
