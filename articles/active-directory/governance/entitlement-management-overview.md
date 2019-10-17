---
title: Wat is Azure AD-rechtenbeheer? (Preview)-Azure Active Directory
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
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a467856550bf2deaab931b3fe2f54b7986f12f8a
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430307"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Wat is Azure AD-rechtenbeheer? (Preview)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Werk nemers in organisaties moeten toegang hebben tot verschillende groepen, toepassingen en sites om hun taak uit te voeren. Het beheer van deze toegang is lastig. In de meeste gevallen is er geen geordende lijst met alle resources die een gebruiker nodig heeft voor een project. De project manager heeft een goed inzicht in de benodigde resources, de betrokken personen en hoe lang het duurt voordat het project wordt uitgevoerd. De project manager heeft meestal echter geen machtigingen om anderen goed te keuren of toegang te verlenen. Dit scenario wordt ingewik kelder wanneer u probeert te werken met externe individuen of bedrijven.

Azure Active Directory (Azure AD) rechten beheer kan u helpen bij het beheren van de toegang tot groepen, toepassingen en share point online-sites voor interne gebruikers en ook gebruikers buiten uw organisatie.

In deze video vindt u een overzicht van het rechten beheer en de bijbehorende bedrijfs waarde:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>Waarom het beheer van rechten gebruiken?

Bedrijfs organisaties geven vaak uitdagingen bij het beheren van toegang tot resources, zoals:

- Gebruikers kunnen niet weten welke toegang ze moeten hebben
- Gebruikers kunnen moeite hebben met het vinden van de juiste personen of de juiste resources
- Zodra gebruikers de toegang tot een resource hebben gevonden en ontvangen, kunnen ze meer toegang hebben dan is vereist voor zakelijke doel einden

Deze problemen worden samengesteld voor gebruikers die toegang nodig hebben tot een andere Directory, zoals externe gebruikers die afkomstig zijn van toeleverings keten organisaties of andere zakelijke partners. Bijvoorbeeld:

- Organisaties kunnen niet alle specifieke personen in andere directory's weten om ze te kunnen uitnodigen
- Zelfs als organisaties deze gebruikers kunnen uitnodigen, is het niet meer mogelijk om alle gebruikers toegang consistent te beheren

Het beheer van rechten van Azure AD kan u helpen om deze uitdagingen aan te pakken.

## <a name="what-can-i-do-with-entitlement-management"></a>Wat kan ik doen met het beheer van rechten?

Hier volgen enkele mogelijkheden voor het beheer van rechten:

- Pakketten maken met verwante resources die gebruikers kunnen aanvragen
- Regels definiëren voor het aanvragen van resources en wanneer de toegang verloopt
- De levens cyclus van toegang voor interne en externe gebruikers bepalen
- Beheer van resources delegeren
- Goed keurders aanwijzen om aanvragen goed te keuren
- Rapporten maken om de geschiedenis bij te houden

Bekijk de volgende video van de Ignite 2018-conferentie voor een overzicht van identiteits bestuur en rechten beheer:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Welke resources kan ik beheren?

Dit zijn de typen resources waarmee u toegang tot kunt beheren met het beheer van rechten:

- Azure AD-beveiligings groepen
- Office 365 Groups
- Zakelijke Azure AD-toepassingen, waaronder SaaS-toepassingen en aangepaste, geïntegreerde toepassingen die ondersteuning bieden voor Federatie of inrichting
- Share point online-site verzamelingen en-sites

U kunt ook de toegang beheren tot andere bronnen die afhankelijk zijn van Azure AD-beveiligings groepen of Office 365-groepen.  Bijvoorbeeld:

- U kunt gebruikers licenties geven voor Microsoft Office 365 door een Azure AD-beveiligings groep te gebruiken in een toegangs pakket en op [groepen gebaseerde licentie verlening](../users-groups-roles/licensing-groups-assign.md) voor die groep te configureren
- U kunt gebruikers toegang geven tot het beheer van Azure-resources met behulp van een Azure AD-beveiligings groep in een toegangs pakket en een [Azure-roltoewijzing](../../role-based-access-control/role-assignments-portal.md) voor die groep maken

## <a name="what-are-access-packages-and-policies"></a>Wat zijn toegangs pakketten en beleids regels?

Het beheer van rechten introduceert het concept van een *toegangs pakket*. Een toegangs pakket is een bundel van alle resources die een gebruiker nodig heeft om te werken aan een project of om de taak uit te voeren. De resources bevatten toegang tot groepen, toepassingen of sites. Toegangs pakketten worden gebruikt voor toegang tot uw interne werk nemers en ook gebruikers buiten uw organisatie. Toegangs pakketten worden gedefinieerd in containers die *catalogi*worden genoemd.

