---
title: Toegang tot Azure-resource rollen controleren in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het controleren van de toegang tot Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c0ce1a5eee4b8d4cc40c47dadd4bcdc74d03ba
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804100"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Toegang tot Azure-resource rollen controleren in PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vereenvoudigt de manier waarop bedrijven bevoorrechte toegang tot resources in azure beheren. 

Als u bent toegewezen aan een administratieve rol, vraagt de beheerder van de bevoegde rol van uw organisatie u mogelijk regel matig te bevestigen dat u deze rol nog nodig hebt voor uw taak. U kunt ook een e-mail bericht met een koppeling ontvangen of direct naar de [Azure Portal](https://portal.azure.com)gaan. Volg de stappen in dit artikel om uw toegewezen rollen zelf te controleren.

Als u een bevoegde rol beheerder bent die geïnteresseerd is in toegangs beoordelingen, kunt u meer informatie vinden [over het starten van een toegangs beoordeling](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>De Privileged Identity Management-toepassing toevoegen
U kunt de Azure Active Directory (Azure AD) PIM-toepassing gebruiken in de [Azure Portal](https://portal.azure.com/) om uw beoordeling uit te voeren. Als u de toepassing niet in uw portal hebt, volgt u deze stappen om aan de slag te gaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer uw gebruikers naam in de rechter bovenhoek van de Azure Portal en selecteer de map waarin u wilt werken.
3. Selecteer **alle services**en gebruik het vak **filteren** om *Azure AD privileged Identity Management*te zoeken.
4. Controleer de **pincode voor het dash board**en selecteer vervolgens **maken**. De PIM-toepassing wordt geopend.

## <a name="approve-or-deny-access"></a>Toegang goed keuren of weigeren
Wanneer u toegang goedkeurt of weigert, vertelt u de revisor of u deze functie nog gebruikt of niet. Kies **goed keuren** als u de rol wilt blijven gebruiken of **weigeren** als u de toegang niet meer nodig hebt. De status verandert alleen wanneer de revisor de resultaten toepast.

Volg deze stappen om de toegangs beoordeling te vinden en te volt ooien:
1. Blader naar de Azure AD PIM-toepassing.
2. Selecteer de Blade **toegang controleren** .

   ![Scherm afbeelding van PIM-toepassing, met de Blade toegang controleren geselecteerd](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Selecteer de beoordeling die u wilt volt ooien. 
4. Kies **goed keuren** of **weigeren**. In het **vak een reden opgeven**moet u mogelijk een reden voor uw beslissing toevoegen.

   ![Scherm afbeelding van de pagina met details van de beoordeling](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling van mijn Azure AD-rollen uitvoeren in PIM](pim-how-to-perform-security-review.md)
