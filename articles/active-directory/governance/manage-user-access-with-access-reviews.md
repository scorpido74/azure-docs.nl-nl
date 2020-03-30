---
title: Gebruikerstoegang beheren met toegangsbeoordelingen - Azure AD
description: Informatie over hoe u Azure Active Directory-toegangsbeoordelingen gebruikt om gebruikerstoegang te beheren als lidmaatschap van een groep of toewijzing aan een toepassing
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
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c307b349144974a4d38f937feeebb98f369d047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932400"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gebruikerstoegang beheren met Azure AD-toegangsbeoordelingen

Met Azure Active Directory (Azure AD) kunt u eenvoudig zorgen dat gebruikers de juiste toegang hebben. U kunt gebruikers zelf of een besluitvormer vragen deel te nemen aan een toegangsbeoordeling om de toegang van gebruikers te bevestigen. De beoordelaars kunnen de noodzaak van een gebruiker voor onafgebroken toegang becommentariëren op basis van suggesties van Azure AD. Wanneer een toegangsbeoordeling is voltooid, kunt u wijzigingen aanbrengen en de toegang verwijderen voor gebruikers die deze niet meer nodig hebben.

> [!NOTE]
> Als u alleen de toegang van gastgebruikers wilt beoordelen, en niet van alle typen gebruikers, raadpleeg dan [Manage guest user access with access reviews](manage-guest-access-with-access-reviews.md) (De toegang van gastgebruikers beheren met toegangsbeoordelingen). Als u het lidmaatschap van een gebruiker in een beheerderrol, zoals Globale beheerder, wilt beoordelen, raadpleeg dan [Start an access review in Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md) (Een toegangsbeoordeling starten in Azure AD Privileged Identity Management).

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2

Zie [Licentievereisten voor](access-reviews-overview.md#license-requirements)meer informatie.

## <a name="create-and-perform-an-access-review"></a>Een toegangscontrole uitvoeren

Een toegangsbeoordeling kan een of meer gebruikers als beoordelaar hebben.  

1. Selecteer een groep in Azure AD met een of meer leden. Of selecteer een toepassing die is verbonden met Azure AD en waaraan een of meer gebruikers zijn toegewezen. 

2. Bepaal of elke gebruikersbeoordeling een eigen toegang moet hebben, of dat een of meer gebruikers de toegang van iedereen kunnen beoordelen.

3. Ga als globale beheerder of gebruikersbeheerder naar de [pagina Identiteitsbeheer](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Maak de toegangscontrole. Zie [Een toegangscontrole van groepen of toepassingen maken voor](create-access-review.md)meer informatie.

5. Wanneer de toegangsbeoordeling wordt gestart, vraagt u de revisoren om invoer te geven. Standaard ontvangen ze elk een e-mail van Azure AD met een koppeling naar het toegangspaneel, waar ze [de toegang tot groepen of toepassingen controleren.](perform-access-review.md)

6. Als de beoordelaars geen feedback hebben gegeven, kunt u Azure AD vragen hen een herinnering te sturen. Standaard ontvangen de beoordelaars die nog niet hebben gereageerd halverwege de einddatum automatisch een herinnering van Azure AD.

7. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [Een toegangscontrole van groepen of toepassingen voltooien](complete-access-review.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

[Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)




