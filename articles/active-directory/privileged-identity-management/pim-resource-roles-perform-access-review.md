---
title: Toegang tot Azure-bronrollen controleren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het controleren van de toegang tot Azure-bronrollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847003"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Toegang tot Azure-bronrollen controleren in Privileged Identity Management

Pim-toegangsbeoordelingen (Privileged Identity Management) kunnen helpen bij het beveiligen van toegang tot bevoorrechte rollen in Azure Active Directory (Azure AD). In dit artikel worden de stappen uitgevoerd om een beoordeling van uw bevoorrechte roltoewijzingen in een Azure AD-toegangscontrole uit te voeren.

Als u bent toegewezen aan een beheerrol, moet u mogelijk een toegangscontrole door uw beheerder voltooien om te bevestigen dat u een rol nodig hebt. Het bevestigingsverzoek kan een e-mail komen met een koppeling, of u dit bevestigen in de [Azure-portal.](https://portal.azure.com)

Als u een bevoorrechte rolbeheerder bent die geïnteresseerd is in toegangsbeoordelingen, krijgt u meer informatie bij [Hoe u een toegangscontrole starten.](pim-resource-roles-start-access-review.md)

## <a name="approve-or-deny-access"></a>Toegang goedkeuren of weigeren

U toegang goedkeuren of weigeren op basis van de vraag of u deze rol nog steeds gebruikt of niet. Kies **Goedkeuren** als u in de rol wilt blijven of **Weigeren** als u de toegang niet meer nodig hebt. Uw status wordt pas gewijzigd nadat de revisor de resultaten heeft gebruikt.

Volg de volgende stappen om de toegangscontrole te zoeken en te voltooien:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer **Azure Active Directory** en open Privileged Identity **Management**.
1. Selecteer **Toegang controleren**.

   ![Schermafbeelding van de toepassing Privileged Identity Management, waarbij toegangsblad controleren is geselecteerd](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selecteer de beoordeling die u wilt voltooien.
1. Kies **Goedkeuren** of **Weigeren**. Voer in het **vak Een reden geven**indien nodig een zakelijke rechtvaardiging in voor uw beslissing.

   ![Schermafbeelding van pagina Details controleren](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Volgende stappen

- [Een toegangscontrole uitvoeren van mijn Azure AD-rollen in Privileged Identity Management](pim-how-to-perform-security-review.md)