Toegangs pakketten bestaan ook uit een of meer *beleids regels*. Een beleid definieert de regels of Guardrails voor toegang tot een toegangs pakket. Als u een beleid inschakelt, wordt alleen de juiste gebruikers toegang verleend tot de juiste resources en voor de juiste hoeveelheid tijd.

![Toegang tot pakket en beleid](./media/entitlement-management-overview/elm-overview-access-package.png)

Met een toegangs pakket en het bijbehorende beleid definieert de Access package manager het volgende:

- Bronnen
- Rollen die de gebruikers nodig hebben voor de resources
- Interne gebruikers en partner organisaties van externe gebruikers die in aanmerking komen om toegang aan te vragen
- Goedkeurings proces en de gebruikers die toegang kunnen goed keuren of weigeren
- Duur van de toegang van de gebruiker

In het volgende diagram ziet u een voor beeld van de verschillende elementen in het recht beheer. Er worden twee voor beelden van toegangs pakketten weer gegeven.

- **Access-pakket 1** bevat één groep als een resource. Toegang is gedefinieerd met een beleid dat een set gebruikers in de Directory in staat stelt om toegang aan te vragen.
- **Access-pakket 2** bevat een groep, een toepassing en een share point online-site als resources. Toegang is gedefinieerd met twee verschillende beleids regels. Met het eerste beleid kunt u een set gebruikers in de Directory toestaan om toegang aan te vragen. Met het tweede beleid kunnen gebruikers in een externe map toegang aanvragen.

![Overzicht van rechten beheer](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>Terminologie

Bekijk de volgende voor waarden om het rechten beheer en de bijbehorende documentatie beter te begrijpen.

| Term of concept | Beschrijving |
| --- | --- |
| beheer rechten | Een service waarmee toegangs pakketten worden toegewezen, ingetrokken en beheerd. |
| toegangs pakket | Een bundel van resources die een team of project nodig heeft en die wordt beheerd door beleid. Een toegangs pakket bevindt zich altijd in een catalogus. |
| toegangs aanvraag | Een aanvraag voor toegang tot de resources in een toegangs pakket. Een aanvraag loopt doorgaans via een werk stroom. |
| policy | Een set regels die de toegangs levenscyclus definieert, bijvoorbeeld hoe gebruikers toegang krijgen, wie kan goed keuren en hoelang gebruikers toegang hebben. Voor beelden van beleids regels zijn toegang tot werk nemers en externe toegang. |
| catalogus | Een container met gerelateerde resources en toegangs pakketten. |
| Algemene catalogus | Een ingebouwde catalogus die altijd beschikbaar is. Om resources toe te voegen aan de algemene catalogus, zijn bepaalde machtigingen vereist. |
| Resource | Een activum of service (zoals een Office-groep, een beveiligings groep, een toepassing of een share point online-site) waaraan een gebruiker machtigingen kan verlenen. |
| Resource type | Het type resource, met inbegrip van groepen, toepassingen en share point online-sites. |
| resource functie | Een verzameling machtigingen die zijn gekoppeld aan een resource. |
| Resource Directory | Een map met een of meer resources die moeten worden gedeeld. |
| toegewezen gebruikers | Een toewijzing van een toegangs pakket aan een gebruiker, zodat de gebruiker alle resource rollen van dat toegangs pakket heeft. |
| Kunt | Het proces van het beschikbaar maken van een toegangs pakket voor gebruikers om aan te vragen. |

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Gespecialiseerde Clouds, zoals Azure Government, Azure Duitsland en Azure China 21Vianet, zijn momenteel niet beschikbaar voor gebruik in deze preview-versie.

### <a name="which-users-must-have-licenses"></a>Welke gebruikers moeten licenties hebben?

Uw Tenant moet ten minste evenveel Azure AD Premium P2-licenties hebben als u actieve leden van de gebruiker hebt. Gebruikers van actieve leden in het recht op beheer zijn onder andere:

- Een gebruiker die een aanvraag voor een toegangs pakket initieert of goedkeurt.
- Een gebruiker aan wie een toegangs pakket is toegewezen. 
- Een gebruiker die toegangs pakketten beheert.

Als onderdeel van de licenties voor leden van gebruikers kunt u ook een aantal gast gebruikers toestaan om te communiceren met het rechten beheer. Zie [Azure Active Directory B2B Collaboration guidance](../b2b/licensing-guidance.md)(Engelstalig) voor meer informatie over het berekenen van het aantal gast gebruikers dat u kunt gebruiken.

Voor informatie over het toewijzen van licenties aan uw gebruikers raadpleegt u [licenties toewijzen of verwijderen via de Azure Active Directory Portal](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: uw eerste toegangs pakket maken](entitlement-management-access-package-first.md)
- [Algemene scenario's](entitlement-management-scenarios.md)
