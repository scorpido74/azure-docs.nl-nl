---
title: Verificatie toevoegen aan uw mobiele apps met Visual Studio App Center en Azure-Services
description: Meer informatie over de services, zoals Visual Studio App Center, waarmee u gebruikers verificatie kunt instellen en mobiele toepassingen kunt inschakelen voor verificatie met sociale accounts, Azure Active Directory en aangepaste verificatie.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ce7fcbb9f8386f2d3455168a78d6c0a9ab33a24d
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451016"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Verificatie toevoegen en gebruikers identiteiten beheren in uw mobiele apps

Met een weer gave van de gebruiker en hun gedrag in uw toepassing kunnen ontwikkel aars gebruikers beter helpen bij het maken van aangepaste ervaringen. Of u nu een toepassings ontwikkelaar bent die een samenwerkings toepassing bouwt voor gebruikers binnen uw organisatie of als u het volgende sociale netwerk platform maakt, u hebt een manier nodig om gebruikers te verifiëren en gebruikers identiteiten te beheren. Een service voor identiteits beheer is een van de belangrijkste functies van een mobiele back-end-service.

Gebruik de volgende services om gebruikers authenticatie in te scha kelen in uw mobiele apps.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) is een identiteits beheer service van Business-to-consumer (B2C) die ontwikkel aars kunnen gebruiken om hun klanten te verifiëren. Met deze service met witte labels kunnen ontwikkel aars aanpassen en bepalen hoe gebruikers veilig werken met hun web-, Desktop-, mobiele of single-page toepassingen. Met Azure AD B2C kunnen gebruikers zich registreren, aanmelden, kunnen ze wachtwoorden herstellen en profielen bewerken. In Azure AD B2C is een formulier geïmplementeerd van de OpenID Connect- en OAuth 2.0-protocollen. 

**Belangrijke functies**
- Klanten veilig verifiëren met hun favoriete ID-provider.
- Beheer de identiteit en toegang van klanten.
- Krijg ondersteuning voor aanmelding bij sociale media, zoals Facebook, GitHub, Google, LinkedIn, Twitter, WeChat en Weibo.
- Maak verbinding met uw gebruikers accounts met behulp van de standaard protocollen van de industrie, zoals OpenID Connect Connect of SAML, om identiteits beheer mogelijk te maken op verschillende platforms.
- Geef merk bare registratie en aanmeldings ervaring op.
- U kunt eenvoudig integreren met CRM-data bases, hulpprogram ma's voor marketing analyse en systemen voor account verificatie.
- Aanmeld-, voorkeurs-en conversie gegevens vastleggen voor klanten.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie over Azure AD B2C](/azure/active-directory-b2c/)
- [Snelstartgidsen](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Voorbeelden](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) is de cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft, waarmee uw werk nemers zich kunnen aanmelden en toegang krijgen tot:
- Externe resources, zoals Microsoft Office 365, de Azure Portal en duizenden andere SaaS-toepassingen (Software as a Service).
- Interne resources, zoals apps op het bedrijfsnetwerk en intranet, samen met cloud-apps die door uw eigen organisatie zijn ontwikkeld.

**Belangrijke functies**
- Naadloze en zeer veilige toegang door gebruikers te verbinden met de toepassingen die ze nodig hebben.
- Uitgebreide identiteits beveiliging en verbeterde beveiliging voor identiteiten en toegang op basis van gebruiker, locatie, apparaat, gegevens en toepassings context.
- Duizenden vooraf geïntegreerde apps voor commerciële en aangepaste toepassingen, zoals Office 365, Salesforce.com en Box.
- De mogelijkheid om toegang op schaal te beheren.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Wat is Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Aan de slag met Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Snelstartgidsen](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)