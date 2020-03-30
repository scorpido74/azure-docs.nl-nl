---
title: Verificatie toevoegen aan uw mobiele apps met Visual Studio App Center en Azure-services
description: Meer informatie over de services zoals Visual Studio App Center waarmee gebruikersverificatie kan worden ingesteld en mobiele toepassingen kunnen worden geverifieerd met sociale accounts, Azure Active Directory en aangepaste verificatie.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241041"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Verificatie toevoegen en gebruikersidentiteiten beheren in uw mobiele apps

Door een beeld te hebben van de gebruiker en hun gedrag in uw toepassing kunnen ontwikkelaars gebruikers beter betrekken door op maat gemaakte ervaringen voor hen te creëren. Of u nu een toepassingsontwikkelaar bent die een samenwerkingstoepassing bouwt voor gebruikers binnen uw organisatie of het volgende sociale netwerkplatform maakt, u hebt een manier nodig om gebruikers te authenticeren en gebruikersidentiteiten te beheren. Een identity management service is een van de belangrijkste kenmerken van een mobiele back-end service.

Gebruik de volgende services om gebruikersverificatie in te schakelen in uw mobiele apps.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth](/appcenter/auth/) is een cloudgebaseerde identiteitsbeheerservice die ontwikkelaars kunnen gebruiken om gebruikers te verifiëren en gebruikersidentiteiten te beheren. App Center Auth integreert ook met andere delen van Visual Studio App Center. Ontwikkelaars kunnen de gebruikersidentiteit gebruiken om [gebruikersgegevens](/appcenter/data/index) in andere services te bekijken en zelfs [pushmeldingen naar gebruikers te verzenden in plaats van naar afzonderlijke apparaten.](/appcenter/push/push-to-user#setting-user-identity) 

**Belangrijke functies**
- Aangedreven door Azure Active Directory B2C (Azure AD B2C). 
    - Ondernemingsniveau.
    - Zeer beschikbaar.
    - Veilige en wereldwijde service.
- Breng uw eigen identiteit en de mogelijkheid om andere populaire leveranciers van identiteits- en toegangsbeheer te gebruiken, zoals Auth0 en Firebase.
- Ondersteuning voor Azure Active Directory.
    - Bestaande Azure AD-tenants verbinden. 
    - Authenticeren inschakelen tegen een bedrijfsdomein.
    - Beheer de toegang tot gevoelige gegevens.
- Eenvoudige gebruikerservaring en magische SDK-ervaring door Microsoft Authentication Library in te pakken met de Visual Studio App Center SDK.
- Platformondersteuning voor iOS, Android, Xamarin en React Native.

**Verwijzingen**
- [Meld u aan bij Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Aan de slag met App Center Auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) is een B2C-service (Business-to-consumer) (identity management) die ontwikkelaars kunnen gebruiken om hun klanten te verifiëren. Met deze white-labelservice kunnen ontwikkelaars aanpassen en bepalen hoe gebruikers veilig omgaan met hun web-, desktop-, mobiele of single-page toepassingen. Met Azure AD B2C kunnen gebruikers zich registreren, aanmelden, kunnen ze wachtwoorden herstellen en profielen bewerken. In Azure AD B2C is een formulier geïmplementeerd van de OpenID Connect- en OAuth 2.0-protocollen. 

**Belangrijke functies**
- Verifieer klanten veilig met hun voorkeursidentiteitsprovider.
- Beheer de identiteit en toegang tot klanten.
- Krijg aanmeldingsondersteuning voor sociale media zoals Facebook, GitHub, Google, LinkedIn, Twitter, WeChat en Weibo.
- Maak verbinding met uw gebruikersaccounts via industriestandaardprotocollen, zoals OpenID Connect of SAML, om identiteitsbeheer op verschillende platforms mogelijk te maken.
- Bied merkregistratie- en aanmeldingservaringen.
- Eenvoudig te integreren met CRM-databases, marketinganalysetools en accountverificatiesystemen.
- Vastleggen van aanmeldings-, voorkeurs- en conversiegegevens voor klanten.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Azure AD B2C-documentatie](/azure/active-directory-b2c/)
- [Snel gestart](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Monsters](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) is de cloudgebaseerde service voor identiteits- en toegangsbeheer van Microsoft, waarmee uw werknemers zich kunnen aanmelden en toegang kunnen krijgen tot:
- Externe bronnen, zoals Microsoft Office 365, de Azure-portal en duizenden andere software-as-a-servicetoepassingen (SaaS).
- Interne resources, zoals apps op het bedrijfsnetwerk en intranet, samen met cloud-apps die door uw eigen organisatie zijn ontwikkeld.

**Belangrijke functies**
- Naadloze, zeer veilige toegang door gebruikers te verbinden met de toepassingen die ze nodig hebben.
- Uitgebreide identiteitsbescherming en verbeterde beveiliging voor identiteiten en toegang op basis van gebruikers, locatie, apparaat, gegevens en toepassingscontext.
- Duizenden vooraf geïntegreerde apps voor zowel commerciële als aangepaste toepassingen, zoals Office 365, Salesforce.com en Box.
- Mogelijkheid om toegang op schaal te beheren.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Wat is Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Aan de slag met Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Snel gestart](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)