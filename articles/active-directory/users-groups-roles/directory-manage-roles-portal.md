---
title: Machtigingen voor beheerdersrol weer geven en toewijzen-Azure AD | Microsoft Docs
description: U kunt nu leden van een Azure AD-beheerdersrol weer geven en beheren in de portal. Voor degenen die regel matig roltoewijzingen beheren.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fea05ada52ba4281acb3451769446d7f0bad790d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448691"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Beheerders rollen in Azure Active Directory weer geven en toewijzen

U kunt nu alle leden van de beheerders rollen weer geven en beheren in de Azure Active Directory Portal. Als u regel matig roltoewijzingen beheert, hebt u waarschijnlijk de voor keur. En als u ooit hebt gevraagd wat de bedoeling is dat deze rollen echt doen, kunt u een gedetailleerde lijst met machtigingen voor elk van de Azure AD-beheerders rollen weer geven.

## <a name="view-all-roles"></a>Alle rollen weer geven

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **Azure Active Directory**.

1. Selecteer **rollen en beheerders** om de lijst met alle beschik bare rollen weer te geven.

1. Selecteer het beletsel teken rechts van elke rij om de machtigingen voor de rol weer te geven. Selecteer een rol om de gebruikers weer te geven die aan de rol zijn toegewezen. Als u iets anders ziet in de volgende afbeelding, leest u de opmerking in [toewijzingen weer geven voor geprivilegieerde rollen](#view-assignments-for-privileged-roles) om te controleren of u in PRIVILEGED Identity Management (PIM) bent.

    ![lijst met rollen in de Azure AD-Portal](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Mijn rollen weer geven

U kunt ook eenvoudig uw eigen machtigingen weer geven. Selecteer **uw rol** op de pagina **rollen en beheerders** om de rollen weer te geven die momenteel aan u zijn toegewezen.

## <a name="view-assignments-for-privileged-roles"></a>Toewijzingen voor geprivilegieerde rollen weer geven

U kunt **beheren in PIM** selecteren voor extra beheer mogelijkheden. Beheerders van geprivilegieerde rollen kunnen de toewijzingen ' permanent ' (altijd actief in de rol) wijzigen in in aanmerking komende (in de rol alleen bij verhoogde bevoegdheid). Als u niet beschikt over Privileged Identity Management, kunt u nog steeds **beheren in PIM** selecteren om u aan te melden voor een proef versie. Voor Privileged Identity Management is een [Azure AD Premium P2-licentie plan](../privileged-identity-management/subscription-requirements.md)vereist.

![lijst met leden van een beheerdersrol](./media/directory-manage-roles-portal/member-list.png)

Als u een globale beheerder of een beheerder van een bevoegde rol bent, kunt u eenvoudig leden toevoegen of verwijderen, de lijst filteren of een lid selecteren om hun actieve toegewezen rollen te zien.

> [!Note]
> Als u een Azure AD Premium P2-licentie hebt en u Privileged Identity Management al gebruikt, worden alle beheer taken voor rollen uitgevoerd in bevoegdheden voor identiteits beheer en niet in azure AD.
>
> ![Azure AD-rollen die worden beheerd in PIM voor gebruikers die PIM al gebruiken en een Premium P2-licentie hebben](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>De rolmachtigingen van een gebruiker weer geven

Wanneer u de leden van een rol bekijkt, selecteert u **Beschrijving** om de volledige lijst met machtigingen te zien die zijn verleend door de roltoewijzing. De pagina bevat koppelingen naar relevante documentatie om u te helpen bij het beheren van Directory rollen.

![lijst met machtigingen voor een beheerdersrol](./media/directory-manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Roltoewijzingen downloaden

Als u alle toewijzingen voor een specifieke functie wilt downloaden, selecteert u op de pagina **rollen en beheerders** een rol en selecteert u vervolgens roltoewijzingen **downloaden**. Een CSV-bestand met een lijst met toewijzingen in alle bereiken voor die rol wordt gedownload.

![alle toewijzingen voor een rol downloaden](./media/directory-manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Een rol toewijzen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met de beheerders machtigingen globale beheerder of privileged Role en selecteer **Azure Active Directory**.

1. Selecteer **rollen en beheerders** om de lijst met alle beschik bare rollen weer te geven.

1. Selecteer een rol om de bijbehorende toewijzingen te bekijken.

    ![lijst met machtigingen voor een beheerdersrol](./media/directory-manage-roles-portal/member-list.png)

1. Selecteer **toewijzingen toevoegen** en selecteer de functies die u wilt toewijzen. U kunt **beheren in PIM** selecteren voor extra beheer mogelijkheden. Als u iets anders ziet in de volgende afbeelding, leest u de opmerking in [toewijzingen weer geven voor geprivilegieerde rollen](#view-assignments-for-privileged-roles) om te controleren of u zich in PIM bevindt.

    ![lijst met machtigingen voor een beheerdersrol](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Volgende stappen

* U kunt graag delen met ons op het [forum van Azure AD-beheerders](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [beheerders rollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over functies en de toewijzing van beheerdersrol.
* Zie voor standaard gebruikers machtigingen een [vergelijking van de standaard machtigingen voor gast-en gebruikers rechten](../fundamentals/users-default-permissions.md).
