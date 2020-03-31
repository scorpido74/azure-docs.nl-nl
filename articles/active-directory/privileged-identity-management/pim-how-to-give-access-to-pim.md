---
title: Toegang verlenen voor het beheren van PIM - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het verlenen van toegang tot andere beheersfuncties voor het beheren van Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022116"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Toegang verlenen aan andere beheerders om privileged identity management te beheren

De globale beheerder die Privileged Identity Management (PIM) voor een organisatie inschakelt, krijgt automatisch roltoewijzingen en toegang tot Privileged Identity Management. Niemand anders in uw Azure Active Directory-organisatie (Azure AD) krijgt standaard schrijftoegang, inclusief andere globale beheerders. Andere globale beheerders, beveiligingsbeheerders en beveiligingslezers hebben alleen-lezen toegang tot Privileged Identity Management. Als u toegang wilt verlenen tot Privileged Identity Management, kan de eerste gebruiker anderen toewijzen aan de rol **Van Privileged Role Administrator.**

> [!NOTE]
> Voor het beheren van privileged identity management is Azure Multi-Factor Authentication vereist. Aangezien Microsoft-accounts zich niet kunnen registreren voor Azure Multi-Factor Authentication, heeft een gebruiker die zich aanmeldt met een Microsoft-account geen toegang tot Privileged Identity Management.

Zorg ervoor dat er altijd ten minste twee gebruikers in een rol van privileged role administrator zitten, voor het geval één gebruiker is vergrendeld of hun account wordt verwijderd.

## <a name="grant-access-to-manage-pim"></a>Toegang verlenen om PIM te beheren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Privileged Identity Management**in Azure AD .

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Rollen**.

    ![Privileged Identity Management Azure AD-rollen - Rollen](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecteer de rol **Van Privileged Role Administrator** om de ledenpagina te openen.

    ![Beheerder van bevoorrechte rollen - Leden](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selecteer **Lid toevoegen** om het deelvenster Beheerde leden toevoegen te openen.

1. Selecteer **Leden selecteren** om het deelvenster Leden selecteren te openen.

    ![Beheerder van een bevoorrechte rol - Leden selecteren](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecteer een lid en klik op **Selecteren**.

1. Selecteer **OK** om het lid in aanmerking te laten komen voor de rol **Van Privileged Role Administrator.**

    Wanneer u een nieuwe rol toewijst aan iemand in Privileged Identity Management, worden deze persoon automatisch geconfigureerd als **In aanmerking komen** om de rol te activeren.

1. Als u het lid permanent wilt maken, selecteert u de gebruiker in de lijst met bevoegde rolbeheerders.

1. Selecteer **Meer** en **maak vervolgens permanent** om de opdracht permanent te maken.

    ![Beheerder van bevoorrechte rollen - Permanent maken](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Stuur de gebruiker een koppeling naar [Start met Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Toegang verwijderen om PIM te beheren

Voordat u iemand uit de rol Van privileged role administrator verwijdert, moet u er altijd voor zorgen dat er nog steeds ten minste twee gebruikers aan zijn toegewezen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Rollen**.

1. Selecteer de rol **Van Privileged Role Administrator** om de ledenpagina te openen.

1. Schakel het selectievakje in naast de gebruiker die u wilt verwijderen en selecteer **Lid verwijderen**.

    ![Beheerder van een bevoorrechte rol - Lid verwijderen](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Wanneer u wordt gevraagd te bevestigen dat u het lid uit de rol wilt verwijderen, selecteert u **Ja**.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Privileged Identity Management](pim-getting-started.md)
