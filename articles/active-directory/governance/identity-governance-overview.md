---
title: Identiteitsbeheer - Azure Active Directory | Microsoft Documenten
description: Met Azure Active Directory Identity Governance u de behoefte van uw organisatie aan beveiliging en productiviteit van werknemers in evenwicht brengen met de juiste processen en zichtbaarheid.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063676"
---
# <a name="what-is-azure-ad-identity-governance"></a>Wat is Azure AD Identity Governance?

Met Azure Active Directory (Azure AD) Identity Governance u de behoefte van uw organisatie aan beveiliging en productiviteit van werknemers in evenwicht brengen met de juiste processen en zichtbaarheid. Het biedt u mogelijkheden om ervoor te zorgen dat de juiste mensen de juiste toegang hebben tot de juiste bronnen. Met deze en gerelateerde Azure AD- en Enterprise Mobility + Security-functies u toegangsrisico's beperken door de toegang tot kritieke assets te beschermen, te bewaken en te controleren, terwijl u tegelijkertijd de productiviteit van werknemers en bedrijfspartners garanderen.  

Identity Governance biedt organisaties de mogelijkheid om de volgende taken uit te voeren voor werknemers, zakenpartners en leveranciers, en tussen services en toepassingen, zowel on-premises als in clouds:

- De levenscyclus van de identiteit regelen
- Levenscyclus van toegang regelen
- Beveiligde bevoorrechte toegang voor beheer

Het is specifiek bedoeld om organisaties te helpen deze vier belangrijke vragen aan te pakken:

- Welke gebruikers moeten toegang hebben tot welke bronnen?
- Wat doen die gebruikers met die toegang?
- Zijn er effectieve organisatorische controles voor het beheren van toegang?
- Kunnen auditors controleren of de controles werken?

## <a name="identity-lifecycle"></a>Levenscyclus van identiteit

Identity Governance helpt organisaties een evenwicht te vinden tussen *productiviteit* - Hoe snel kan een persoon toegang hebben tot de resources die hij nodig heeft, bijvoorbeeld wanneer ze lid worden van mijn organisatie? En *veiligheid* - Hoe moet hun toegang veranderen in de tijd, zoals als gevolg van veranderingen in de arbeidsstatus van die persoon?  Identity lifecycle management is de basis voor Identity Governance, en effectief beheer op schaal vereist modernisering van de infrastructuur voor identiteitslevenscyclusbeheer voor toepassingen.

![Levenscyclus van identiteit](./media/identity-governance-overview/identity-lifecycle.png)

Voor veel organisaties is de levenscyclus van identiteiten voor werknemers gekoppeld aan de vertegenwoordiging van die gebruiker in een HCM -systeem (human capital management).  Azure AD Premium onderhoudt automatisch gebruikersidentiteiten voor personen die in Workday zijn vertegenwoordigd in zowel Active Directory als Azure Active Directory, zoals beschreven in de [inkomende inkomende inprovisioning-zelfstudie Workday.](../saas-apps/workday-inbound-tutorial.md)  Azure AD Premium bevat ook [Microsoft Identity Manager,](/microsoft-identity-manager/)dat records kan importeren uit on-premises HCM-systemen zoals SAP, Oracle eBusiness en Oracle PeopleSoft.

Scenario's vereisen steeds vaker samenwerking met mensen buiten uw organisatie. [Azure AD B2B-samenwerking](/azure/active-directory/b2b/) stelt u in staat om de applicaties en services van uw organisatie veilig te delen met gastgebruikers en externe partners van elke organisatie, terwijl u de controle behoudt over uw eigen bedrijfsgegevens.  [Azure AD-rechtenbeheer](entitlement-management-overview.md) stelt u in staat om te selecteren welke gebruikers van de organisatie toegang mogen aanvragen en als B2B-gasten aan de directory van uw organisatie kunnen worden toegevoegd, en zorgt ervoor dat deze gasten worden verwijderd wanneer ze geen toegang meer nodig hebben.

## <a name="access-lifecycle"></a>Toegangslevenscyclus

Organisaties hebben een proces nodig om toegang te beheren die verder gaat dan wat in eerste instantie voor een gebruiker was ingericht toen de identiteit van die gebruiker werd gemaakt.  Bovendien moeten bedrijfsorganisaties efficiënt kunnen schalen om voortdurend toegangsbeleid en -controles te kunnen ontwikkelen en afdwingen.

![Toegangslevenscyclus](./media/identity-governance-overview/access-lifecycle.png)

Doorgaans hebben IT-afgevaardigden toegang tot goedkeuringsbeslissingen voor zakelijke besluitvormers.  Bovendien kan IT de gebruikers zelf betrekken.  Gebruikers die toegang hebben tot vertrouwelijke klantgegevens in de marketingtoepassing van een bedrijf in Europa, moeten bijvoorbeeld het beleid van het bedrijf kennen. Gastgebruikers zijn mogelijk niet op de hoogte van de verwerkingsvereisten voor gegevens in een organisatie waarvoor ze zijn uitgenodigd.

