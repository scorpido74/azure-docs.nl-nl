---
title: Een groep gebruiken voor het beheren van toegang tot SaaS-apps-Azure AD | Microsoft Docs
description: Groepen in Azure Active Directory gebruiken om toegang toe te wijzen aan SaaS-toepassingen die zijn geïntegreerd met Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51375f057543c86fe021822eb9722ffd1be16804
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74026856"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Een groep gebruiken om SaaS-toepassingen te beheren

Met Azure Active Directory (Azure AD) met een Azure AD Premium licentie abonnement kunt u groepen gebruiken om toegang toe te wijzen aan een SaaS-toepassing die is geïntegreerd met Azure AD. Als u bijvoorbeeld toegang wilt toewijzen voor de marketing afdeling voor het gebruik van vijf verschillende SaaS-toepassingen, kunt u een groep maken die de gebruikers in de marketing afdeling bevat en die groep vervolgens toewijzen aan deze vijf SaaS-toepassingen die nodig zijn voor de marketing afdeling. Op deze manier kunt u tijd besparen door het lidmaatschap van de marketing afdeling op één plek te beheren. Gebruikers worden vervolgens toegewezen aan de toepassing wanneer ze worden toegevoegd als leden van de marketing groep en hun toewijzingen worden verwijderd uit de toepassing wanneer ze uit de groep Marketing worden verwijderd. Deze mogelijkheid kan worden gebruikt met honderden toepassingen die u kunt toevoegen in de Azure AD-toepassings galerie.

> [!IMPORTANT]
> U kunt deze functie alleen gebruiken nadat u een Azure AD Premium proef abonnement hebt gestart of Azure AD Premium licentie plan hebt aangeschaft.
> Toewijzing op basis van een groep wordt alleen ondersteund voor beveiligings groepen.
> Op dit moment wordt genest groepslidmaatschap niet ondersteund voor toewijzing aan een toepassing op basis van een groep.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Toegang voor een gebruiker of groep toewijzen aan een SaaS-toepassing

1. Selecteer in het [Azure AD-beheer centrum](https://aad.portal.azure.com) **bedrijfs toepassingen**.
2. Selecteer een toepassing die u hebt toegevoegd in de toepassings galerie om deze te openen.
3. Selecteer **gebruikers en groepen**en selecteer vervolgens **gebruiker toevoegen**.
4. Bij **toewijzing toevoegen**selecteert u **gebruikers en groepen** om de selectie lijst **gebruikers en groepen** te openen.
6. Selecteer zoveel groepen of gebruikers als u wilt en klik of tik op **selecteren** om ze toe te voegen aan de lijst **toewijzing toevoegen** . U kunt in deze fase ook een rol toewijzen aan een gebruiker.
7. Selecteer **toewijzen** om de gebruikers of groepen toe te wijzen aan de geselecteerde bedrijfs toepassing.

## <a name="next-steps"></a>Volgende stappen
Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Managing access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md) (Toegang tot resources beheren met Azure Active Directory-groepen)
* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](groups-settings-cmdlets.md)
* [What is Azure Active Directory? (Engelstalig)](../fundamentals/active-directory-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md)
