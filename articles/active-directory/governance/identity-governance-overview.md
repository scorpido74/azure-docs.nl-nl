---
title: Identity governance-Azure Active Directory | Microsoft Docs
description: Met Azure Active Directory Identity Governance kunt u de behoeften van uw organisatie voor beveiliging en productiviteit van werk nemers verdelen met de juiste processen en zicht baarheid.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063676"
---
# <a name="what-is-azure-ad-identity-governance"></a>Wat is Azure AD Identity Governance?

Met Identity governance van Azure Active Directory (Azure AD) kunt u de behoeften van uw organisatie voor beveiliging en productiviteit van werk nemers balanceren met de juiste processen en zicht baarheid. Het biedt u de mogelijkheid om ervoor te zorgen dat de juiste mensen toegang hebben tot de juiste bronnen. Met deze en verwante Azure AD-en Enterprise Mobility + Security-functies kunt u het risico op toegang beperken door toegang tot kritieke assets te beveiligen, te bewaken en te controleren, en tegelijkertijd de productiviteit van werk nemers en zaken partners te garanderen.  

Identiteits beheer biedt organisaties de mogelijkheid om de volgende taken uit te voeren voor werk nemers, zakelijke partners en leveranciers, en in verschillende services en toepassingen, zowel on-premises als in Clouds:

- De levens cyclus van identiteiten bepalen
- De toegangs levenscyclus bepalen
- Beveiligde privileged Access for Administration

Het is met name bedoeld om organisaties te helpen bij het oplossen van deze vier belang rijke vragen:

- Welke gebruikers moeten toegang hebben tot welke bronnen?
- Wat doen gebruikers met die toegang?
- Zijn er effectief organisatie-instellingen voor het beheren van toegang?
- Kunnen Audi tors controleren of de besturings elementen werken?

## <a name="identity-lifecycle"></a>Levens duur van identiteit

Identiteits beheer helpt organisaties bij het verkrijgen van een evenwicht tussen *productiviteit* : hoe snel kan een persoon toegang krijgen tot de resources die ze nodig hebben, bijvoorbeeld wanneer ze lid worden van mijn organisatie? En *hoe* moet de toegang tot de tijd worden gewijzigd, bijvoorbeeld als gevolg van wijzigingen in de werk status van die persoon?  Het beheer van identiteits levenscyclus is de basis voor Identity governance, en effectief beheer op schaal vereist een modernisering van de infra structuur voor Identity Lifecycle Management voor toepassingen.

![Levens duur van identiteit](./media/identity-governance-overview/identity-lifecycle.png)

Voor veel organisaties is de identiteits levenscyclus voor werk nemers gekoppeld aan de weer gave van die gebruiker in een HCM-systeem (Human Capital Management).  Azure AD Premium onderhoudt automatisch gebruikers identiteiten voor personen die in workday worden weer gegeven in zowel Active Directory als Azure Active Directory, zoals beschreven in de [zelf studie voor inkomende inrichtingen](../saas-apps/workday-inbound-tutorial.md)voor werk dagen.  Azure AD Premium omvat ook [Microsoft Identity Manager](/microsoft-identity-manager/), waarmee u records kunt importeren uit on-PREMISes HCM-systemen zoals SAP, Oracle eBusiness en Oracle People Soft.

Scenario's vereisen steeds meer samen werking met mensen buiten uw organisatie. Met [Azure AD B2B](/azure/active-directory/b2b/) Collaboration kunt u de toepassingen en services van uw organisatie veilig delen met gast gebruikers en externe partners van elke organisatie, terwijl u de controle houdt over uw eigen bedrijfs gegevens.  Met het [beheer recht van Azure AD](entitlement-management-overview.md) kunt u selecteren welke gebruikers van de organisatie toegang mogen aanvragen en als B2B-gasten kunnen worden toegevoegd aan de directory van uw organisatie. Hiermee zorgt u ervoor dat deze gasten worden verwijderd wanneer ze niet langer toegang nodig hebben.

## <a name="access-lifecycle"></a>Toegangs levenscyclus

Organisaties hebben een proces nodig om toegang te beheren dan wat oorspronkelijk is ingericht voor een gebruiker wanneer de identiteit van die gebruiker is gemaakt.  Daarnaast moeten bedrijfs organisaties efficiënt kunnen schalen om het toegangs beleid en besturings elementen continu te kunnen ontwikkelen en afdwingen.

![Toegangs levenscyclus](./media/identity-governance-overview/access-lifecycle.png)

Normaal gesp roken worden de toegangs goedkeurings beslissingen overgedragen aan besluit vormers van uw bedrijf.  Daarnaast kunnen de gebruikers zelf gebruikmaken van deze.  Gebruikers die toegang hebben tot vertrouwelijke klant gegevens in de marketing toepassing van een bedrijf in Europa, moeten bijvoorbeeld het beleid van het bedrijf weten. Gast gebruikers zijn mogelijk niet op de hoogte van de verwerkings vereisten voor gegevens in een organisatie waarvoor ze zijn uitgenodigd.

