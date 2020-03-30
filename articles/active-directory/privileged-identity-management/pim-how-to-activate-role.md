---
title: Mijn Azure AD-rollen activeren in PIM - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het activeren van Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499229"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Mijn Microsoft Azure AD-rollen activeren in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vereenvoudigt de manier waarop ondernemingen bevoorrechte toegang tot bronnen in Azure AD en andere Online-services van Microsoft, zoals Office 365 of Microsoft Intune, beheren.  

Als u in aanmerking bent gekomen voor een administratieve rol, betekent dit dat u die rol activeren wanneer u bevoorrechte acties moet uitvoeren. Als u bijvoorbeeld af en toe Office 365-functies beheert, maken de bevoorrechte rolbeheerders van uw organisatie u mogelijk geen permanente globale beheerder, omdat die rol ook gevolgen heeft voor andere services. In plaats daarvan komen ze in aanmerking voor Azure AD-rollen, zoals Exchange Online Administrator. U vragen om die rol te activeren wanneer u de bevoegdheden nodig hebt, en dan hebt u beheerdersbeheer voor een vooraf bepaalde periode.

Dit artikel is bedoeld voor beheerders die hun Azure AD-rol moeten activeren in Privileged Identity Management.

## <a name="determine-your-version-of-pim"></a>Bepaal uw versie van PIM

