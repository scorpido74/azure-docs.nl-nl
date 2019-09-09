---
title: Instellingen voor Azure AD-functies configureren in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor Azure AD-functies in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804438"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Instellingen voor Azure AD-rollen configureren in PIM

Een beheerder van een bevoegde rol kan Azure Active Directory (Azure AD) Privileged Identity Management (PIM) in hun organisatie aanpassen, met inbegrip van het wijzigen van de ervaring voor een gebruiker die een in aanmerking komende roltoewijzing activeert.

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

Gebruik de optie **meldingen** om op te geven of beheerders e-mail meldingen zullen ontvangen wanneer rollen worden geactiveerd. Dit kan handig zijn om niet-geautoriseerde of illegitimate-activeringen te detecteren.

Wanneer deze optie is ingesteld op **inschakelen**, worden meldingen verzonden naar:

- Beheerder met bevoorrechte rol
- Beveiligingsbeheerder
- Globale beheerder

Zie [e-mail meldingen in PIM](pim-email-notifications.md)voor meer informatie.

## <a name="incidentrequest-ticket"></a>Incident/aanvraag ticket

Gebruik de switch **incident/aanvraag ticket** om op te geven of u in aanmerking komende beheerders een ticket nummer moet hebben wanneer ze hun rol activeren. Dit kan handig zijn wanneer u toegangs controles voor rollen uitvoert.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Gebruik de schakel optie **multi-factor Authentication** om op te geven of gebruikers hun identiteit moeten verifiëren met MFA voordat ze hun rollen kunnen activeren. Ze hoeven dit eenmaal per sessie te controleren, niet elke keer dat ze een rol activeren. Er zijn twee tips die u moet onthouden wanneer u MFA inschakelt:

* Gebruikers met micro soft-accounts voor hun e-mail @outlook.comadressen (doorgaans, maar niet altijd) kunnen niet worden geregistreerd voor Azure MFA. Als u rollen wilt toewijzen aan gebruikers met micro soft-accounts, moet u deze permanent beheerder maken of MFA uitschakelen voor die rol.
* U kunt MFA niet uitschakelen voor rollen met zeer privileged voor Azure AD en Office365. Dit is een veiligheids functie omdat deze rollen zorgvuldig moeten worden beveiligd:  
  
  * Azure Information Protection beheerder
  * Factureringsbeheerder
  * Cloudtoepassingsbeheerder
  * Beheerder voor naleving
  * Voorwaardelijke toegang beheerder
  * CRM-servicebeheerder
  * Toegangsfiatteur voor Klanten-lockbox
  * Schrijvers van mappen
  * Exchange-beheerder
  * Globale beheerder
  * Intune-servicebeheerder
  * Power BI-servicebeheerder
  * Beheerder met bevoorrechte rol
  * Beveiligingsbeheerder
  * SharePoint-servicebeheerder
  * Skype voor Bedrijven-beheerder
  * Gebruikerbeheerder

Zie [multi-factor Authentication (MFA) en PIM](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="require-approval"></a>Goedkeuring vereisen

Als u wilt dat goed keuring vereist is om een rol te activeren, voert u de volgende stappen uit.

1. Stel de schakel optie **goed keuring vereisen** in op **ingeschakeld**. Het deel venster wordt uitgebreid met opties voor het selecteren van goed keurders.

    ![Azure AD-rollen-instellingen-goed keuring vereisen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Als u **geen** goed keurders opgeeft, worden de beheerders met verhoogde bevoegdheden de standaard fiatteurs. Beheerders van geprivilegieerde rollen moeten **alle** activerings aanvragen voor deze rol goed keuren.

1. Als u goed keurders wilt opgeven, klikt u op **fiatteurs selecteren**.

    ![Azure AD-rollen-instellingen-goed keuring vereisen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecteer een of meer goed keurders en klik vervolgens op **selecteren**. U kunt gebruikers of groepen selecteren. Ten minste twee goed keurders wordt aanbevolen. Zelf goedkeuring is niet toegestaan.

    Uw selecties worden weer gegeven in de lijst met geselecteerde goed keurders.

1. Wanneer u uw rolinstellingen hebt opgegeven, klikt u op **Opslaan** om uw wijzigingen op te slaan.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in PIM](pim-how-to-add-role-to-user.md)
- [Beveiligings waarschuwingen configureren voor Azure AD-rollen in PIM](pim-how-to-configure-security-alerts.md)
