---
title: Wat is Azure AD-rechtenbeheer? -Azure Active Directory
description: Bekijk een overzicht van Azure Active Directory rechten beheer en hoe u dit kunt gebruiken voor het beheren van de toegang tot groepen, toepassingen en share point online-sites voor interne en externe gebruikers.
services: active-directory
documentationCenter: ''
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
ms.openlocfilehash: 1fd72bba6e8c01644adebaa333c8fe588c35b8c7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927319"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Wat is Azure AD-rechtenbeheer?

Azure Active Directory (Azure AD) is een functie voor [identiteits](identity-governance-overview.md) beheer waarmee organisaties identiteits-en toegangs levenscyclus op schaal kunnen beheren door het automatiseren van werk stromen voor toegangs aanvragen, toegangs toewijzingen, recensies en verval datum.

Werk nemers in organisaties moeten toegang hebben tot verschillende groepen, toepassingen en sites om hun taak uit te voeren. Het beheer van deze toegang is lastig, omdat de vereisten veranderen: nieuwe toepassingen worden toegevoegd of gebruikers hebben extra toegangs rechten nodig.  Dit scenario haalt meer gecompliceerd op wanneer u samen met externe organisaties samenwerkt. u weet niet wie in de andere organisatie toegang nodig heeft tot de resources van uw organisatie en weet niet welke toepassingen, groepen of sites uw organisatie gebruikt.

Met het beheer van rechten van Azure AD kunt u de toegang tot groepen, toepassingen en share point online-sites efficiënter beheren voor interne gebruikers, en voor gebruikers buiten uw organisatie die toegang nodig hebben tot deze resources.

## <a name="why-use-entitlement-management"></a>Waarom het beheer van rechten gebruiken?

Bedrijfs organisaties geven vaak uitdagingen bij het beheren van de toegang van werk nemers tot resources, zoals:

- Gebruikers zijn mogelijk niet op de hoogte van de toegang die ze zouden hebben, en zelfs als ze dat doen, kunnen ze de juiste personen mogelijk niet vinden om hun toegang goed te keuren
- Zodra gebruikers de toegang tot een resource hebben gevonden en ontvangen, kunnen ze meer toegang hebben dan is vereist voor zakelijke doel einden

Deze problemen worden samengesteld voor gebruikers die toegang nodig hebben tot een andere organisatie, zoals externe gebruikers die afkomstig zijn van toeleverings keten organisaties of andere zakelijke partners. Bijvoorbeeld:

- Niemand kan niet alle specifieke personen in de directory's van een organisatie weten om ze te kunnen uitnodigen
- Zelfs als ze deze gebruikers kunnen uitnodigen, kan niemand in die organisatie de toegang tot alle gebruikers consistent beheren