Organisaties kunnen het proces van toegangslevenscyclus automatiseren via technologieën zoals [dynamische groepen](../users-groups-roles/groups-dynamic-membership.md), in combinatie met gebruikersvoorzieningen voor [SaaS-apps](../saas-apps/tutorial-list.md) of [apps die zijn geïntegreerd met SCIM.](../app-provisioning/use-scim-to-provision-users-and-groups.md)  Organisaties kunnen ook bepalen welke [gastgebruikers toegang hebben tot on-premises toepassingen.](../b2b/hybrid-cloud-to-on-premises.md)  Deze toegangsrechten kunnen vervolgens regelmatig worden beoordeeld met terugkerende [Azure AD-toegangsbeoordelingen.](access-reviews-overview.md)   [Azure AD-rechtenbeheer](entitlement-management-overview.md) stelt u ook in staat om te definiëren hoe gebruikers toegang aanvragen voor pakketten van groeps- en teamlidmaatschappen, toepassingsrollen en SharePoint Online-rollen.

Wanneer een gebruiker toegang probeert te krijgen tot toepassingen, dwingt Azure AD [beleid voor voorwaardelijke toegang](/azure/active-directory/conditional-access/) af. Het beleid voor voorwaardelijke toegang kan bijvoorbeeld bestaan uit het weergeven van een [gebruiksvoorwaarden](../conditional-access/terms-of-use.md) en [ervoor zorgen dat de gebruiker met deze voorwaarden heeft ingestemd](../conditional-access/require-tou.md) voordat hij toegang heeft tot een toepassing.

## <a name="privileged-access-lifecycle"></a>Levenscyclus van bevoorrechte toegang

In het verleden is bevoorrechte toegang door andere leveranciers beschreven als een afzonderlijke mogelijkheid van Identiteitsbeheer. Bij Microsoft denken we echter dat het beheren van bevoorrechte toegang een belangrijk onderdeel is van Identity Governance - vooral gezien het potentieel voor misbruik in verband met die beheerdersrechten kan leiden tot een organisatie. De werknemers, leveranciers en contractanten die administratieve rechten overnemen moeten worden geregeld.

![Levenscyclus van bevoorrechte toegang](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) biedt extra besturingselementen die zijn afgestemd op het beveiligen van toegangsrechten voor resources, in Azure AD, Azure en andere Microsoft Online Services.  De just-in-time toegang en de waarschuwingsmogelijkheden voor het waarschuwen van problemen met rollenswijzigingen die worden geboden door Azure AD PIM, naast multi-factor authenticatie en voorwaardelijke toegang, bieden een uitgebreide set beheerbesturingselementen om de bronnen van uw bedrijf te beveiligen (directory, Office 365- en Azure-bronrollen). Net als bij andere vormen van toegang kunnen organisaties toegangsbeoordelingen gebruiken om terugkerende toegangshercertificering te configureren voor alle gebruikers in beheerdersrollen.

## <a name="getting-started"></a>Aan de slag

Bekijk het tabblad Aan de slag met **Identiteitsbeheer** in de Azure-portal om te beginnen met het beheer van rechten, toegangsbeoordelingen, Privileged Identity Management en Gebruiksvoorwaarden.

![Identity Governance aan de slag](./media/identity-governance-overview/getting-started.png)


Als u feedback hebt over functies voor identiteitsbeheer, klikt u op **Feedback ontvangen** in de Azure-portal om uw feedback in te dienen. Het team beoordeelt regelmatig uw feedback.

Hoewel er geen perfecte oplossing of aanbeveling is voor elke klant, bieden de volgende configuratiehandleidingen ook het basisbeleid dat Microsoft aanbeveelt om een veiliger en productiever personeelsbestand te garanderen.

- [Configuraties voor identiteit en apparaattoegang](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Uitgebreide toegang beveiligen](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Bijlage - minst bevoorrechte rollen voor het beheer in functies voor identiteitsbeheer

Het is een beste gewoonte om de minst bevoorrechte rol te gebruiken om administratieve taken uit te voeren in Identity Governance. We raden u aan Azure AD PIM te gebruiken om een rol te activeren die nodig is om deze taken uit te voeren. De volgende zijn de minst bevoorrechte directoryrollen om functies voor identiteitsbeheer te configureren:

| Functie | Minst bevoorrechte rol |
| ------- | --------------------- |
| Rechtenbeheer | Gebruikersbeheerder (met uitzondering van het toevoegen van SharePoint Online-sites aan catalogi, waarvoor globale beheerder vereist is) |
| Toegangsbeoordelingen | Gebruikersbeheerder (met uitzondering van toegangsbeoordelingen van Azure- of Azure AD-rollen, waarvoor bevoorrechte rolbeheerder vereist is) |
|Privileged Identity Management | Bevoegde rolbeheerder |
| Gebruiksvoorwaarden | Beveiligingsbeheerder of beheerder van voorwaardelijke toegang |

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD-rechtenbeheer?](entitlement-management-overview.md)
- [Wat zijn toegangsbeoordelingen in Azure Active Directory?](access-reviews-overview.md)
- [Wat is Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Wat kan ik doen met Gebruiksrechtovereenkomst?](active-directory-tou.md)


