---
title: App &-brontoegang beheren met groepen - Azure AD
description: Meer informatie over het beheren van toegang tot de cloudgebaseerde apps, on-premises apps en resources van uw organisatie met Azure Active Directory-groepen.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89c4fa536994ecc8cecb50c907d43df2110be7df
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982596"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Toegang tot apps en resources beheren met Azure Active Directory-groepen
Met Azure Active Directory (Azure AD) u groepen gebruiken om toegang te beheren tot uw cloudgebaseerde apps, on-premises apps en uw resources. Uw resources kunnen deel uitmaken van de Azure AD-organisatie, zoals machtigingen voor het beheren van objecten via rollen in Azure AD of extern aan de organisatie, zoals voor SaaS-apps (Software as a Service), Azure-services, SharePoint-sites en on-premises bronnen.

>[!NOTE]
> In de Azure-portal u enkele groepen zien waarvan u het lidmaatschap en de groepsgegevens niet beheren in de portal:
>
> - Groepen die worden gesynchroniseerd vanuit on-premises Active Directory, kunnen alleen worden beheerd in on-premises Active Directory.
> - Andere groepstypen, zoals distributielijsten en beveiligingsgroepen met e-mail, worden alleen beheerd in het Exchange-beheercentrum of het Microsoft 365-beheercentrum. U moet zich aanmelden bij het Exchange-beheercentrum of het Microsoft 365-beheercentrum om deze groepen te beheren.

## <a name="how-access-management-in-azure-ad-works"></a>Hoe toegangsbeheer in Azure AD werkt

Azure AD helpt u toegang te geven tot de bronnen van uw organisatie door toegangsrechten te bieden aan één gebruiker of aan een hele Azure AD-groep. Met behulp van groepen kan de resource-eigenaar (of de Azure AD-Directory-eigenaar) een reeks toegangsmachtigingen toewijzen aan alle leden van de groep, in plaats van de rechten één voor één te verstrekken. De bron- of directory-eigenaar kan ook beheerrechten voor de ledenlijst aan iemand anders geven, zoals een afdelingsmanager of een helpdeskbeheerder, zodat die persoon desnoodzaak leden kan toevoegen en verwijderen. Zie Groepseigenaren beheren voor meer informatie over het beheren [van groepseigenaren](active-directory-accessmanagement-managing-group-owners.md)

![Schema van toegangsbeheer in Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Manieren om toegangsrechten toe te wijzen

Er zijn vier manieren om brontoegangsrechten toe te wijzen aan uw gebruikers:

- **Directe opdracht.** De eigenaar van de resource wijst de gebruiker rechtstreeks toe aan de bron.

- **Groepsopdracht.** De eigenaar van de resource wijst een Azure AD-groep toe aan de bron, waardoor alle groepsleden automatisch toegang krijgen tot de bron. Het groepslidmaatschap wordt beheerd door zowel de eigenaar van de groep als de eigenaar van de bron, zodat de eigenaar leden uit de groep kan toevoegen of verwijderen. Zie [Een groep toevoegen of verwijderen uit een andere groep toevoegen of verwijderen met behulp van de Azure Active Directory-portal](active-directory-groups-membership-azure-portal.md)voor meer informatie over het toevoegen of verwijderen van groepslidmaatschap. 

- **Op regels gebaseerde toewijzing.** De eigenaar van de resource maakt een groep en gebruikt een regel om te bepalen welke gebruikers aan een specifieke bron zijn toegewezen. De regel is gebaseerd op kenmerken die zijn toegewezen aan individuele gebruikers. De eigenaar van de resource beheert de regel en bepaalt welke kenmerken en waarden nodig zijn om toegang tot de bron toe te staan. Zie [Een dynamische groep maken en status controleren voor](../users-groups-roles/groups-create-rule.md)meer informatie.

    U deze korte video ook bekijken voor een snelle uitleg over het maken en gebruiken van dynamische groepen:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Opdracht van externe autoriteiten.** Toegang is afkomstig van een externe bron, zoals een on-premises directory of een SaaS-app. In deze situatie wijst de eigenaar van de resource een groep toe om toegang te bieden tot de bron en vervolgens beheert de externe bron de groepsleden.

   ![Schema van toegangsbeheer](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Kunnen gebruikers lid worden van groepen zonder te worden toegewezen?
De eigenaar van de groep kan gebruikers hun eigen groepen laten vinden om lid te worden, in plaats van ze toe te voegen. De eigenaar kan de groep ook zo instellen dat alle gebruikers die lid worden of goedkeuring nodig hebben, automatisch worden geaccepteerd.

Nadat een gebruiker een groepslid heeft aangevraagd, wordt de aanvraag doorgestuurd naar de eigenaar van de groep. Als dit nodig is, kan de eigenaar het verzoek goedkeuren en wordt de gebruiker op de hoogte gesteld van het groepslidmaatschap. Als u echter meerdere eigenaren hebt en een van hen afkeurt, wordt de gebruiker hiervan op de hoogte gesteld, maar wordt deze niet aan de groep toegevoegd. Zie [Azure AD instellen zodat gebruikers kunnen vragen om lid te worden van groepen voor](../users-groups-roles/groups-self-service-management.md) meer informatie en instructies over het aanvragen van uw gebruikers om lid te worden van groepen.

## <a name="next-steps"></a>Volgende stappen
Nu u een beetje een inleiding tot toegangsbeheer met behulp van groepen hebt, begint u uw resources en apps te beheren.

- [Een nieuwe groep maken met Azure Active Directory](active-directory-groups-create-azure-portal.md) of Een nieuwe groep maken en beheren met [PowerShell-cmdlets](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Groepen gebruiken om toegang toe te wijzen aan een geïntegreerde SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Een on-premises groep synchroniseren met Azure met Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
