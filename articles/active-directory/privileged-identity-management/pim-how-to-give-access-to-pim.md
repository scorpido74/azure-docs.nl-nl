---
title: Toegang verlenen aan andere beheerders voor het beheren van PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het verlenen van toegang aan andere instanties om Azure AD Privileged Identity Management (PIM) te beheren.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3a0173108b6c884994ca25fd0495e9cb8d45186
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804362"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Toegang verlenen aan andere beheerders om PIM te beheren

De globale beheerder die Azure Active Directory (Azure AD) Privileged Identity Management (PIM) voor een organisatie inschakelt, kan automatisch Roltoewijzingen en toegang krijgen tot PIM. Niemand anders krijgt standaard schrijf toegang, maar ook met andere globale beheerders. Andere globale beheerders, beveiligings beheerders en beveiligings lezers hebben alleen-lezen toegang tot PIM. Als u toegang wilt verlenen tot PIM, kan de eerste gebruiker andere gebruikers toewijzen aan de rol van **beheerdersrol voor bevoegde rol** .

> [!NOTE]
> Voor het beheren van PIM is Azure MFA vereist. Aangezien micro soft-accounts niet kunnen worden geregistreerd voor Azure MFA, kan een gebruiker die zich aanmeldt met een Microsoft-account geen toegang krijgen tot PIM.

Zorg ervoor dat er altijd ten minste twee gebruikers in een rol voor geprivilegieerde rol beschikken, voor het geval één gebruiker is vergrendeld of het account wordt verwijderd.

## <a name="grant-access-to-manage-pim"></a>Toegang verlenen voor het beheren van PIM

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

1. Klik op **rollen**.

    ![PIM Azure AD-rollen-rollen](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Klik op de beheerdersrol **privileged Role** om de pagina leden te openen.

    ![Beheerder van geprivilegieerde rol-leden](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Klik op **lid toevoegen** om het deel venster beheerde leden toevoegen te openen.

1. Klik op **leden selecteren** om het deel venster leden selecteren te openen.

    ![Beheerder van geprivilegieerde rol-leden selecteren](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecteer een lid en klik vervolgens op **selecteren**.

1. Klik op **OK** om het lid in aanmerking te laten komen voor de rol van **Administrator voor geprivilegieerde functies** .

    Wanneer u een nieuwe rol toewijst aan iemand in PIM, worden deze automatisch geconfigureerd als in **aanmerking komend** om de rol te activeren.

1. Als u het lid permanent wilt maken, klikt u op de gebruiker in de lijst Administrator leden van geprivilegieerde rol.

1. Klik op **meer** en vervolgens op **permanent maken** om de toewijzing permanent te maken.

    ![Beheerder van geprivilegieerde rol: permanent maken](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Stuur de gebruiker een koppeling om te [beginnen met behulp van Pim](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Toegang tot het beheer van PIM verwijderen

Zorg er altijd voor dat er nog mini maal twee gebruikers aan zijn toegewezen voordat u iemand verwijdert uit de beheerderrol.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

1. Klik op **rollen**.

1. Klik op de beheerdersrol **privileged Role** om de pagina leden te openen.

1. Voeg een vinkje toe naast de gebruiker die u wilt verwijderen en klik vervolgens op **lid verwijderen**.

    ![Beheerder van geprivilegieerde rol-lid verwijderen](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Klik op **Ja**in het bericht dat wordt weer gegeven waarin u wordt gevraagd of u het lid uit de rol wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Beginnen met PIM](pim-getting-started.md)
