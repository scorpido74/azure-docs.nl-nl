---
title: OAuth 2.0- en OpenID Connect-protocollen - Microsoft-identiteitsplatform | Azure
description: Een handleiding voor OAuth 2.0- en OpenID Connect-protocollen die worden ondersteund door het eindpunt van het Microsoft-identiteitsplatform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4847bddcbcfbc27502965efa221a3707fa453457
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885664"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>OAuth 2.0- en OpenID Connect-protocollen op het Microsoft-identiteitsplatform

Het eindpunt van het Microsoft-identiteitsplatform voor identity-as-a-service met industriestandaardprotocollen, OpenID Connect en OAuth 2.0. Hoewel de service voldoet aan de normen, kunnen er subtiele verschillen zijn tussen de twee implementaties van deze protocollen. De informatie hier is handig als u ervoor kiest om uw code te schrijven door http-verzoeken rechtstreeks te verzenden en te verwerken of een open-sourcebibliotheek van derden te gebruiken, in plaats van een van onze [open-source bibliotheken](reference-v2-libraries.md)te gebruiken.

> [!NOTE]
> Niet alle Azure AD-scenario's en -functies worden ondersteund door het eindpunt van het Microsoft-identiteitsplatform. Als u wilt bepalen of u het eindpunt van het Microsoft-identiteitsplatform moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](active-directory-v2-limitations.md)

## <a name="the-basics"></a>De basisbeginselen

In bijna alle OAuth 2.0- en OpenID Connect-stromen zijn er vier partijen betrokken bij de uitwisseling:

