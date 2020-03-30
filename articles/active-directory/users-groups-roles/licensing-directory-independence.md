---
title: Kenmerken van meerdere tenantinteractie - Azure AD | Microsoft Documenten
description: Uw Azure Active-tenanttenants beheren door uw tenants te begrijpen als volledig onafhankelijke bronnen
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562255"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Begrijpen hoe meerdere Azure Active Directory-tenants samenwerken

In Azure Active Directory (Azure AD) is elke tenant een volledig onafhankelijke bron: een peer die logischonafhankelijk is van de andere tenants die u beheert. Er is geen ouder-kind relatie tussen huurders. Deze onafhankelijkheid tussen huurders omvat resource onafhankelijkheid, administratieve onafhankelijkheid, en synchronisatie onafhankelijkheid.

## <a name="resource-independence"></a>Onafhankelijkheid van hulpbronnen
* Als u een resource in één tenant maakt of verwijdert, heeft dit geen invloed op een resource in een andere tenant, met uitzondering van externe gebruikers. 
* Als u een van uw domeinnamen met één tenant gebruikt, kan deze niet met een andere tenant worden gebruikt.

## <a name="administrative-independence"></a>Bestuurlijke onafhankelijkheid
Als een niet-administratieve gebruiker van tenant 'Contoso' een testtenant 'Test' maakt, gaat het dan als:

* Standaard wordt de gebruiker die een tenant maakt, als externe gebruiker in die nieuwe tenant toegevoegd en toegewezen aan de globale beheerdersrol in die tenant.
* De beheerders van tenant 'Contoso' hebben geen directe beheerdersbevoegdheden voor tenant 'Test', tenzij een beheerder van 'Test' hen specifiek deze bevoegdheden verleent. Beheerders van 'Contoso' kunnen echter de toegang tot tenant 'Test' beheren als ze het gebruikersaccount beheren dat 'Test' heeft gemaakt.
* Als u een beheerdersrol voor een gebruiker in één tenant toevoegt/verwijdert, heeft de wijziging geen invloed op de beheerdersrollen die de gebruiker in een andere tenant heeft.

## <a name="synchronization-independence"></a>Synchronisatie onafhankelijkheid
U elke Azure AD-tenant onafhankelijk configureren om gegevens te laten synchroniseren vanuit één exemplaar van:

* Het azure AD Connect-gereedschap om gegevens te synchroniseren met één AD-forest.
* De Azure Active-tenantconnector voor Forefront Identity Manager, om gegevens te synchroniseren met een of meer on-premises forests en/of niet-Azure AD-gegevensbronnen.

## <a name="add-an-azure-ad-tenant"></a>Een Azure AD-tenant toevoegen
Als u een Azure AD-tenant wilt toevoegen aan de Azure-portal, meldt u zich aan bij [de Azure-portal](https://portal.azure.com) met een account dat een globale beheerder van Azure AD is en selecteert u links **Nieuw**.

> [!NOTE]
> In tegenstelling tot andere Azure-bronnen zijn uw tenants geen onderliggende bronnen van een Azure-abonnement. Als uw Azure-abonnement is geannuleerd of verlopen, hebt u nog steeds toegang tot uw tenantgegevens via Azure PowerShell, de Microsoft Graph API of het Microsoft 365-beheercentrum. U ook [een ander abonnement koppelen aan de tenant.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Volgende stappen
Zie [Wat is Azure Active-tenantlicenties voor](../fundamentals/active-directory-licensing-whatis-azure-portal.md)een breed overzicht van Azure AD-licentieproblemen en aanbevolen procedures.
