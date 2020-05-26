---
title: Wat is B2B-samenwerking in Azure Active Directory?
description: Azure Active Directory B2B-samenwerking ondersteunt gastgebruikerstoegang, zodat u veilig resources kunt delen en samenwerken met externe partners.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0effb6bb6e3d3f80775a3cee1ad8a63013a78805
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585815"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Wat is gastgebruikerstoegang in Azure Active Directory B2B?

Azure Active Directory (Azure AD) business-to-business B2B-samenwerking laat u uw bedrijfstoepassingen en services veilig delen met gastgebruikers van andere organisaties, zonder controle te verliezen over uw eigen bedrijfsgegevens. Veilig en zeker werken met externe partners, groot of klein, zelfs als ze niet beschikken over de Azure AD of een IT-afdeling. Een eenvoudig uitnodigings- en inwisselproces laat uw partners hun eigen referenties gebruiken voor toegang tot resources van uw bedrijf. Ontwikkelaars kunnen gebruikmaken van Azure AD business-to-business API's voor het aanpassen van het uitnodigingsproces of toepassingen schrijven, zoals selfservice aanmeldingsportals.

Bekijk de video voor informatie over hoe u veilig kan samenwerken met gastgebruikers door ze uit te nodigen om zich aan te melden bij de apps en services van uw bedrijf met behulp van hun eigen identiteiten.

De volgende video biedt een handig overzicht.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021** heeft Microsoft geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde Azure AD-accounts en -tenants voor B2B-samenwerkingsscenario's. In de voorbereiding raden wij klanten aan om te kiezen voor de [verificatie van de eenmalige wachtwoordcode e-mailen](one-time-passcode.md). We waarderen uw feedback over deze openbare preview-functie en willen graag nog meer manieren te maken om samen te werken.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Samenwerken met een partner met behulp van hun identiteit

Met Azure AD B2B gebruikt de partner hun eigen oplossing voor identiteitsbeheer, zonder externe administratieve overhead voor uw organisatie.

- De partner maakt gebruik van hun eigen identiteiten en referenties; Azure AD is niet vereist.
- U hoeft te beheren externe accounts of wachtwoorden.
- U hoeft accounts niet te synchroniseren of account levenscycli niet te beheren.  

![Schermopname van de pagina Leden toevoegen](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Uitnodigen van gastgebruikers met een eenvoudige uitnodiging en inwisselproces

Gastgebruikers melden zich met hun eigen werk-, school- of sociale identiteiten aan bij uw apps en services. Als de gastgebruiker geen Microsoft-account of een Azure AD-account heeft, wordt er een gemaakt voor hen wanneer ze hun uitnodiging inwisselen. 

- Nodig gastgebruikers uit met behulp van de identiteit van de e-mail van hun keuze.
- Stuur een directe koppeling naar een app of een uitnodiging naar het toegangsvenster van de gastgebruiker.
- Gastgebruikers volgen een paar eenvoudige inwisselstappen om zich aan te melden.

![Schermopname van de pagina Machtigingen controleren](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Gebruik beleidsrichtlijnen voor het veilig delen van uw apps en services

U kunt een autorisatiebeleid voeren om uw zakelijke inhoud te beveiligen. Beleid voor voorwaardelijke toegang zoals meervoudige verificatie kan worden afgedwongen:

- Op tenantniveau.
- Op toepassingsniveau.
- Voor specifieke gastgebruikers om zakelijke apps en gegevens te beschermen.

![Schermopname van de optie Voorwaardelijke toegang](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Gastgebruikers eenvoudig toevoegen in de Azure AD portal

Als beheerder kunt u eenvoudig gastgebruikers toevoegen aan uw organisatie in de Azure Portal.

- Maak een nieuwe gastgebruiker in Azure AD, vergelijkbaar met hoe u een nieuwe gebruiker wilt toevoegen.
- De gastgebruiker ontvangt onmiddellijk een aangepaste uitnodiging waarmee ze aanmelden bij hun Toegangsvenster.
- Gastgebruikers in de map kunnen aan apps of groepen worden toegewezen.  

![Schermopname van de invoerpagina Uitnodiging voor nieuwe gastgebruiker](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Toepassings- en groepseigenaars toestaan om hun eigen gastgebruikers te beheren

U kunt beheer van gastgebruikers overdragen aan toepassingseigenaars zodat ze gastgebruikers rechtstreeks kunnen toevoegen aan een toepassing die ze willen delen, ongeacht of het een Microsoft-toepassing is of niet.

- Administrators stellen selfservice app en groepsbeheer in.
- Niet-administrators gebruiken hun [Toegangsvenster](https://myapps.microsoft.com) om gastgebruikers aan toepassingen of groepen toe te voegen.

![Schermopname van het toegangsvenster voor een gastgebruiker](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>De onboardingervaring voor B2B-gastgebruikers aanpassen

Breng uw externe partners aan boord op manieren die zijn aangepast aan de behoeften van uw organisatie.

- Gebruik [Azure AD-rechtenbeheer](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) om beleidsregels te configureren die [de toegang voor externe gebruikers beheren](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).
- Gebruik de [B2B-samenwerkingsuitnodiging API's](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) voor het aanpassen van uw onboarding-ervaringen.

## <a name="integrate-with-identity-providers"></a>Integreren met id-providers

Azure AD biedt ondersteuning voor externe ID-providers, zoals Facebook, Microsoft-accounts, Google- of zakelijke id-providers. U kunt federatie met id-providers instellen zodat uw externe gebruikers zich kunnen aanmelden met hun bestaande sociale of zakelijke accounts in plaats van een nieuw account te maken voor uw toepassing. Meer informatie over id-providers voor externe identiteiten.

![Schermopname van de pagina Id-providers](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>Een selfservice voor registreren van gasten via gebruikersstromen maken (preview)

Met een selfservice voor het registreren van gebruikers kunt u een aanmeldingservaring maken voor externe gebruikers die toegang willen ontvangen tot uw apps. Als onderdeel van de registratiestroom kunt u opties bieden voor verschillende sociale of zakelijke id-providers, en informatie over de gebruiker verzamelen. Meer informatie over de selfservice-aanmelding en hoe u deze kunt instellen.

![Schermopname van de pagina Gebruikersstromen](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)

## <a name="next-steps"></a>Volgende stappen

- [Licentierichtlijnen voor Azure AD B2B-samenwerking](licensing-guidance.md)
- [Gastgebruikers voor B2B-samenwerking toevoegen in de portal](add-users-administrator.md)
- [Het inwisselproces van de uitnodiging begrijpen](redemption-experience.md)
- En zoals altijd verbinding maken met het productteam voor feedback, discussies en suggesties via onze [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
