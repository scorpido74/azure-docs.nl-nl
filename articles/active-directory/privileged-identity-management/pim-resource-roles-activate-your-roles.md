---
title: Azure-bronrollen activeren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het activeren van uw Azure-bronrollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023155"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Mijn Azure-bronrollen activeren in Privileged Identity Management

Gebruik Privileged Identity Management (PIM) om in aanmerking komende rolleden voor Azure-resources de activering voor een toekomstige datum en tijd te laten plannen. Ze kunnen ook een specifieke activeringsduur selecteren binnen het maximum (geconfigureerd door beheerders).

Dit artikel is bedoeld voor leden die hun Azure-bronrol moeten activeren in Privileged Identity Management.

## <a name="activate-a-role"></a>Een rol activeren

Wanneer u een Azure-bronrol moet overnemen, u activering aanvragen met de navigatieoptie **Mijn rollen** in Privileged Identity Management.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**. Zie [Start met Privileged Identity Management](pim-getting-started.md)voor informatie over het toevoegen van de tegel Privileged Identity Management aan uw dashboard.

1. Selecteer **Mijn rollen**.

    ![Op mijn rollenpagina met rollen die u activeren](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selecteer **Azure-bronrollen** om een lijst met uw in aanmerking komende Azure-bronrollen te bekijken.

   ![Pagina Mijn rollen - Azure-resourcerollen](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Zoek in de lijst **met Azure-bronrollen** de rol die u wilt activeren.

    ![Azure-bronrollen - Mijn lijst met in aanmerking komende rollen](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Selecteer **Activeren** om het deelvenster Activeren te openen.

1. Als uw rol meervoudige verificatie vereist, selecteert **u Uw identiteit verifiëren voordat u verdergaat.** U hoeft zich slechts één keer per sessie te verifiëren.

    ![Mijn identiteit verifiëren met MFA voordat de rol wordt geactiveerd](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecteer **Mijn identiteit verifiëren** en volg de instructies om aanvullende beveiligingsverificatie te bieden.

    ![Scherm om beveiligingsverificatie te bieden, zoals een pincode](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Als u een beperkt bereik wilt opgeven, selecteert u **Bereik** om het deelvenster Resourcefilter te openen.

    Het is een aanbevolen manier om alleen toegang te vragen tot de resources die u nodig hebt. In het filtervenster Resource u de brongroepen of resources opgeven waartoe u toegang nodig hebt.

    ![Activeren - deelvenster Resourcefilter om bereik op te geven](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Geef indien nodig een aangepaste begintijd voor activering op. Het lid wordt na de geselecteerde tijd geactiveerd.

1. Voer in het vak **Reden** de reden voor de activeringsaanvraag in.

    ![Voltooid deelvenster Activeren met bereik, begintijd, duur en reden](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Selecteer **Activeren**.

    Als de rol geen goedkeuring vereist, wordt deze geactiveerd en toegevoegd aan de lijst met actieve rollen. Als u de rol wilt gebruiken, volgt u de stappen in de volgende sectie.

    Als de [rol goedkeuring vereist](pim-resource-roles-approval-workflow.md) om te activeren, verschijnt er een melding in de rechterbovenhoek van uw browser waarin u wordt geïnformeerd dat het verzoek in behandeling is.

    ![Activeringsaanvraag is in afwachting van goedkeuringskennisgeving](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Een rol direct na activering gebruiken

In het geval van vertraging na activering, volgt u deze stappen nadat u hebt geactiveerd om uw Azure-bronrollen onmiddellijk te gebruiken.

1. Open Azure AD Privileged Identity Management.

1. Selecteer **Mijn rollen** om een lijst te zien met uw in aanmerking komende Azure AD-rollen en Azure-bronrollen.

1. Selecteer **Azure-bronrollen**.

1. Selecteer het tabblad **Actieve rollen.**

1. Zodra de rol actief is, meld u zich af bij het portaal en meldt u zich weer aan.

    De rol moet nu beschikbaar zijn voor gebruik.

## <a name="view-the-status-of-your-requests"></a>De status van uw aanvragen weergeven

U de status van uw in behandeling zijnde aanvragen bekijken om te activeren.

1. Open Azure AD Privileged Identity Management.

1. Selecteer **Mijn aanvragen** om een lijst met uw Azure AD-rol en Azure-bronrolaanvragen te bekijken.

    ![Mijn aanvragen - Azure-bronpagina met uw aanvragen in behandeling](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Schuif naar rechts om de kolom **Status aanvragen** weer te geven.

## <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

Als u geen activering nodig hebt van een rol waarvoor goedkeuring vereist is, u een aanvraag in behandeling op elk gewenst moment annuleren.

1. Open Azure AD Privileged Identity Management.

1. Selecteer **Mijn aanvragen**.

1. Selecteer de koppeling **Annuleren** voor de rol die u wilt annuleren.

    Wanneer u Annuleren selecteert, wordt het verzoek geannuleerd. Om de rol opnieuw te activeren, moet u een nieuw verzoek tot activering indienen.

   ![Mijn aanvraaglijst met actie Annuleren gemarkeerd](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Machtigingen worden niet toegekend na het activeren van een rol

Wanneer u een rol activeert in Privileged Identity Management, wordt de activering mogelijk niet onmiddellijk doorgegeven aan alle portalen waarvoor de bevoorrechte rol vereist is. Zelfs als de wijziging is doorgevoerd, kan het soms gebeuren dat webcaching in een portal tot gevolg heeft dat de wijziging niet onmiddellijk van kracht gaat. Als uw activering is vertraagd, hier is wat je moet doen.

1. Meld u af bij de Azure-portal en meld u weer aan.

    Wanneer u een Azure-bronrol activeert, ziet u de fasen van uw activering. Wanneer alle fasen zijn voltooid, ziet u de koppeling **Afmelden**. U deze link gebruiken om u af te melden. Dit lost de meeste gevallen voor activeringvertraging op.

1. Controleer in Privileged Identity Management of u wordt vermeld als lid van de rol.

## <a name="next-steps"></a>Volgende stappen

- [Azure-bronrollen uitbreiden of vernieuwen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Mijn Azure AD-rollen activeren in Privileged Identity Management](pim-how-to-activate-role.md)