Vanaf november 2019 wordt het gedeelte Azure AD-rollen van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen voor Azure-bronrollen. Dit zorgt voor extra functies en [wijzigingen in de bestaande API.](azure-ad-roles-features.md#api-changes) Terwijl de nieuwe versie wordt uitgerold, zijn welke procedures u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management hebt weten, u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met de rol [Privileged role administrator.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Azure **AD Privileged Identity Management openen**. Als u boven aan de overzichtspagina een banner hebt staan, volgt u de instructies op het tabblad **Nieuwe versie** van dit artikel. Volg anders de instructies op het tabblad **Vorige versie.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

## <a name="activate-a-role"></a>Een rol activeren

Wanneer u een Azure AD-rol moet aannemen, u activering aanvragen met de navigatieoptie **Mijn rollen** in Privileged Identity Management.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**. Zie [Start met Privileged Identity Management](pim-getting-started.md)voor informatie over het toevoegen van de tegel Privileged Identity Management aan uw dashboard.

1. Selecteer **Mijn rollen**en selecteer vervolgens Azure **AD-rollen** om een lijst met uw in aanmerking komende Azure AD-rollen weer te geven.

    ![Op mijn rollenpagina met rollen die u activeren](./media/pim-how-to-activate-role/my-roles.png)

1. Zoek in de lijst **met Azure AD-rollen** de rol die u wilt activeren.

    ![Azure AD-rollen - Mijn lijst met in aanmerking komende rollen](./media/pim-how-to-activate-role/activate-link.png)

1. Selecteer **Activeren** om het deelvenster Activeren te openen.

    ![Azure AD-rollen - activeringspagina bevat duur en bereik](./media/pim-how-to-activate-role/activate-page.png)

1. Als uw rol meervoudige verificatie vereist, selecteert **u Uw identiteit verifiëren voordat u verdergaat.** U hoeft zich slechts één keer per sessie te verifiëren.

    ![Mijn identiteit verifiëren met MFA voordat de rol wordt geactiveerd](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecteer **Mijn identiteit verifiëren** en volg de instructies om aanvullende beveiligingsverificatie te bieden.

    ![Scherm om beveiligingsverificatie te bieden, zoals een pincode](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Als u een beperkt bereik wilt opgeven, selecteert u **Bereik** om het filtervenster te openen. In het filtervenster u de Azure AD-resources opgeven waartoe u toegang nodig hebt. Het is een aanbevolen gewoonte om alleen toegang te vragen tot de resources die u nodig hebt.

1. Geef indien nodig een aangepaste begintijd voor activering op. De Azure AD-rol wordt na de geselecteerde tijd geactiveerd.

1. Voer in het vak **Reden** de reden voor de activeringsaanvraag in.

1. Selecteer **Activeren**.

    Als de rol geen goedkeuring vereist, wordt deze geactiveerd en toegevoegd aan de lijst met actieve rollen. Als u de rol wilt gebruiken, volgt u de stappen in de volgende sectie.

    ![Voltooid deelvenster Activeren met bereik, begintijd, duur en reden](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Als de [rol goedkeuring vereist](pim-resource-roles-approval-workflow.md) om te activeren, verschijnt er een melding in de rechterbovenhoek van uw browser waarin u wordt geïnformeerd dat het verzoek in behandeling is.

    ![Activeringsaanvraag is in afwachting van goedkeuringskennisgeving](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Een rol direct na activering gebruiken

In het geval van vertraging na activering, volgt u deze stappen nadat u hebt geactiveerd om uw Azure AD-rollen onmiddellijk te gebruiken.

1. Open Azure AD Privileged Identity Management.

1. Selecteer **Mijn rollen** om een lijst te zien met uw in aanmerking komende Azure AD-rollen en Azure-bronrollen.

1. Selecteer **Azure AD-rollen**.

1. Selecteer het tabblad **Actieve rollen.**

1. Zodra de rol actief is, meld u zich af bij het portaal en meldt u zich weer aan.

    De rol moet nu beschikbaar zijn voor gebruik.

## <a name="view-the-status-of-your-requests"></a>De status van uw aanvragen weergeven

U de status van uw in behandeling zijnde aanvragen bekijken om te activeren.

1. Open Azure AD Privileged Identity Management.

1. Selecteer **Mijn aanvragen** om een lijst met uw Azure AD-rol en Azure-bronrolaanvragen te bekijken.

    ![Mijn verzoeken - Azure AD-pagina met uw aanvragen in behandeling](./media/pim-how-to-activate-role/my-requests-page.png)

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

    Wanneer u een Azure AD-rol activeert, ziet u de fasen van uw activering. Wanneer alle fasen zijn voltooid, ziet u de koppeling **Afmelden**. U deze link gebruiken om u af te melden. Dit lost de meeste gevallen voor activeringvertraging op.

1. Controleer in Privileged Identity Management of u wordt vermeld als lid van de rol.

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="activate-a-role"></a>Een rol activeren

Wanneer u een Azure AD-rol moet overnemen, u activering aanvragen met de navigatieoptie **Mijn rollen** in Privileged Identity Management.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Azure **AD Privileged Identity Management openen**. Zie [Start met Privileged Identity Management](pim-getting-started.md)voor informatie over het toevoegen van de tegel Privileged Identity Management aan uw dashboard.

1. Klik op **Azure AD-rollen**.

1. Klik **op Mijn rollen** om een lijst te zien met uw in aanmerking komende Azure AD-rollen.

    ![Azure AD-rollen - Mijn rollen met lijst met in aanmerking komende of actieve rollen](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Zoek een rol die u wilt activeren.

    ![Azure AD-rollen - Mijn lijst met in aanmerking komende rollen met koppeling Activeren](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Klik **op Activeren** om het deelvenster Details van de activeringvan de rol te openen.

1. Als uw rol multifactorauthenticatie (MFA) vereist, klikt u op **Uw identiteit verifiëren voordat u verdergaat.** U hoeft zich slechts één keer per sessie te verifiëren.

    ![Mijn identiteitsvenster verifiëren met MFA voordat de rol wordt geactiveerd](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klik **op Mijn identiteit verifiëren** en volg de instructies om extra beveiligingsverificatie te bieden.

    ![Aanvullende pagina voor beveiligingsverificatie met de vraag hoe u contact met u opnemen](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Klik **op Activeren** om het deelvenster Activering te openen.

    ![Activeringsvenster om begintijd, duur, ticket en reden op te geven](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Geef indien nodig een aangepaste begintijd voor activering op.

1. Geef de activeringsduur op.

1. Voer in het vak **Activeringsreden** de reden voor de activeringsaanvraag in. Voor sommige rollen moet u een probleemticketnummer verstrekken.

    ![Voltooid activeringsvenster met een aangepaste begintijd, duur, ticket en reden](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klik op **Activeren**.

    Als de rol geen goedkeuring vereist, verschijnt er een **activeringsstatusvenster** met de status van de activering.

    ![Statuspagina activering met de drie activeringsfasen](./media/pim-how-to-activate-role/activation-status.png)

    Zodra alle fasen zijn voltooid, klikt u op de **koppeling Afmelden** om u af te melden bij de Azure-portal. Wanneer u zich weer aanmeldt bij de portal, u de rol nu gebruiken.

    Als de [rol goedkeuring vereist](./azure-ad-pim-approval-workflow.md) om te activeren, wordt een Azure-melding weergegeven in de rechterbovenhoek van uw browser waarin u wordt geïnformeerd dat de aanvraag in afwachting van goedkeuring is.

## <a name="view-the-status-of-your-requests"></a>De status van uw aanvragen weergeven

U de status van uw in behandeling zijnde aanvragen bekijken om te activeren.

1. Open Azure AD Privileged Identity Management.

1. Klik op **Azure AD-rollen**.

1. Klik **op Mijn verzoeken** om een lijst met uw aanvragen te bekijken.

    ![Azure AD-rollen - Lijst met mijn aanvragen](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Een rol deactiveren

Zodra een rol is geactiveerd, wordt deze automatisch gedeactiveerd wanneer de tijdslimiet (in aanmerking komende duur) is bereikt.

Als u uw beheerderstaken voortijdig voltooit, u een rol ook handmatig deactiveren in Azure AD Privileged Identity Management.

1. Open Azure AD Privileged Identity Management.

1. Klik op **Azure AD-rollen**.

1. Klik op **Mijn rollen**.

1. Klik **op Actieve rollen** om de lijst met actieve rollen weer te geven.

1. Zoek de rol die u hebt gebruikt en klik op **Deactiveren**.

## <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

Als u geen activering nodig hebt van een rol waarvoor goedkeuring vereist is, u een aanvraag in behandeling op elk gewenst moment annuleren.

1. Open Azure AD Privileged Identity Management.

1. Klik op **Azure AD-rollen**.

1. Klik op **Mijn aanvragen**.

1. Klik op **annuleren** voor de rol die u wilt annuleren.

    Wanneer u op Annuleren klikt, wordt het verzoek geannuleerd. Om de rol opnieuw te activeren, moet u een nieuw verzoek tot activering indienen.

   ![Lijst met mijn aanvragen met de knop Annuleren gemarkeerd](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Machtigingen worden niet toegekend na het activeren van een rol

Wanneer u een rol activeert in Privileged Identity Management, wordt de activering mogelijk niet onmiddellijk doorgegeven aan alle portalen waarvoor de bevoorrechte rol vereist is. Zelfs als de wijziging is doorgevoerd, kan het soms gebeuren dat webcaching in een portal tot gevolg heeft dat de wijziging niet onmiddellijk van kracht gaat. Als uw activering is vertraagd, hier is wat je moet doen.

1. Meld u af bij de Azure-portal en meld u weer aan.

    Wanneer u een Azure AD-rol activeert, ziet u de fasen van uw activering. Wanneer alle fasen zijn voltooid, ziet u de koppeling **Afmelden**. U deze link gebruiken om u af te melden. Dit lost de meeste gevallen voor activeringvertraging op.

1. Controleer in Privileged Identity Management of u wordt vermeld als lid van de rol.

 ---

## <a name="next-steps"></a>Volgende stappen

- [Mijn Azure AD-rollen activeren in Privileged Identity Management](pim-how-to-activate-role.md)
