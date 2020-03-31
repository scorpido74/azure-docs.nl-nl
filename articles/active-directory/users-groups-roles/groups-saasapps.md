---
title: Een groep gebruiken om de toegang tot SaaS-apps te beheren - Azure AD | Microsoft Documenten
description: Groepen in Azure Active Directory gebruiken om toegang toe te wijzen tot SaaS-toepassingen die zijn geïntegreerd met Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026856"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Een groep gebruiken om SaaS-toepassingen te beheren

Met Azure Active Directory (Azure AD) met een Azure AD Premium-licentieplan u groepen gebruiken om toegang toe te wijzen tot een SaaS-toepassing die is geïntegreerd met Azure AD. Als u bijvoorbeeld toegang wilt toewijzen aan de marketingafdeling om vijf verschillende SaaS-toepassingen te gebruiken, u een groep maken die de gebruikers op de marketingafdeling bevat en deze groep vervolgens toewijzen aan deze vijf SaaS-toepassingen die nodig zijn door de marketingafdeling. Op deze manier u tijd besparen door het beheer van het lidmaatschap van de marketing afdeling op een plaats. Gebruikers worden vervolgens toegewezen aan de toepassing wanneer ze worden toegevoegd als leden van de marketinggroep en laten hun toewijzingen uit de toepassing verwijderen wanneer ze uit de marketinggroep worden verwijderd. Deze mogelijkheid kan worden gebruikt met honderden toepassingen die u toevoegen vanuit de Azure AD Application Gallery.

> [!IMPORTANT]
> U deze functie alleen gebruiken nadat u een proefversie van Azure AD Premium hebt gestart of een Azure AD Premium-licentieplan hebt gekocht.
> Groepstoewijzing wordt alleen ondersteund voor beveiligingsgroepen.
> Op dit moment wordt genest groepslidmaatschap niet ondersteund voor toewijzing aan een toepassing op basis van een groep.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Toegang voor een gebruiker of groep toewijzen aan een SaaS-toepassing

1. Selecteer **Enterprise-toepassingen**in het [Azure AD-beheercentrum](https://aad.portal.azure.com).
2. Selecteer een toepassing die u hebt toegevoegd in de toepassingsgalerie om deze te openen.
3. Selecteer **Gebruikers en groepen**en selecteer Vervolgens Gebruiker **toevoegen**.
4. Selecteer **Op Toewijzing toevoegen**de optie Gebruikers en **groepen** om de selectielijst Gebruikers **en groepen** te openen.
6. Selecteer zoveel groepen of gebruikers als u wilt en klik of tik op **Selecteren** om ze toe te voegen aan de lijst **Toewijzing toevoegen.** U in dit stadium ook een rol aan een gebruiker toewijzen.
7. Selecteer **Toewijzen** om de gebruikers of groepen toe te wijzen aan de geselecteerde bedrijfstoepassing.

## <a name="next-steps"></a>Volgende stappen
Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Managing access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md) (Toegang tot resources beheren met Azure Active Directory-groepen)
* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](groups-settings-cmdlets.md)
* [Wat is Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md)
