---
title: Toegang verlenen voor het beheren van PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het verlenen van toegang aan andere instanties om Azure AD Privileged Identity Management (PIM) te beheren.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022116"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Toegang verlenen aan andere beheerders om Privileged Identity Management te beheren

De globale beheerder die Privileged Identity Management (PIM) inschakelt voor een organisatie, kan automatisch roltoewijzingen ophalen en toegang krijgen tot Privileged Identity Management. Niemand anders in uw Azure Active Directory-organisatie (Azure AD) krijgt standaard schrijf toegang, maar ook met andere globale beheerders. Andere globale beheerders, beveiligings beheerders en beveiligings lezers hebben alleen-lezen toegang tot Privileged Identity Management. Als u toegang wilt verlenen aan Privileged Identity Management, kan de eerste gebruiker andere gebruikers toewijzen aan de rol van **beheerdersrol voor bevoegde rollen** .

> [!NOTE]
> Voor het beheren van Privileged Identity Management is Azure Multi-Factor Authentication vereist. Aangezien micro soft-accounts zich niet kunnen registreren voor Azure Multi-Factor Authentication, kan een gebruiker die zich aanmeldt met een Microsoft-account geen toegang krijgen tot Privileged Identity Management.

Zorg ervoor dat er altijd ten minste twee gebruikers in een rol voor geprivilegieerde rol beschikken, voor het geval één gebruiker is vergrendeld of het account wordt verwijderd.

## <a name="grant-access-to-manage-pim"></a>Toegang verlenen voor het beheren van PIM

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Open **privileged Identity Management**in azure AD.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **rollen**.

    ![Privileged Identity Management Azure AD-rollen-rollen](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecteer de beheerdersrol **privileged Role** om de pagina leden te openen.

    ![Beheerder van geprivilegieerde rol-leden](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selecteer **lid toevoegen** om het deel venster beheerde leden toevoegen te openen.

1. Selecteer **leden selecteren** om het deel venster leden selecteren te openen.

    ![Beheerder van geprivilegieerde rol-leden selecteren](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecteer een lid en klik vervolgens op **selecteren**.

1. Selecteer **OK** om het lid in aanmerking te laten komen voor de beheerdersrol van de **beveiligingsrol** .

    Wanneer u een nieuwe rol toewijst aan iemand in Privileged Identity Management, worden deze automatisch geconfigureerd als **in aanmerking komend** om de rol te activeren.

1. Als u het lid permanent wilt maken, selecteert u de gebruiker in de lijst Administrator leden van geprivilegieerde rol.

1. Selecteer **meer** en vervolgens **permanent maken** om de toewijzing permanent te maken.

    ![Beheerder van geprivilegieerde rol: permanent maken](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Stuur de gebruiker een koppeling om te [beginnen met privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Toegang tot het beheer van PIM verwijderen

Zorg er altijd voor dat er nog mini maal twee gebruikers aan zijn toegewezen voordat u iemand verwijdert uit de beheerderrol.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **rollen**.

1. Selecteer de beheerdersrol **privileged Role** om de pagina leden te openen.

1. Schakel het selectie vakje in naast de gebruiker die u wilt verwijderen en selecteer **lid verwijderen**.

    ![Beheerder van geprivilegieerde rol-lid verwijderen](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Als u wordt gevraagd om te bevestigen dat u het lid uit de rol wilt verwijderen, selecteert u **Ja**.

## <a name="next-steps"></a>Volgende stappen

- [Beginnen met het gebruik van Privileged Identity Management](pim-getting-started.md)
