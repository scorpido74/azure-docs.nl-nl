---
title: Verificatieprotocollen in Azure Active Directory B2C | Microsoft Documenten
description: Apps rechtstreeks bouwen met behulp van de protocollen die worden ondersteund door Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183902"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: verificatieprotocollen
Azure Active Directory B2C (Azure AD B2C) biedt identiteit als service voor uw apps door ondersteuning voor twee industriestandaardprotocollen: OpenID Connect en OAuth.0. De service voldoet aan de normen, maar twee implementaties van deze protocollen kunnen subtiele verschillen hebben.

De informatie in deze handleiding is handig als u uw code schrijft door http-verzoeken rechtstreeks te verzenden en te verwerken, in plaats van met behulp van een open source-bibliotheek. We raden je aan deze pagina te lezen voordat je ingaat op de details van elk specifiek protocol. Maar als u al bekend bent met Azure AD B2C, u rechtstreeks naar [de protocolverwijzingshandleidingen](#protocols)gaan.

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>De basisbeginselen
Elke app die Azure AD B2C gebruikt, moet worden geregistreerd in uw B2C-map in de [Azure-portal.](https://portal.azure.com) Tijdens het registratieproces van de app worden enkele waarden verzameld en toegewezen aan uw app:

* Een **toepassings-id** die de app op unieke wijze identificeert.
* Een **OMLEIDINGs-URI-** of **pakket-id** die kan worden gebruikt om reacties terug te sturen naar uw app.
* Een paar andere scenario-specifieke waarden. Voor meer informatie, meer informatie [over hoe u uw aanvraag registreren.](tutorial-register-applications.md)

Nadat u uw app hebt geregistreerd, wordt deze gecommuniceerd met Azure AD (Azure DIRECTORY) door aanvragen naar het eindpunt te verzenden:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

In bijna alle OAuth- en OpenID Connect-stromen zijn vier partijen betrokken bij de uitwisseling:

![Diagram met de vier OAuth 2.0-rollen](./media/protocols-overview/protocols_roles.png)

* De **autorisatieserver** is het Azure AD-eindpunt. Het behandelt veilig alles wat met gebruikersinformatie en toegang te maken heeft. Het behandelt ook de vertrouwensrelaties tussen de partijen in een stroom. Het is verantwoordelijk voor het verifiëren van de identiteit van de gebruiker, het verlenen en intrekken van toegang tot bronnen en het uitgeven van tokens. Het is ook bekend als de identiteit provider.

* De **eigenaar van** de resource is meestal de eindgebruiker. Het is de partij die eigenaar is van de gegevens, en het heeft de bevoegdheid om derden toegang te geven tot die gegevens of bron.

* De **OAuth-client** is uw app. Het is geïdentificeerd door zijn application ID. Het is meestal de partij die eindgebruikers interactie met. Het vraagt ook tokens van de autorisatieserver. De eigenaar van de resource moet de client toestemming geven om toegang te krijgen tot de bron.

* De **resourceserver** is de plaats waar de bron of gegevens zich bevinden. Het vertrouwt erop dat de autorisatieserver de OAuth-client veilig verifieert en autoriseert. Het maakt ook gebruik van toegangskaarten aan toonder om ervoor te zorgen dat toegang tot een bron kan worden verleend.

## <a name="policies-and-user-flows"></a>Beleid en gebruikersstromen
Het is ongetwijfeld de belangrijkste kenmerken van de service voor Azure AD B2C. Azure AD B2C breidt de standaard OAuth 2.0- en OpenID Connect-protocollen uit met de invoering van beleidsregels. Hierdoor kan Azure AD B2C veel meer uitvoeren dan eenvoudige verificatie en autorisatie.

Om u te helpen bij het instellen van de meest voorkomende identiteitstaken, bevat de Azure AD B2C-portal vooraf gedefinieerde, configureerbare beleidsregels genaamd **gebruikersstromen.** Gebruikersstromen beschrijven volledig de identiteitservaringen van consumenten, waaronder aanmelding, aanmelding en profielbewerking. Gebruikersstromen kunnen worden gedefinieerd in een administratieve gebruikersinterface. Ze kunnen worden uitgevoerd met behulp van een speciale queryparameter in HTTP-verificatieaanvragen.

Beleid en gebruikersstromen zijn geen standaardfuncties van OAuth 2.0 en OpenID Connect, dus u moet de tijd nemen om ze te begrijpen. Zie de [naslaggids voor azure AD B2C-gebruikersstromen voor](user-flow-overview.md)meer informatie .

## <a name="tokens"></a>Tokens
De Azure AD B2C-implementatie van OAuth 2.0 en OpenID Connect maakt uitgebreid gebruik van tokens aan toonder, inclusief tokens aan toonder die worden weergegeven als JSON-webtokens (JWT's). Een token aan toonder is een lichtgewicht beveiligingstoken dat de "drager" toegang geeft tot een beveiligde bron.

De drager is elke partij die het token kan presenteren. Azure AD moet eerst een partij verifiëren voordat deze een token aan toonder kan ontvangen. Maar als de vereiste stappen niet worden genomen om het token in transmissie en opslag te beveiligen, kan het worden onderschept en gebruikt door een onbedoelde partij.

Sommige beveiligingstokens hebben ingebouwde mechanismen die voorkomen dat onbevoegde partijen ze gebruiken, maar tokens aan toonder hebben dit mechanisme niet. Ze moeten worden vervoerd in een beveiligd kanaal, zoals een transportlaagbeveiliging (HTTPS).

Als een token aan toonder wordt verzonden buiten een beveiligd kanaal, kan een kwaadwillende partij een man-in-the-middle-aanval gebruiken om het token te verwerven en het te gebruiken om ongeautoriseerde toegang tot een beveiligde bron te krijgen. Dezelfde beveiligingsprincipes zijn van toepassing wanneer tokens aan toonder worden opgeslagen of in de cache worden opgeslagen voor later gebruik. Zorg er altijd voor dat uw app tokens aan toonder op een veilige manier verzendt en opslaat.

Zie [RFC 6750 Sectie 5](https://tools.ietf.org/html/rfc6750)voor aanvullende overwegingen voor token aan toonder.

Meer informatie over de verschillende typen tokens die worden gebruikt in Azure AD B2C zijn beschikbaar in [de naslaggids voor Azure AD-token](tokens-overview.md).

## <a name="protocols"></a>Protocollen
Wanneer u klaar bent om een aantal voorbeeldaanvragen te bekijken, u beginnen met een van de volgende zelfstudies. Elk komt overeen met een bepaald verificatiescenario. Als u hulp nodig hebt bij het bepalen welke stroom geschikt is voor u, raadpleegt u [de typen apps die u bouwen met Azure AD B2C.](application-types.md)

* [Mobiele en native applicaties bouwen met OAuth 2.0](authorization-code-flow.md)
* [Web-apps bouwen met OpenID Connect](openid-connect.md)
* [Apps met één pagina bouwen met de impliciete stroom van OAuth 2.0](implicit-flow-single-page-application.md)

