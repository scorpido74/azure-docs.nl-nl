---
title: Verificatie toevoegen aan uw mobiele apps met Visual Studio App Center en Azure-Services
description: Meer informatie over de services, zoals App Center, waarmee u gebruikers verificatie kunt instellen en mobiele toepassingen kunt inschakelen voor verificatie met sociale accounts, Azure Active Directory en aangepaste verificatie.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795755"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Verificatie toevoegen en gebruikers identiteiten beheren in uw mobiele apps

Met een weer gave van de gebruiker en hun gedrag in uw toepassing kunnen ontwikkel aars gebruikers beter helpen bij het maken van aangepaste ervaringen. Of u nu een toepassings ontwikkelaar bent die een samenwerkings toepassing bouwt voor gebruikers binnen uw organisatie of het volgende sociale netwerk platform maakt, u hebt een manier nodig om gebruikers te verifiëren en gebruikers identiteiten te beheren. Het gebruik van een service voor identiteits beheer is een van de belangrijkste functies van een mobiele back-upservice.

Gebruik de volgende services om gebruikers authenticatie in te scha kelen in uw mobiele apps.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center auth](/appcenter/auth/) is een op de cloud gebaseerde identiteits beheer service waarmee ontwikkel aars gebruikers kunnen verifiëren en gebruikers identiteiten kunnen beheren. App Center auth kan ook worden geïntegreerd met andere onderdelen van App Center, waardoor ontwikkel aars de gebruikers identiteit kunnen gebruiken om [gebruikers gegevens](/appcenter/data/index) in andere services te bekijken en zelfs [Push meldingen naar gebruikers te verzenden in plaats van afzonderlijke apparaten](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Belangrijkste functies**
- **Aangedreven door Azure Active Directory B2C (Azure AD B2C)** 
    - Bedrijfs kwaliteit.
    - Maxi maal beschikbaar.
    - Veilige en wereld wijde service.
- **Breng uw eigen identiteit** en gebruik andere populaire providers voor identiteits-en toegangs beheer, zoals Auth0 en Firebase.
- **Ondersteuning voor AAD** 
    - Bestaande AAD-tenants verbinden. 
    - Verificatie op basis van een bedrijfs domein inschakelen.
    - Toegang tot gevoelige gegevens beheren.
- **Eenvoudige gebruikers ervaring** en Magical SDK-ervaring door de MSAL-bibliotheek met app Center SDK in te pakken.
- **Platform ondersteuning** : IOS, Android, Xamarin, reageren systeem eigen.

**Referentie**
- [Meld u aan met App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Aan de slag met App Center auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) is een Business-to-consumer identiteits beheer service waarmee ontwikkel aars hun klanten kunnen verifiëren. Met deze service met witte labels kunnen ontwikkel aars aanpassen en bepalen hoe gebruikers veilig werken met hun web-, Desktop-, mobiele of single-page toepassingen. Met Azure AD B2C kunnen gebruikers zich registreren, aanmelden, kunnen ze wachtwoorden herstellen en profielen bewerken. In Azure AD B2C is een formulier geïmplementeerd van de OpenID Connect- en OAuth 2.0-protocollen. 

**Belangrijkste functies**
- Klanten veilig verifiëren met hun favoriete ID-provider.
- **Identiteits-en toegangs beheer van klanten**.
- Sociale aanmeldingen die worden ondersteund, zoals Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo.
- Maak verbinding met uw gebruikers accounts met behulp van **industrie standaard protocollen** zoals OpenID Connect Connect of SAML om identiteits beheer mogelijk te maken op verschillende platforms.
- Geef merk registratie en aanmeldings ervaringen op.
- U kunt eenvoudig integreren met CRM-data bases, hulpprogram ma's voor marketing analyse en systemen voor account verificatie.
- Leg aanmeldings-, voorkeurs-en conversie gegevens vast voor klanten.

**Referentie**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie over Azure AD B2C](/azure/active-directory-b2c/)
- [Snelstartgidsen](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Voorbeelden](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) is de cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft, waarmee uw mede werkers zich kunnen aanmelden en toegang krijgen.
- Externe resources, zoals Microsoft Office 365, de Azure-portal en duizenden andere SaaS-toepassingen.
- Interne resources, zoals apps op het bedrijfsnetwerk en intranet, samen met cloud-apps die door uw eigen organisatie zijn ontwikkeld.

**Belangrijkste functies**
- **Naadloze en zeer veilige toegang** door gebruikers te verbinden met de toepassingen die ze nodig hebben.
- **Uitgebreide identiteits beveiliging** en verbeterde beveiliging voor identiteiten en toegang op basis van gebruiker, locatie, apparaat, gegevens en toepassings context.
- **Duizenden vooraf geïntegreerde apps** , zowel commerciële als aangepaste toepassingen zoals Office 365, Salesforce.com en Box.
- **Toegang op schaal beheren**.

**Referentie**
- [Azure-portal](https://portal.azure.com/)
- [Wat is Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Aan de slag met Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Snelstartgidsen](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)