Organisaties kunnen het toegangs levenscyclus proces automatiseren door middel van technologieën zoals [dynamische groepen](../users-groups-roles/groups-dynamic-membership.md), samen met het inrichten van gebruikers tot [SaaS-apps](../saas-apps/tutorial-list.md) of apps die zijn [geïntegreerd met scim](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organisaties kunnen ook bepalen welke [gast gebruikers toegang hebben tot on-premises toepassingen](../b2b/hybrid-cloud-to-on-premises.md).  Deze toegangs rechten kunnen vervolgens regel matig worden gecontroleerd met behulp van terugkerende [Azure AD-toegangs beoordelingen](access-reviews-overview.md).   Met het [beheer van rechten van Azure AD](entitlement-management-overview.md) kunt u ook definiëren hoe gebruikers toegang aanvragen via pakketten van groeps-en team lidmaatschappen, toepassings rollen en share point online-rollen.

Wanneer een gebruiker probeert toegang te krijgen tot toepassingen, dwingt Azure AD beleids regels voor [voorwaardelijke toegang](/azure/active-directory/conditional-access/) af. Het beleid voor voorwaardelijke toegang kan bijvoorbeeld bestaan uit het weer geven [van gebruiks voorwaarden](../conditional-access/terms-of-use.md) en [ervoor zorgen dat de gebruiker akkoord gaat met deze voor waarden](../conditional-access/require-tou.md) voordat ze toegang kunnen krijgen tot een toepassing.

## <a name="privileged-access-lifecycle"></a>Privileged Access Lifecycle

In het verleden werd bevoorrechte toegang door andere leveranciers als afzonderlijke mogelijkheid van Identity governance beschreven. Bij micro soft denken we echter dat het beheer van bevoegde toegang een belang rijk onderdeel van Identity governance is, met name op basis van het potentieel voor misbruik dat is gekoppeld aan deze beheerders rechten kan een organisatie tot gevolg hebben. De werk nemers, leveranciers en contract ANTEN die gebruikmaken van administratieve rechten, moeten worden geregeld.

![Privileged Access Lifecycle](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) biedt extra besturings elementen die zijn afgestemd op het beveiligen van toegangs rechten voor resources, in azure AD, Azure en andere online services van micro soft.  De just-in-time-toegang en functies voor het wijzigen van de waarschuwingen die worden geboden door Azure AD PIM, naast multi-factor Authentication en voorwaardelijke toegang, bieden een uitgebreide set beheer controles waarmee u de resources van uw bedrijf (Directory, Office 365 en Azure resource roles) kunt beveiligen. Net als bij andere vormen van toegang kunnen organisaties toegangs beoordelingen gebruiken om hercertificering van terugkerende toegang te configureren voor alle gebruikers in beheerders rollen.

## <a name="getting-started"></a>Aan de slag

Bekijk het tabblad aan de slag van **Identity governance** in het Azure Portal om rechten beheer, toegangs beoordelingen, Privileged Identity Management en gebruiksvoorwaarden te gebruiken.

![Aan de slag met Identity governance](./media/identity-governance-overview/getting-started.png)


Als u feedback hebt over de functies voor identiteits beheer, klikt u op **feedback ontvangen?** in de Azure Portal om uw feedback te verzenden. Het team controleert uw feedback regel matig.

Hoewel er voor elke klant geen perfecte oplossing of aanbeveling bestaat, bieden de volgende configuratie handleidingen ook het basislijn beleid dat door micro soft wordt aanbevolen om een veiligere en productief personeel te garanderen.

- [Configuraties voor identiteit en apparaattoegang](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Uitgebreide toegang beveiligen](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Bijlagen-minst geprivilegieerde rollen voor het beheren van Identity governance-functies

Het is een best practice om de minst privileged Role te gebruiken voor het uitvoeren van beheer taken in Identity governance. We raden u aan Azure AD PIM te gebruiken om een rol zo nodig te activeren om deze taken uit te voeren. Hieronder vindt u de minst geprivilegieerde Directory rollen voor het configureren van Identity governance-functies:

| Functie | Minst geprivilegieerde rol |
| ------- | --------------------- |
| Rechtenbeheer | Gebruikers beheerder (met uitzonde ring van het toevoegen van share point online-sites aan catalogi, waarvoor globale beheerder is vereist) |
| Toegangsbeoordelingen | Gebruikers beheerder (met uitzonde ring van toegangs beoordelingen van Azure-of Azure AD-rollen, waarvoor beheerders van een bevoegde rol zijn vereist) |
|Privileged Identity Management | Beheerder van geprivilegieerde rol |
| Gebruiksvoorwaarden | Beveiligings beheerder of beheerder van voorwaardelijke toegang |

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD-rechtenbeheer?](entitlement-management-overview.md)
- [Wat zijn toegangsbeoordelingen in Azure Active Directory?](access-reviews-overview.md)
- [Wat is Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Wat kan ik doen met Gebruiksrechtovereenkomst?](active-directory-tou.md)


