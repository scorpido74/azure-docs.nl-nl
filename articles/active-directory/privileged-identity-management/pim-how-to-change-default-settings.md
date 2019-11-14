---
title: Instellingen voor Azure AD-functies configureren in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor Azure AD-functies in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021800"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Instellingen voor Azure AD-functies configureren in Privileged Identity Management

Een beheerder van een bevoegde rol kan Privileged Identity Management (PIM) in hun Azure Active Directory (Azure AD)-organisatie aanpassen, met inbegrip van het wijzigen van de ervaring voor een gebruiker die een in aanmerking komende roltoewijzing activeert.

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure AD-functie te openen.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

1. Klik op **Instellingen**.

    ![Azure AD-rollen-instellingen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Klik op **rollen**.

1. Klik op de rol waarvan u de instellingen wilt configureren.

    ![Azure AD-rollen-instellingen rollen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Op de pagina instellingen voor elke rol zijn verschillende instellingen die u kunt configureren. Deze instellingen zijn alleen van invloed op gebruikers die **in aanmerking komen** voor toewijzingen, niet **permanente** toewijzingen.

## <a name="activations"></a>Activeringen

Gebruik de schuif regelaar **activeringen** om de maximale tijd in uren in te stellen dat een rol actief blijft voordat deze verloopt. Deze waarde kan tussen 1 en 72 uur liggen.

## <a name="notifications"></a>Meldingen

Gebruik de optie **meldingen** om op te geven of beheerders e-mail meldingen zullen ontvangen wanneer rollen worden geactiveerd. Deze melding kan handig zijn voor het detecteren van ongeoorloofde of illegitimate activeringen.

Wanneer deze optie is ingesteld op **inschakelen**, worden meldingen verzonden naar:

- Beheerder van geprivilegieerde rol
- Beveiligingsbeheerder
- Globale beheerder

Zie [e-mail meldingen in privileged Identity Management](pim-email-notifications.md)voor meer informatie.

## <a name="incidentrequest-ticket"></a>Incident/aanvraag ticket

Gebruik de switch **incident/aanvraag ticket** om in aanmerking komende beheerders te vragen om een ticket nummer op te nemen wanneer ze hun rol activeren. Met deze procedure kunt u toegangs controles voor rollen effectiever uitvoeren.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Gebruik de schakel optie **multi-factor Authentication** om op te geven of gebruikers hun identiteit moeten verifiëren met MFA voordat ze hun rollen kunnen activeren. Ze hoeven hun identiteit slechts eenmaal per sessie te verifiëren, niet elke keer dat ze een rol activeren. Er zijn twee tips die u moet onthouden wanneer u MFA inschakelt:

- Gebruikers met micro soft-accounts voor hun e-mail adressen (meestal @outlook.com, maar niet altijd) kunnen zich niet registreren voor Azure Multi-Factor Authentication. Als u rollen wilt toewijzen aan gebruikers met micro soft-accounts, moet u deze permanent beheerder maken of multi-factor Authentication uitschakelen voor die rol.
- U kunt Azure Multi-Factor Authentication niet uitschakelen voor rollen met een hoge bevoegdheden voor Azure AD en Office 365. Deze veiligheids functie helpt bij het beveiligen van de volgende rollen:  
  
  - Azure Information Protection beheerder
  - Factureringsbeheerder
  - Beheerder van de Cloud toepassing
  - Beheerder voor naleving
  - Voorwaardelijke-toegangsbeheerder
  - Dynamics 365-beheerder
  - Toegangs fiatteur voor klanten-LockBox
  - Schrijvers van mappen
  - Exchange-beheerder
  - Globale beheerder
  - InTune-beheerder
  - Power BI beheerder
  - Beheerder van geprivilegieerde rol
  - Beveiligingsbeheerder
  - SharePoint-beheerder
  - Skype voor bedrijven-beheerder
  - Gebruikers beheerder

Zie [multi-factor Authentication en privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="require-approval"></a>Goed keuring vereisen

Als u de vereiste goed keuring wilt delegeren om een rol te activeren, voert u de volgende stappen uit.

1. Stel de schakel optie **goed keuring vereisen** in op **ingeschakeld**. Het deel venster wordt uitgebreid met opties voor het selecteren van goed keurders.

    ![Azure AD-rollen-instellingen-goed keuring vereisen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Als u geen goed keurders opgeeft, wordt de beheerder van de bevoegde rol de standaard fiatteur. vervolgens moet alle activerings aanvragen voor deze rol worden goedgekeurd.

1. Als u goed keurders wilt opgeven, klikt u op **fiatteurs selecteren**.

    ![Azure AD-rollen-instellingen-goed keuring vereisen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecteer een of meer goed keurders naast de beheerder van de geprivilegieerde rol en klik vervolgens op **selecteren**. U kunt gebruikers of groepen selecteren. We raden ten minste twee goed keurders aan. Zelfs als u uzelf als fiatteur toevoegt, kunt u een functie activering niet zelf goed keuren. Uw selecties worden weer gegeven in de lijst met geselecteerde goed keurders.

1. Nadat u de instellingen van uw rol hebt opgegeven, selecteert u **Opslaan** om uw wijzigingen op te slaan.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Beveiligings waarschuwingen configureren voor Azure AD-rollen in Privileged Identity Management](pim-how-to-configure-security-alerts.md)