![Diagram met de rollen OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* De **Autorisatieserver** is het eindpunt van het Microsoft-identiteitsplatform en is verantwoordelijk voor het waarborgen van de identiteit van de gebruiker, het verlenen en intrekken van toegang tot bronnen en het uitgeven van tokens. De autorisatieserver, ook wel bekend als de identiteitsprovider, verwerkt veilig alles wat te maken heeft met de informatie van de gebruiker, hun toegang en de vertrouwensrelaties tussen partijen in een stroom.
* De **resource-eigenaar** is meestal de eindgebruiker. Het is de partij die eigenaar is van de gegevens en heeft de bevoegdheid om derden toegang te geven tot die gegevens of bron.
* De **OAuth-client** is uw app, die wordt geïdentificeerd door de toepassings-ID. De OAuth-client is meestal de partij waarmee de eindgebruiker communiceert en vraagt tokens aan van de autorisatieserver. De client moet toestemming krijgen om toegang te krijgen tot de bron door de eigenaar van de resource.
* De **resourceserver** is de plaats waar de bron of gegevens zich bevinden. Het vertrouwt de Autorisatieserver om de OAuth-client veilig te verifiëren en te autoriseren, en gebruikt toegangstokens voor dragers om ervoor te zorgen dat toegang tot een bron kan worden verleend.

## <a name="app-registration"></a>App-registratie

Elke app die zowel persoonlijke als werk- of schoolaccounts wil accepteren, moet worden geregistreerd via de **ervaring met app-registraties** in de [Azure-portal](https://aka.ms/appregistrations) voordat deze gebruikers kunnen worden aangemeld met OAuth 2.0 of OpenID Connect. Het registratieproces van de app verzamelt en wijst een aantal waarden toe aan uw app:

* Een **toepassings-id** die uw app op een unieke basis identificeert
* Een **OmleidingsURI** (optioneel) die kan worden gebruikt om reacties terug te sturen naar uw app
* Een paar andere scenario-specifieke waarden.

Zie [Een app registreren](quickstart-register-app.md) voor meer informatie.

## <a name="endpoints"></a>Eindpunten

Eenmaal geregistreerd communiceert de app met het Microsoft-identiteitsplatform door verzoeken naar het eindpunt te verzenden:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Waar `{tenant}` het kan een van de vier verschillende waarden:

| Waarde | Beschrijving |
| --- | --- |
| `common` | Hiermee kunnen gebruikers met zowel persoonlijke Microsoft-accounts als werk-/schoolaccounts van Azure AD zich aanmelden bij de toepassing. |
| `organizations` | Hiermee kunnen alleen gebruikers met werk-/schoolaccounts van Azure AD zich aanmelden bij de toepassing. |
| `consumers` | Hiermee kunnen alleen gebruikers met persoonlijke Microsoft-accounts (MSA) zich aanmelden bij de toepassing. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` of `contoso.onmicrosoft.com` | Hiermee kunnen alleen gebruikers met werk-/schoolaccounts van een bepaalde Azure AD-tenant zich aanmelden bij de toepassing. De vriendelijke domeinnaam van de Azure AD-tenant of de GUID-id van de tenant kan worden gebruikt. |

Als u wilt weten hoe u met deze eindpunten communiceren, kiest u een bepaald app-type in de sectie [Protocollen](#protocols) en volgt u de koppelingen voor meer informatie.

> [!TIP]
> Elke app die is geregistreerd in Azure AD kan het eindpunt van het Microsoft-identiteitsplatform gebruiken, zelfs als deze zich niet aanmeldt voor persoonlijke accounts.  Op deze manier u bestaande toepassingen migreren naar microsoft-identiteitsplatform en [MSAL](reference-v2-libraries.md) zonder uw toepassing opnieuw te maken.  

## <a name="tokens"></a>Tokens

De implementatie van het Microsoft-identiteitsplatform van OAuth 2.0 en OpenID Connect maakt uitgebreid gebruik van tokens aan toonder, inclusief tokens aan toonder die worden weergegeven als JWT's. Een token aan toonder is een lichtgewicht beveiligingstoken dat de "drager" toegang geeft tot een beveiligde bron. In deze zin is de "drager" elke partij die het token kan presenteren. Hoewel een partij zich eerst moet verifiëren met het Microsoft-identiteitsplatform om het token aan toonder te ontvangen, kan het token worden onderschept en gebruikt door een onbedoelde partij als de vereiste stappen niet worden genomen om het token in transmissie en opslag te beveiligen. Hoewel sommige beveiligingstokens een ingebouwd mechanisme hebben om te voorkomen dat onbevoegde partijen ze gebruiken, hebben tokens aan toonder dit mechanisme niet en moeten ze worden vervoerd in een beveiligd kanaal, zoals transportlaagbeveiliging (HTTPS). Als een token aan toonder in de fout wordt verzonden, kan een kwaadwillende partij een man-in-the-middle-aanval gebruiken om het token te verwerven en te gebruiken voor ongeautoriseerde toegang tot een beveiligde bron. Dezelfde beveiligingsprincipes zijn van toepassing bij het opslaan of caching toonder tokens voor later gebruik. Zorg er altijd voor dat uw app tokens aan toonder op een veilige manier verzendt en opslaat. Zie [RFC 6750 Sectie 5](https://tools.ietf.org/html/rfc6750)voor meer beveiligingsoverwegingen op tokens aan toonder.

Verdere details van verschillende soorten tokens die worden gebruikt in het eindpunt van het Microsoft-identiteitsplatform zijn beschikbaar in [de referentie van het eindpunt van microsoft-identiteitsplatform.](v2-id-and-access-tokens.md)

## <a name="protocols"></a>Protocollen

Als u klaar bent om een aantal voorbeeldaanvragen te zien, gaat u aan de slag met een van de onderstaande zelfstudies. Elk scenario komt overeen met een bepaald verificatiescenario. Als u hulp nodig hebt bij het bepalen wat de juiste stroom voor u is, bekijk dan [de typen apps die u bouwen met het Microsoft-identiteitsplatform.](v2-app-types.md)

* [Mobiele en native applicatie bouwen met OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Web-apps bouwen met OpenID Connect](v2-protocols-oidc.md)
* [Apps met één pagina bouwen met de Impliciete Stroom van OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Bouw daemons of serverprocessen met de OAuth 2.0 clientreferenties flow](v2-oauth2-client-creds-grant-flow.md)
* [Tokens in een web-API krijgen met de OAuth 2.0 namens Flow](v2-oauth2-on-behalf-of-flow.md)