Het beheer van rechten van Azure AD kan u helpen om deze uitdagingen aan te pakken.  Als u meer wilt weten over hoe klanten gebruik kunnen maken van het beheer van rechten van Azure AD, kunt u de [Avanade](https://aka.ms/AvanadeELMCase) -casestudy en de [centra](https://aka.ms/CentricaELMCase)-casestudy lezen.  In deze video vindt u een overzicht van het rechten beheer en de bijbehorende waarde:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Wat kan ik doen met het beheer van rechten?

Hier volgen enkele mogelijkheden voor het beheer van rechten:

- Delegeren aan niet-beheerders de mogelijkheid om toegangs pakketten te maken. Deze toegangs pakketten bevatten bronnen die gebruikers kunnen aanvragen en de gedelegeerde toegangs pakket beheerders kunnen beleids regels definiëren waarvoor gebruikers een aanvraag kunnen indienen, die hun toegang moeten goed keuren en wanneer de toegang verloopt.
- Selecteer verbonden organisaties waarvan de gebruikers toegang kunnen aanvragen.  Wanneer een gebruiker die zich nog niet in uw directory bevindt, toegang vraagt en is goedgekeurd, worden ze automatisch uitgenodigd voor de Directory en de toegewezen toegang.  Als de toegang is verlopen en er geen andere toegangs pakket toewijzingen zijn, kunnen hun B2B-account in uw Directory automatisch worden verwijderd.

U kunt aan de slag met onze [zelf studie voor het maken van uw eerste toegangs pakket](entitlement-management-access-package-first.md). U kunt ook de [algemene scenario's](entitlement-management-scenarios.md)lezen of Video's bekijken, met inbegrip van

- [Het beheer van rechten van Azure AD implementeren in uw organisatie](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Uw gebruik van het beheer van rechten van Azure AD bewaken en schalen](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Delegeren in het recht beheer](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Wat zijn toegangs pakketten en welke resources kan ik met hen beheren?

Het beheer van rechten introduceert Azure AD het concept van een *toegangs pakket*. Een toegangs pakket is een bundel van alle resources met de toegang die een gebruiker nodig heeft om te werken aan een project of om de taak uit te voeren. Toegangs pakketten worden gebruikt voor toegang tot uw interne werk nemers en ook gebruikers buiten uw organisatie.

 Dit zijn de typen resources waarmee u de toegang van de gebruiker tot het beheer recht kunt beheren:

- Lidmaatschap van Azure AD-beveiligings groepen
- Lidmaatschap van Office 365-groepen en-teams
- Toewijzing aan Azure AD-zakelijke toepassingen, waaronder SaaS-toepassingen en toepassingen met aangepaste integratie die ondersteuning bieden voor Federatie/eenmalige aanmelding en/of inrichting
- Lidmaatschap van share point online-sites

U kunt ook de toegang beheren tot andere bronnen die afhankelijk zijn van Azure AD-beveiligings groepen of Office 365-groepen.  Bijvoorbeeld:

- U kunt gebruikers licenties geven voor Microsoft Office 365 door een Azure AD-beveiligings groep te gebruiken in een toegangs pakket en op [groepen gebaseerde licentie verlening](../users-groups-roles/licensing-groups-assign.md) voor die groep te configureren
- U kunt gebruikers toegang geven tot het beheer van Azure-resources met behulp van een Azure AD-beveiligings groep in een toegangs pakket en een [Azure-roltoewijzing](../../role-based-access-control/role-assignments-portal.md) voor die groep maken

## <a name="how-do-i-control-who-gets-access"></a>Hoe kan ik besturings element wie toegang krijgt?

Met een toegangs pakket bevat een beheerder of gedelegeerde Access package manager de resources (groepen, apps en sites) en de rollen die de gebruikers nodig hebben voor deze resources.

Toegangs pakketten bestaan ook uit een of meer *beleids regels*. Een beleid definieert de regels of Guardrails voor toewijzing aan toegangs pakket. Elk beleid kan worden gebruikt om ervoor te zorgen dat alleen de juiste gebruikers toegang kunnen vragen, dat er goed keurders zijn voor hun aanvraag en dat hun toegang tot deze resources een beperkt tijds limiet is en verloopt als het niet wordt verlengd.

![Toegang tot pakket en beleid](./media/entitlement-management-overview/elm-overview-access-package.png)

Binnen elk beleid definieert een beheerder of toegangs pakket beheer

- De al bestaande gebruikers (meestal werk nemers of al geuitgenodigde gasten) of de partner organisaties van externe gebruikers die in aanmerking komen voor het aanvragen van toegang
- Het goedkeurings proces en de gebruikers die toegang kunnen goed keuren of weigeren
- De duur van de toegangs toewijzing van een gebruiker na goed keuring voordat de toewijzing verloopt

In het volgende diagram ziet u een voor beeld van de verschillende elementen in het recht beheer. Er wordt één catalogus met twee voor beelden van toegangs pakketten weer gegeven.

- **Access-pakket 1** bevat één groep als een resource. Toegang is gedefinieerd met een beleid dat een set gebruikers in de Directory in staat stelt om toegang aan te vragen.
- **Access-pakket 2** bevat een groep, een toepassing en een share point online-site als resources. Toegang is gedefinieerd met twee verschillende beleids regels. Met het eerste beleid kunt u een set gebruikers in de Directory toestaan om toegang aan te vragen. Met het tweede beleid kunnen gebruikers in een externe map toegang aanvragen.

![Overzicht van rechten beheer](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Wanneer moet ik toegangs pakketten gebruiken?

Toegangs pakketten worden niet vervangen door andere mechanismen voor toegangs toewijzing.  Ze zijn het meest geschikt in situaties zoals de volgende:

- Werk nemers hebben tijd beperkte toegang nodig voor een bepaalde taak.  U kunt bijvoorbeeld groeps-gebaseerde licentie verlening en een dynamische groep gebruiken om ervoor te zorgen dat alle werk nemers over een Exchange Online-postvak beschikken en vervolgens toegangs pakketten gebruiken voor situaties waarin werk nemers extra toegang nodig hebben, zoals het lezen van afdelings resources van een andere vertrouwelijke.
- De toegang moet worden goedgekeurd door de Manager van een werk nemer of door andere aangewezen personen.
- Afdelingen willen hun eigen toegangs beleid voor hun resources beheren zonder IT-betrokkenheid.  
- Er zijn twee of meer organisaties die samen werken aan een project. als gevolg hiervan moeten meerdere gebruikers van één organisatie worden binnengebracht via Azure AD B2B om toegang te krijgen tot de resources van een andere organisatie.

## <a name="how-do-i-delegate-access"></a>Hoe kan ik gemachtigden toegang?

 Toegangs pakketten worden gedefinieerd in containers die *catalogi*worden genoemd.  U kunt één catalogus voor al uw toegangs pakketten hebben, maar u kunt ook personen aanwijzen om hun eigen catalogi te maken. Een beheerder kan resources toevoegen aan elke catalogus, maar een niet-beheerder kan alleen een catalogus toevoegen aan de resources waarvan ze eigenaar zijn. Een catalogus eigenaar kan andere gebruikers toevoegen als catalogus co-eigen aren of als toegangs pakket beheerders.  Deze scenario's worden verder beschreven in het artikel [delegering en rollen in azure AD rechten beheer](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Samen vatting van terminologie

Als u meer inzicht wilt krijgen in het rechten beheer en de bijbehorende documentatie, kunt u teruggaan naar de volgende lijst met voor waarden.

| Termijn | Beschrijving |
| --- | --- |
| toegangs pakket | Een bundel van resources die een team of project nodig heeft en die wordt beheerd door beleid. Een toegangs pakket bevindt zich altijd in een catalogus. U maakt een nieuw toegangs pakket voor een scenario waarin gebruikers toegang moeten aanvragen.  |
| toegangs aanvraag | Een aanvraag voor toegang tot de resources in een toegangs pakket. Een aanvraag loopt doorgaans via een goedkeurings werk stroom.  Als deze is goedgekeurd, ontvangt de gebruiker die de aanvraag heeft ingediend een toegangs pakket toewijzing. |
| toewijzingen | Een toewijzing van een toegangs pakket aan een gebruiker zorgt ervoor dat de gebruiker alle resource rollen van dat toegangs pakket heeft.  Toegangs pakket toewijzingen hebben doorgaans een tijds limiet voordat ze verlopen. |
| catalogus | Een container met gerelateerde resources en toegangs pakketten.  Catalogi worden gebruikt voor delegering, zodat niet-beheerders hun eigen toegangs pakketten kunnen maken. Catalog-eigen aren kunnen resources toevoegen die eigendom zijn van een catalogus. |
| Maker van catalogus | Een verzameling gebruikers die gemachtigd zijn om nieuwe catalogussen te maken.  Wanneer een niet-beheerders gebruiker die gemachtigd is om een catalogus Maker te maken, een nieuwe catalogus maakt, wordt deze automatisch de eigenaar van die catalogus. |
| verbonden organisatie | Een externe Azure AD-Directory of-domein waarmee u een relatie hebt. De gebruikers van een verbonden organisatie kunnen worden opgegeven in een beleid als toestemming om toegang aan te vragen. |
| policy | Een set regels die de toegangs levenscyclus definieert, bijvoorbeeld hoe gebruikers toegang krijgen, wie kan goed keuren en hoelang gebruikers toegang hebben via een toewijzing. Een beleid is gekoppeld aan een toegangs pakket. Een toegangs pakket kan bijvoorbeeld twee beleids regels bevatten: een voor werk nemers om toegang aan te vragen en een tweede voor externe gebruikers om toegang aan te vragen. |
| resource | Een Asset, zoals een Office-groep, een beveiligings groep, een toepassing of een share point online-site, met een rol waaraan een gebruiker machtigingen kan verlenen. |
| Resource Directory | Een map met een of meer resources die moeten worden gedeeld. |
| resource functie | Een verzameling machtigingen die zijn gekoppeld aan en gedefinieerd door een resource. Een groep heeft twee rollen: lid en eigenaar. Share point-sites hebben doorgaans drie rollen, maar kunnen extra aangepaste rollen hebben. Toepassingen kunnen aangepaste rollen hebben. |


## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Gespecialiseerde Clouds, zoals Azure Government, Azure Duitsland en Azure China 21Vianet, zijn momenteel niet beschikbaar voor gebruik.

### <a name="which-users-must-have-licenses"></a>Welke gebruikers moeten licenties hebben?

Uw Tenant moet ten minste evenveel Azure AD Premium P2-licenties hebben als lid van gebruikers actief zijn in het rechts beheer. Gebruikers van actieve leden in het recht op beheer zijn onder andere:

- Een gebruiker die een aanvraag voor een toegangs pakket initieert of goedkeurt.
- Een gebruiker aan wie een toegangs pakket is toegewezen.
- Een gebruiker die toegangs pakketten beheert.

Als onderdeel van de licenties voor leden van gebruikers kunt u ook een aantal gast gebruikers toestaan om te communiceren met het rechten beheer. Zie [Azure Active Directory B2B Collaboration guidance](../b2b/licensing-guidance.md)(Engelstalig) voor meer informatie over het berekenen van het aantal gast gebruikers dat u kunt gebruiken.

Voor informatie over het toewijzen van licenties aan uw gebruikers raadpleegt u [licenties toewijzen of verwijderen via de Azure Active Directory Portal](../fundamentals/license-users-groups.md). Houd er rekening mee dat het rechten beheer momenteel geen licentie toewijzing afdwingt voor gebruikers.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: uw eerste toegangs pakket maken](entitlement-management-access-package-first.md)
- [Algemene scenario's](entitlement-management-scenarios.md)
