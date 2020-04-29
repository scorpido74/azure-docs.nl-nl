---
title: Wat is B2B-samen werking in Azure Active Directory?
description: Azure Active Directory B2B-samenwerking ondersteunt gastgebruikerstoegang, zodat u veilig resources kunt delen en samenwerken met externe partners.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 167c7f7aa3dbf967575c0ee8905821fb9d3f77ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80050728"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Wat is gastgebruikerstoegang in Azure Active Directory B2B?

Azure Active Directory (Azure AD) business-to-business B2B-samenwerking laat u uw bedrijfstoepassingen en services veilig delen met gastgebruikers van andere organisaties, zonder controle te verliezen over uw eigen bedrijfsgegevens. Veilig en zeker werken met externe partners, groot of klein, zelfs als ze niet beschikken over de Azure AD of een IT-afdeling. Een eenvoudig uitnodigings- en inwisselproces laat uw partners hun eigen referenties gebruiken voor toegang tot resources van uw bedrijf. Ontwikkelaars kunnen gebruikmaken van Azure AD business-to-business API's voor het aanpassen van het uitnodigingsproces of toepassingen schrijven, zoals selfservice aanmeldingsportals.

Bekijk de video voor informatie over hoe u veilig kan samenwerken met gastgebruikers door ze uit te nodigen om zich aan te melden bij de apps en services van uw bedrijf met behulp van hun eigen identiteiten.

De volgende video biedt een handig overzicht.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021**biedt micro soft geen ondersteuning meer voor de terugbetaling van uitnodigingen door het maken van niet-beheerde Azure AD-accounts en-tenants voor B2B-samenwerkings scenario's. In de voor bereiding raden wij klanten aan om te kiezen voor [verificatie via een eenmalige wachtwoord code](one-time-passcode.md). We hebben uw feedback over deze open bare preview-functie en zijn enthousiast om nog meer manieren te maken om samen te werken.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Samenwerken met een partner met behulp van hun identiteit

Met Azure AD B2B gebruikt de partner hun eigen oplossing voor identiteitsbeheer, zonder externe administratieve overhead voor uw organisatie.

- De partner maakt gebruik van hun eigen identiteiten en referenties; Azure AD is niet vereist.
- U hoeft te beheren externe accounts of wachtwoorden.
- U hoeft accounts niet te synchroniseren of account levenscycli niet te beheren.  

![Scherm opname met de pagina leden toevoegen](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Uitnodigen van gastgebruikers met een eenvoudige uitnodiging en inwisselproces

Gastgebruikers melden zich met hun eigen werk-, school- of sociale identiteiten aan bij uw apps en services. Als de gastgebruiker geen Microsoft-account of een Azure AD-account heeft, wordt er een gemaakt voor hen wanneer ze hun uitnodiging inwisselen. 

- Nodig gastgebruikers uit met behulp van de identiteit van de e-mail van hun keuze.
- Stuur een directe koppeling naar een app of een uitnodiging naar het toegangsvenster van de gastgebruiker.
- Gastgebruikers volgen een paar eenvoudige inwisselstappen om zich aan te melden.

![Scherm opname met de pagina Machtigingen controleren](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Gebruik beleidsrichtlijnen voor het veilig delen van uw apps en services

U kunt autorisatie beleid gebruiken om uw bedrijfs inhoud te beschermen. Beleids regels voor voorwaardelijke toegang, zoals multi-factor Authentication, kunnen worden afgedwongen:

- Op tenantniveau.
- Op toepassingsniveau.
- Voor specifieke gastgebruikers om zakelijke apps en gegevens te beschermen.

![Scherm afbeelding van de optie voorwaardelijke toegang](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Gastgebruikers eenvoudig toevoegen in de Azure AD portal

Als beheerder kunt u eenvoudig gastgebruikers toevoegen aan uw organisatie in de Azure Portal.

- Maak een nieuwe gastgebruiker in Azure AD, vergelijkbaar met hoe u een nieuwe gebruiker wilt toevoegen.
- De gastgebruiker ontvangt onmiddellijk een aangepaste uitnodiging waarmee ze aanmelden bij hun Toegangsvenster.
- Gastgebruikers in de map kunnen aan apps of groepen worden toegewezen.  

![Scherm opname van de pagina met de nieuwe uitnodiging voor gast gebruikers](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Toepassings- en groepseigenaars toestaan om hun eigen gastgebruikers te beheren

U kunt beheer van gastgebruikers overdragen aan toepassingseigenaars zodat ze gastgebruikers rechtstreeks kunnen toevoegen aan een toepassing die ze willen delen, ongeacht of het een Microsoft-toepassing is of niet.

- Administrators stellen selfservice app en groepsbeheer in.
- Niet-administrators gebruiken hun [Toegangsvenster](https://myapps.microsoft.com) om gastgebruikers aan toepassingen of groepen toe te voegen.

![Scherm opname van het toegangs venster voor een gast gebruiker](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>De voorbereidings ervaring voor B2B-gast gebruikers aanpassen

Breng uw externe partners aan boord op manieren die zijn aangepast aan de behoeften van uw organisatie.

- Gebruik het [beheer recht van Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) om beleids regels te configureren die [toegang beheren voor externe gebruikers](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).
- Gebruik de [api's voor B2B-samen werking](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) om uw onboarding-ervaringen aan te passen.

## <a name="next-steps"></a>Volgende stappen

- [Licentierichtlijnen voor Azure AD B2B-samenwerking](licensing-guidance.md)
- [Gastgebruikers voor B2B-samenwerking toevoegen in de portal](add-users-administrator.md)
- [Het inwisselproces van de uitnodiging begrijpen](redemption-experience.md)
- En zoals altijd verbinding maken met het productteam voor feedback, discussies en suggesties via onze [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
