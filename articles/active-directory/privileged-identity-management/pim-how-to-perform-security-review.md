---
title: Toegang tot Azure AD-rollen controleren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het controleren van de toegang tot Azure Active Directory-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847094"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Toegang tot Azure AD-rollen controleren in Privileged Identity Management

Privileged Identity Management (PIM) vereenvoudigt de manier waarop ondernemingen bevoorrechte toegang tot bronnen in Azure Active Directory (AD) en andere Online Services van Microsoft, zoals Office 365 of Microsoft Intune, beheren. Volg de stappen in dit artikel om uw toegewezen rollen zelf te bekijken.

Als u bent toegewezen aan een beheerrol, kan de bevoegde rolbeheerder van uw organisatie u vragen om regelmatig te bevestigen dat u die rol nog steeds nodig hebt voor uw functie. U een e-mail ontvangen met een koppeling, of u rechtstreeks naar de [Azure-portal](https://portal.azure.com) gaan en beginnen.

Als u een bevoorrechte rolbeheerder of globale beheerder bent die geïnteresseerd is in toegangsbeoordelingen, vindt u meer informatie bij [Hoe u een toegangscontrole starten.](pim-how-to-start-security-review.md)

## <a name="add-a-pim-dashboard-tile"></a>Een PIM-dashboardtegel toevoegen

Als u de Azure AD Privileged Identity Management-service niet vastgemaakt aan uw dashboard in uw Azure-portal hebt, voert u de volgende stappen uit om aan de slag te gaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer uw gebruikersnaam in de rechterbovenhoek van de Azure-portal en selecteer de map waar u actief bent.
3. Selecteer **Alle services** en gebruik het tekstvak Filteren om te zoeken naar **Azure AD Privileged Identity Management**.
4. Schakel **Vastmaken aan dashboard** in en klik op de knop **Maken**. De Privileged Identity Management-toepassing wordt geopend.

## <a name="approve-or-deny-access"></a>Toegang goedkeuren of weigeren

Wanneer u de toegang goedkeurt of weigert, vertelt u de revisor alleen of u deze rol nog steeds gebruikt of niet. Kies **Goedkeuren** als u in de rol wilt blijven of **Weigeren** als u de toegang niet meer nodig hebt. Uw status wordt niet meteen gewijzigd, totdat de controleur de resultaten toepast.
Volg de volgende stappen om de toegangscontrole te zoeken en te voltooien:

1. Selecteer in de service Privileged Identity Management de optie **Bevoegde toegang controleren**. Als u beoordelingen in behandeling hebt, worden deze weergegeven op de pagina Azure AD **Access-recensies.**
2. Selecteer de beoordeling die u wilt voltooien.
3. Tenzij u de beoordeling hebt gemaakt, wordt u weergegeven als de enige gebruiker in de beoordeling. Selecteer het vinkje naast uw naam.
4. Kies **Goedkeuren** of **Weigeren**. Mogelijk moet u een reden voor uw beslissing opnemen in het tekstvak **Een reden geven.**  
5. Sluit het **hoofdblad azure AD-rollen bekijken.**

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen

- [Perform an access review of my Azure AD resource roles in PIM](pim-resource-roles-perform-access-review.md) (Een toegangsbeoordeling uitvoeren van mijn Azure AD-resourcerollen in PIM)
