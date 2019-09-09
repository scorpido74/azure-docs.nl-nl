---
title: Mijn Azure-resource rollen activeren in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het activeren van uw Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e2bf1253f1c2f53b839a73034fcdb33f3aab463
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804332"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Activeer mijn Azure-resource rollen in PIM

Door Azure Active Directory (Azure AD) Privileged Identity Management (PIM) te gebruiken, kunnen in aanmerking komende leden voor Azure-resources activering plannen voor een datum en tijd in de toekomst. Ze kunnen ook een specifieke activerings duur binnen het maximum (geconfigureerd door beheerders) selecteren.

Dit artikel is voor leden die hun Azure-resource functie moeten activeren in PIM.

## <a name="activate-a-role"></a>Een rol activeren

Wanneer u een Azure-resource functie moet uitvoeren, kunt u de activering aanvragen met de navigatie optie **mijn rollen** in Pim.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**. Zie voor meer informatie over het toevoegen van de PIM-tegel aan uw dash board [starten met behulp van Pim](pim-getting-started.md).

1. Klik op **mijn rollen**.

    ![Pagina mijn rollen met rollen die u kunt activeren](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Klik op **Azure-resource rollen** om een lijst weer te geven met uw in aanmerking komende Azure-resource rollen.

   ![Mijn rollen-pagina Azure-resource rollen](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Zoek in de lijst met **Azure-resource rollen** de rol die u wilt activeren.

    ![Azure-resource rollen: mijn in aanmerking komende rollen lijst](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Klik op **activeren** om het deel venster activeren te openen.

1. Als voor uw rol multi-factor Authentication (MFA) is vereist, klikt **u op uw identiteit verifiëren voordat u doorgaat**. U hoeft slechts één keer per sessie te authenticeren.

    ![Mijn identiteit met MFA verifiëren voordat de functie wordt geactiveerd](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Klik op **Mijn identiteit verifiëren** en volg de instructies voor aanvullende beveiligings verificatie.

    ![Scherm om beveiligings verificatie te bieden, zoals een pincode](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Als u een kleiner bereik wilt opgeven, klikt u op **bereik** om het deel venster Resource filter te openen.

    Het is een best practice om alleen toegang te vragen voor de resources die u nodig hebt. In het deel venster Resource filter kunt u de resource groepen of bronnen opgeven waartoe u toegang nodig hebt.

    ![Activeren-resource filter venster om het bereik op te geven](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Geef indien nodig een aangepaste begin tijd voor de activering op. Het lid wordt na de geselecteerde tijd geactiveerd.

1. Voer in het vak **reden** de reden voor de activerings aanvraag in.

    ![Deel venster activeren met bereik, start tijd, duur en reden is voltooid](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Klik op **Activeren**.

    Als voor de rol geen goed keuring is vereist, wordt deze geactiveerd en toegevoegd aan de lijst met actieve rollen. Als u de rol wilt gebruiken, volgt u de stappen in de volgende sectie.

    Als voor de [rol goed keuring moet](pim-resource-roles-approval-workflow.md) worden geactiveerd, wordt in de rechter bovenhoek van uw browser gemeld dat de aanvraag goed keuring in behandeling is.

    ![Goedkeurings melding in afwachting van activerings aanvraag](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Een rol direct na de activering gebruiken

In het geval van een vertraging na de activering voert u de volgende stappen uit nadat u hebt geactiveerd om uw Azure-resource rollen direct te gebruiken.

1. Open Azure AD Privileged Identity Management.

1. Klik op **mijn rollen** om een lijst weer te geven met uw in aanmerking komende Azure AD-rollen en Azure-resource rollen.

1. Klik op **Azure-resource rollen**.

1. Klik op het tabblad **actieve rollen** .

1. Zodra de rol actief is, meldt u zich af bij de portal en meldt u zich opnieuw aan.

    De rol moet nu beschikbaar zijn voor gebruik.

## <a name="view-the-status-of-your-requests"></a>De status van uw aanvragen weer geven

U kunt de status van de in behandeling zijnde aanvragen weer geven om te activeren.

1. Open Azure AD Privileged Identity Management.

1. Klik op **mijn aanvragen** om een lijst weer te geven met uw Azure AD-rol en aanvragen voor Azure-resource rollen.

    ![Mijn aanvragen-Azure-resource pagina met uw aanvragen in behandeling](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Schuif naar rechts om de kolom Status van de **aanvraag** weer te geven.

## <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

Als u geen activering van een rol vereist waarvoor goed keuring is vereist, kunt u een aanvraag in behandeling op elk gewenst moment annuleren.

1. Open Azure AD Privileged Identity Management.

1. Klik op **mijn aanvragen**.

1. Klik op de koppeling **Annuleren** voor de functie die u wilt annuleren.

    Wanneer u op Annuleren klikt, wordt de aanvraag geannuleerd. Als u de functie opnieuw wilt activeren, moet u een nieuwe aanvraag indienen voor activering.

   ![Mijn aanvraag lijst met de actie annuleren gemarkeerd](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Machtigingen worden niet toegekend na het activeren van een rol

Wanneer u een rol in PIM activeert, wordt de activering mogelijk niet onmiddellijk door gegeven aan alle portals waarvoor de rol met privileges is vereist. Zelfs als de wijziging is doorgevoerd, kan het soms gebeuren dat webcaching in een portal tot gevolg heeft dat de wijziging niet onmiddellijk van kracht gaat. Als de activering is vertraagd, is dit wat u moet doen.

1. Meld u af bij de Azure-portal en meld u weer aan.

    Wanneer u een Azure-resource functie activeert, worden de fasen van uw activering weer geven. Wanneer alle fasen zijn voltooid, ziet u de koppeling **Afmelden**. Met deze koppeling kunt u zich afmelden. In de meeste gevallen is de vertraging hiermee opgelost.

1. Controleer in PIM of u wordt vermeld als lid van de rol.

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen in PIM uitbreiden of vernieuwen](pim-resource-roles-renew-extend.md)
- [Mijn Microsoft Azure AD-rollen activeren in PIM](pim-how-to-activate-role.md)
