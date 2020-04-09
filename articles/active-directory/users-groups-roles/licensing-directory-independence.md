---
title: Kenmerken van meerdere tenantinteractie - Azure AD | Microsoft Documenten
description: Uw Azure Active Directory-tenants begrijpen als volledig onafhankelijke organisaties
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878116"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Begrijpen hoe meerdere Azure Active Directory-organisaties met elkaar omgaan

In Azure Active Directory (Azure AD) is elke tenant een volledig onafhankelijke organisatie: een peer die logischonafhankelijk is van de andere Azure AD-organisaties die u beheert. Deze onafhankelijkheid tussen organisaties omvat resource onafhankelijkheid, administratieve onafhankelijkheid, en synchronisatie onafhankelijkheid. Er is geen ouder-kind relatie tussen organisaties.

## <a name="resource-independence"></a>Onafhankelijkheid van hulpbronnen

* Als u een Azure AD-bron in één organisatie maakt of verwijdert, heeft dit geen invloed op een resource in een andere organisatie, met uitzondering van externe gebruikers.
* Als u een van uw domeinnamen registreert bij één organisatie, kan deze niet door een andere organisatie worden gebruikt.

## <a name="administrative-independence"></a>Bestuurlijke onafhankelijkheid

Als een niet-administratieve gebruiker van organisatie 'Contoso' een testorganisatie 'Test' maakt, dan:

* De gebruiker die een organisatie maakt, wordt standaard toegevoegd als externe gebruiker in die nieuwe organisatie en toegewezen aan de globale beheerdersrol in die organisatie.
* De beheerders van organisatie 'Contoso' hebben geen directe beheerdersbevoegdheden aan organisatie 'Test', tenzij een beheerder van 'Test' hen specifiek deze bevoegdheden verleent. Beheerders van 'Contoso' kunnen echter de toegang tot organisatie 'Test' beheren als ze het gebruikersaccount beheren dat 'Test' heeft gemaakt.
* Als u een Azure AD-rol toevoegt of verwijdert voor een gebruiker in één organisatie, heeft de wijziging geen invloed op de rollen die de gebruiker in een andere Azure AD-organisatie is toegewezen.

## <a name="synchronization-independence"></a>Synchronisatie onafhankelijkheid

U elke Azure AD-organisatie onafhankelijk configureren om gegevens te laten synchroniseren vanuit één exemplaar van:

* Het azure AD Connect-gereedschap om gegevens te synchroniseren met één AD-forest.
* De Azure Active Directory Connector voor Forefront Identity Manager, om gegevens te synchroniseren met een of meer on-premises forests en/of niet-Azure AD-gegevensbronnen.

## <a name="add-an-azure-ad-organization"></a>Een Azure AD-organisatie toevoegen

Als u een Azure AD-organisatie wilt toevoegen aan de Azure-portal, meldt u zich aan bij [de Azure-portal](https://portal.azure.com) met een account dat een globale azure-beheerder van Azure is en selecteert u **Nieuw**.

> [!NOTE]
> In tegenstelling tot andere Azure-bronnen zijn uw Azure AD-organisaties geen onderliggende resources van een Azure-abonnement. Als uw Azure-abonnement is geannuleerd of verlopen, hebt u nog steeds toegang tot de gegevens van uw Azure AD-organisatie met Azure PowerShell, de Microsoft Graph API of het Microsoft 365-beheercentrum. U ook [een ander abonnement koppelen aan de organisatie.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Volgende stappen

Zie Wat [is Azure Active Directory-licentieverlening voor](../fundamentals/active-directory-licensing-whatis-azure-portal.md)Azure AD-licentieoverwegingen en aanbevolen procedures.
