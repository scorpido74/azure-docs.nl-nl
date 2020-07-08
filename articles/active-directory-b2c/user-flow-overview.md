---
title: Gebruikers stromen in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het flexibele beleids raamwerk van Azure Active Directory B2C en het maken van verschillende gebruikers stromen.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78185604"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Gebruikers stromen in Azure Active Directory B2C

Het Extensible Policy Framework van Azure Active Directory B2C (Azure AD B2C) is de kern sterkte van de service. Het beleid beschrijft volledige identiteits ervaringen, zoals aanmelden, aanmelden of profiel bewerking. Om u te helpen bij het instellen van de meest voorkomende identiteits taken, bevat de Azure AD B2C Portal vooraf gedefinieerde, Configureer bare beleids regels met de naam **gebruikers stromen**.

## <a name="what-are-user-flows"></a>Wat zijn gebruikers stromen?

Met een gebruikers stroom kunt u gedrag in uw toepassingen beheren door de volgende instellingen te configureren:

- Account typen die worden gebruikt voor aanmelding, zoals sociale accounts zoals een Facebook-of lokale account
- Kenmerken die moeten worden verzameld van de consument, zoals de voor naam, de post code en de grootte van de schoenen
- Azure Multi-Factor Authentication
- Aanpassing van de gebruikersinterface
- Informatie die de toepassing ontvangt als claims in een token

U kunt veel gebruikers stromen van verschillende typen in uw Tenant maken en deze naar behoefte gebruiken in uw toepassingen. Gebruikers stromen kunnen opnieuw worden gebruikt in verschillende toepassingen. Met deze flexibiliteit kunt u identiteits ervaringen definiëren en wijzigen met minimale of geen wijzigingen in uw code. Uw toepassing activeert een gebruikers stroom met behulp van een standaard-HTTP-verificatie aanvraag die een para meter voor de gebruikers stroom bevat. Er wordt een aangepast [token](tokens-overview.md) ontvangen als antwoord.

In de volgende voor beelden ziet u de query reeks parameter "p" waarmee de gebruikers stroom wordt opgegeven die moet worden gebruikt:

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

In de Azure Portal worden er voortdurend nieuwe [versies van gebruikers stromen](user-flow-versions.md) toegevoegd. Wanneer u aan de slag gaat met Azure AD B2C, worden geteste gebruikers stromen aanbevolen voor gebruik. Wanneer u een nieuwe gebruikers stroom maakt, kiest u de gebruikers stroom die u nodig hebt op het tabblad **Aanbevolen** .

De volgende gebruikers stromen worden momenteel aanbevolen:

- **Meld u aan en meld u** aan voor beide registratie-en aanmeldings ervaringen met één configuratie. Gebruikers ondervinden het pad naar de juiste locatie, afhankelijk van de context. Het is raadzaam deze gebruikers stroom te gebruiken via een **aanmeldings stroom van** een gebruiker of een gebruikers stroom **voor het aanmelden** .
- **Profiel bewerken** : Hiermee kunnen gebruikers hun profiel gegevens bewerken.
- **Wacht woord opnieuw instellen** : Hiermee kunt u configureren of en hoe gebruikers hun wacht woord opnieuw kunnen instellen.

## <a name="linking-user-flows"></a>Gebruikers stromen koppelen

Een **registratie-of aanmeldings** gebruikers stroom met lokale accounts bevat een **verg eten wacht woord?** koppeling op de eerste pagina van de ervaring. Als u op deze koppeling klikt, wordt de gebruikers stroom voor wacht woord opnieuw instellen niet automatisch geactiveerd.

In plaats daarvan wordt de fout code `AADB2C90118` geretourneerd naar uw toepassing. Uw toepassing moet deze fout code verwerken door een specifieke gebruikers stroom uit te voeren waarmee het wacht woord opnieuw wordt ingesteld. Bekijk een voor beeld met een [eenvoudig ASP.net](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) -voor beeld dat het koppelen van gebruikers stromen laat zien.

## <a name="email-address-storage"></a>E-mail adres opslag

Een e-mail adres kan worden vereist als onderdeel van een gebruikers stroom. Als de gebruiker zich verifieert bij een sociale ID-provider, wordt het e-mail adres opgeslagen in de eigenschap **otherMails** . Als een lokaal account is gebaseerd op een gebruikers naam, wordt het e-mail adres opgeslagen in een eigenschap met een sterke authenticatie Details. Als een lokaal account is gebaseerd op een e-mail adres, wordt het e-mail adres opgeslagen in de eigenschap **signInNames** .

Het e-mail adres is niet gegarandeerd om in een van deze gevallen te worden geverifieerd. Een Tenant beheerder kan e-mail verificatie uitschakelen in het basis beleid voor lokale accounts. Zelfs als verificatie via e-mail adres is ingeschakeld, worden adressen niet gecontroleerd als ze afkomstig zijn van een sociale ID-provider en ze zijn niet gewijzigd.

Alleen de eigenschappen **otherMails** en **signInNames** worden weer gegeven via de Microsoft Graph-API. Het e-mail adres in de detail eigenschap sterke authenticatie is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Als u de aanbevolen gebruikers stromen wilt maken, volgt u de instructies in de [zelf studie: een gebruikers stroom maken](tutorial-create-user-flows.md).
