---
title: Gebruikersstromen in Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het uitbreidbare beleidskader van Azure Active Directory B2C en hoe u verschillende gebruikersstromen maken.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185604"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Gebruikersstromen in Azure Active Directory B2C

Het uitbreidbare beleidsframework van Azure Active Directory B2C (Azure AD B2C) is de kernsterkte van de service. Beleid beschrijft volledig identiteitservaringen zoals aanmelden, aanmelden of profielbewerking. Om u te helpen bij het instellen van de meest voorkomende identiteitstaken, bevat de Azure AD B2C-portal vooraf gedefinieerde, configureerbare beleidsregels genaamd **gebruikersstromen.**

## <a name="what-are-user-flows"></a>Wat zijn gebruikersstromen?

Met een gebruikersstroom u het gedrag in uw toepassingen beheren door de volgende instellingen te configureren:

- Accounttypen die worden gebruikt voor aanmelden, zoals sociale accounts zoals een Facebook- of lokale account
- Kenmerken die bij de consument moeten worden verzameld, zoals voornaam, postcode en schoenmaat
- Azure Multi-Factor Authentication
- Aanpassing van de gebruikersinterface
- Informatie die de toepassing ontvangt als claims in een token

U veel gebruikersstromen van verschillende typen in uw tenant maken en deze zo nodig in uw toepassingen gebruiken. Gebruikersstromen kunnen worden hergebruikt voor toepassingen. Deze flexibiliteit stelt u in staat om identiteitservaringen te definiëren en aan te passen met minimale of geen wijzigingen in uw code. Uw toepassing activeert een gebruikersstroom met behulp van een standaard HTTP-verificatieverzoek dat een parameter voor gebruikersstroom bevat. Een aangepast [token](tokens-overview.md) wordt ontvangen als antwoord.

In de volgende voorbeelden wordt de parameter 'p'-querytekenreeks weergegeven die de te gebruiken gebruikersstroom opgeeft:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Gebruikersstroomversies

In de Azure-portal worden steeds nieuwe [versies van gebruikersstromen](user-flow-versions.md) toegevoegd. Wanneer u aan de slag gaat met Azure AD B2C, worden geteste gebruikersstromen aanbevolen om te gebruiken. Wanneer u een nieuwe gebruikersstroom maakt, kiest u de gebruikersstroom die u nodig hebt op het **tabblad Aanbevolen.**

De volgende gebruikersstromen worden momenteel aanbevolen:

- **Meld u aan en meld u aan** - Hiermee worden zowel de aanmeldings- als aanmeldingservaringen met één configuratie verwerkt. Gebruikers worden naar het juiste pad geleid, afhankelijk van de context. Het wordt aanbevolen dat u deze gebruikersstroom gebruikt via een **aanmeldingsgebruikersstroom** of een **aanmeldingsgebruikersstroom.**
- **Profielbewerking** - Stelt gebruikers in staat om hun profielgegevens te bewerken.
- **Wachtwoord reset** - Hiermee u configureren of en hoe gebruikers hun wachtwoord kunnen resetten.

## <a name="linking-user-flows"></a>Gebruikersstromen koppelen

Een **aanmeldings- of aanmeldingsstroom** met lokale accounts bevat een **wachtwoord Vergeten?** koppeling op de eerste pagina van de ervaring. Als u op deze koppeling klikt, wordt een gebruikersstroom voor het opnieuw instellen van wachtwoorden niet automatisch geactiveerd.

In plaats daarvan `AADB2C90118` wordt de foutcode teruggestuurd naar uw toepassing. Uw toepassing moet deze foutcode verwerken door een specifieke gebruikersstroom uit te voeren waarmee het wachtwoord wordt gereset. Als u een voorbeeld wilt zien, bekijkt u een [eenvoudig ASP.NET voorbeeld](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) dat de koppeling van gebruikersstromen aantoont.

## <a name="email-address-storage"></a>Opslag van e-mailadressen

Een e-mailadres kan worden vereist als onderdeel van een gebruikersstroom. Als de gebruiker zich verifieert met een aanbieder van sociale identiteit, wordt het e-mailadres opgeslagen in de eigenschap **otherMails.** Als een lokaal account is gebaseerd op een gebruikersnaam, wordt het e-mailadres opgeslagen in een eigenschap met sterke verificatiedetails. Als een lokaal account is gebaseerd op een e-mailadres, wordt het e-mailadres opgeslagen in de eigenschap **signInNames.**

Het e-mailadres wordt in geen van deze gevallen gegarandeerd geverifieerd. Een tenantbeheerder kan e-mailverificatie uitschakelen in het basisbeleid voor lokale accounts. Zelfs als verificatie van e-mailadressen is ingeschakeld, worden adressen niet geverifieerd als ze afkomstig zijn van een aanbieder van sociale identiteit en zijn ze niet gewijzigd.

Alleen de eigenschappen **OtherMails** en **signInNames** worden weergegeven via de Microsoft Graph API. Het e-mailadres in de eigenschap sterke verificatiedetails is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Als u de aanbevolen gebruikersstromen wilt maken, volgt u de instructies in [Zelfstudie: Een gebruikersstroom maken.](tutorial-create-user-flows.md)
