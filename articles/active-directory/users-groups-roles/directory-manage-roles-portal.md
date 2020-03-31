---
title: Beheerdersrolmachtigingen weergeven en toewijzen - Azure AD | Microsoft Documenten
description: U nu leden van een Azure AD-beheerdersrol in de portal zien en beheren. Voor degenen die vaak rolopdrachten beheren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900916"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Beheerdersrollen weergeven en toewijzen in Azure Active Directory

U nu alle leden van de beheerdersrollen in de Azure Active Directory-portal bekijken en beheren. Als u vaak rolopdrachten beheert, zult u deze ervaring waarschijnlijk verkiezen. En als je je ooit hebt afgevraagd "Wat doen deze rollen echt?", zie je een gedetailleerde lijst met machtigingen voor elk van de Azure AD-beheerdersrollen.

## <a name="view-all-roles"></a>Alle rollen weergeven

Zoek naar **Azure Active Directory** en selecteer deze optie. Selecteer **Rollen en beheerders** om de lijst met alle beschikbare rollen te bekijken.

Klik op de ellips aan de rechterkant van elke rij om de gedetailleerde beschrijving van de rol te openen.

![lijst met rollen in Azure AD-portal](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Mijn rollen bekijken

Het is ook gemakkelijk om uw eigen machtigingen te bekijken. Selecteer **Uw rol** op de pagina Rollen en **beheerders** om de rollen te zien die momenteel aan u zijn toegewezen.

## <a name="view-assignments-for-a-role"></a>Opdrachten voor een rol weergeven

Klik op een rol om de gebruikers weer te geven die aan de rol zijn toegewezen. U **Beheren in PIM** selecteren voor extra beheermogelijkheden. Beheerders van bevoorrechte rollen kunnen toewijzingen 'Permanent' (altijd actief in de rol) wijzigen in 'In aanmerking komend' (in de rol alleen wanneer deze worden verhoogd). Als u geen PIM hebt, u nog steeds **Beheren in PIM** selecteren om u aan te melden voor een proefversie. Voor privileged Identity Management is een [Azure AD Premium P2-licentieplan vereist.](../privileged-identity-management/subscription-requirements.md)

![lijst met leden van een beheerdersrol](./media/directory-manage-roles-portal/member-list.png)

Als u een globale beheerder of een beheerder van een bevoorrechte rol bent, u eenvoudig leden toevoegen of verwijderen, de lijst filteren of een lid selecteren om hun actieve toegewezen rollen te zien.

## <a name="view-a-users-role-permissions"></a>De rolmachtigingen van een gebruiker weergeven

Wanneer u de leden van een rol bekijkt, selecteert u **Beschrijving** om de volledige lijst met machtigingen te bekijken die door de roltoewijzing zijn verleend. De pagina bevat koppelingen naar relevante documentatie om u te helpen bij het beheren van directoryrollen.

![lijst met machtigingen voor een beheerdersrol](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Volgende stappen

* Voel je vrij om met ons te delen op het [Azure AD-forum voor beheerfuncties](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [Beheerdersrollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over rollen en toewijzing van beheerders.
* Zie een vergelijking van [standaardmachtigingen voor gasten en leden](../fundamentals/users-default-permissions.md)voor standaardgebruikersmachtigingen voor standaardgebruikers.
