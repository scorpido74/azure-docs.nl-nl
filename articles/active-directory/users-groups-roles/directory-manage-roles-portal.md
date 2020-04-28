---
title: Machtigingen voor beheerdersrol weer geven en toewijzen-Azure AD | Microsoft Docs
description: U kunt nu leden van een Azure AD-beheerdersrol weer geven en beheren in de portal. Voor degenen die regel matig roltoewijzingen beheren.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900916"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Beheerders rollen in Azure Active Directory weer geven en toewijzen

U kunt nu alle leden van de beheerders rollen weer geven en beheren in de Azure Active Directory Portal. Als u regel matig roltoewijzingen beheert, hebt u waarschijnlijk de voor keur. En als u ooit hebt gevraagd wat de bedoeling is dat deze rollen echt doen, kunt u een gedetailleerde lijst met machtigingen voor elk van de Azure AD-beheerders rollen weer geven.

## <a name="view-all-roles"></a>Alle rollen weer geven

Zoek naar **Azure Active Directory** en selecteer deze optie. Selecteer **rollen en beheerders** om de lijst met alle beschik bare rollen weer te geven.

Klik op het weglatings teken rechts van elke rij om de gedetailleerde beschrijving van de functie te openen.

![lijst met rollen in de Azure AD-Portal](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Mijn rollen weer geven

U kunt ook eenvoudig uw eigen machtigingen weer geven. Selecteer **uw rol** op de pagina **rollen en beheerders** om de rollen weer te geven die momenteel aan u zijn toegewezen.

## <a name="view-assignments-for-a-role"></a>Toewijzingen voor een rol weer geven

Klik op een rol om de gebruikers weer te geven die aan de rol zijn toegewezen. U kunt **beheren in PIM** selecteren voor extra beheer mogelijkheden. Beheerders van geprivilegieerde rollen kunnen de toewijzingen ' permanent ' (altijd actief in de rol) wijzigen in in aanmerking komende (in de rol alleen bij verhoogde bevoegdheid). Als u geen PIM hebt, kunt u nog steeds **beheren in PIM** selecteren om u aan te melden voor een proef versie. Voor Privileged Identity Management is een [Azure AD Premium P2-licentie plan](../privileged-identity-management/subscription-requirements.md)vereist.

![lijst met leden van een beheerdersrol](./media/directory-manage-roles-portal/member-list.png)

Als u een globale beheerder of een beheerder van een bevoegde rol bent, kunt u eenvoudig leden toevoegen of verwijderen, de lijst filteren of een lid selecteren om hun actieve toegewezen rollen te zien.

## <a name="view-a-users-role-permissions"></a>De rolmachtigingen van een gebruiker weer geven

Wanneer u de leden van een rol bekijkt, selecteert u **Beschrijving** om de volledige lijst met machtigingen te zien die zijn verleend door de roltoewijzing. De pagina bevat koppelingen naar relevante documentatie om u te helpen bij het beheren van Directory rollen.

![lijst met machtigingen voor een beheerdersrol](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Volgende stappen

* U kunt graag delen met ons op het [forum van Azure AD-beheerders](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [beheerders rollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over functies en de toewijzing van beheerdersrol.
* Zie voor standaard gebruikers machtigingen een [vergelijking van de standaard machtigingen voor gast-en gebruikers rechten](../fundamentals/users-default-permissions.md).
