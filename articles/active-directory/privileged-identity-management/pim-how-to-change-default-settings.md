---
title: Azure AD-rolinstellingen configureren in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het configureren van Azure AD-rolinstellingen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205021"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Azure AD-rolinstellingen configureren in Privileged Identity Management

Een bevoegde rolbeheerder kan Privileged Identity Management (PIM) aanpassen in zijn Azure Active Directory (Azure AD)-organisatie, inclusief het wijzigen van de ervaring voor een gebruiker die een in aanmerking komende roltoewijzing activeert.

## <a name="determine-your-version-of-pim"></a>Bepaal uw versie van PIM

Vanaf november 2019 wordt het gedeelte Azure AD-rollen van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen voor Azure-bronrollen. Dit zorgt voor extra functies en [wijzigingen in de bestaande API.](azure-ad-roles-features.md#api-changes) Terwijl de nieuwe versie wordt uitgerold, zijn welke procedures u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management hebt weten, u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een gebruiker die zich in de rol [Van Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) bevindt.
1. Azure **AD Privileged Identity Management openen**. Als u boven aan de overzichtspagina een banner hebt staan, volgt u de instructies op het tabblad **Nieuwe versie** van dit artikel. Volg anders de instructies op het tabblad **Vorige versie.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Volg de stappen in dit artikel om aanvragen voor Azure AD-rollen goed te keuren of te weigeren.

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure AD-rol te openen.

1. Meld u aan bij [azure portal](https://portal.azure.com/) met een gebruiker in de rol Privileged [Role Administrator.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
Gt
1. Azure **AD Privileged Identity Management** &gt; **Azure AD-rollenrolinstellingen** &gt; **openen**.

    ![Pagina Met pagina Met Azure AD-rollen met functie-instellingen](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selecteer de rol waarvan u de instellingen wilt configureren.

    ![Functie-instelling details pagina met verschillende toewijzing en activering instellingen](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selecteer **Bewerken** om de pagina Rolinstellingen te openen.

    ![Pagina Rolinstellingen bewerken met opties voor het bijwerken van toewijzings- en activeringsinstellingen](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    In het venster Rolinstelling voor elke rol zijn er verschillende instellingen die u configureren.

## <a name="assignment-duration"></a>Toewijzingsduur

U kiezen uit twee opties voor toewijzingsduur voor elk toewijzingstype (in aanmerking komend en actief) wanneer u instellingen voor een rol configureert. Deze opties worden de standaard maximale duur wanneer een gebruiker is toegewezen aan de rol in Privileged Identity Management.

U een van deze **in aanmerking komende** opties voor de duur van de toewijzing kiezen:

| | |
| --- | --- |
| **Permanente toewijzing toestaan** | Globale beheerders en bevoorrechte rolbeheerders kunnen permanente toewijzing toewijzen. |
| **In aanmerking komende toewijzing aflopen na** | Globale beheerders en bevoorrechte rolbeheerders kunnen vereisen dat alle in aanmerking komende toewijzingen een opgegeven begin- en einddatum hebben. |

En u een van deze opties voor **actieve** toewijzingsduur kiezen:

| | |
| --- | --- |
| **Permanente actieve toewijzing toestaan** | Globale beheerders en bevoorrechte rolbeheerders kunnen permanente actieve toewijzing toewijzen. |
| **Actieve toewijzing verlopen na** | Globale beheerders en bevoorrechte rolbeheerders kunnen vereisen dat alle actieve toewijzingen een opgegeven begin- en einddatum hebben. |

> [!NOTE]
> Alle toewijzingen met een opgegeven einddatum kunnen worden verlengd door globale beheerders en bevoorrechte rolbeheerders. Gebruikers kunnen ook selfserviceaanvragen initiëren om [roltoewijzingen uit](pim-resource-roles-renew-extend.md)te breiden of te vernieuwen.

## <a name="require-multi-factor-authentication"></a>Meervoudige verificatie vereisen

Privileged Identity Management biedt optionele handhaving van Azure Multi-Factor Authentication voor twee verschillende scenario's.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multifactorauthenticatie vereisen bij actieve toewijzing

In sommige gevallen u een gebruiker voor een korte duur (bijvoorbeeld één dag) aan een rol toewijzen. In dit geval hoeven de toegewezen gebruikers geen activering aan te vragen. In dit scenario kan Privileged Identity Management geen meervoudige verificatie afdwingen wanneer de gebruiker zijn roltoewijzing gebruikt omdat hij al actief is in de rol vanaf het moment dat deze is toegewezen.

Als u ervoor wilt zorgen dat de beheerder die de toewijzing vervult, de taak is wie hij zegt dat ze zijn, u multi-factor authenticatie afdwingen bij actieve toewijzing door het vak **Multifactorverificatie vereisen in actieve toewijzing aan** te vinken.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication vereisen bij activering

U gebruikers die in aanmerking komen voor een rol vereisen dat ze bewijzen wie ze Azure Multi-Factor Authentication gebruiken voordat ze kunnen worden geactiveerd. Multi-factor authenticatie zorgt ervoor dat de gebruiker is wie ze zeggen dat ze zijn met redelijke zekerheid. Als u deze optie afdwingt, worden kritieke resources beschermd in situaties waarin het gebruikersaccount mogelijk is gecompromitteerd.

Als u multifactorverificatie wilt vereisen voordat u wordt geactiveerd, schakelt u het selectievakje **Multifactorverificatie vereisen bij activering in** het tabblad Toewijzing van De **rolinstelling Bewerken in**.

Zie [Multi-factor authenticatie en Privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="activation-maximum-duration"></a>Maximale duur activering

Gebruik de schuifregelaar **Activering seinmaximale duur** om de maximale tijd in te stellen, in uren, dat een rol actief blijft voordat deze verloopt. Deze waarde kan van één tot 24 uur zijn.

## <a name="require-justification"></a>Rechtvaardiging vereisen

U vereisen dat gebruikers een zakelijke rechtvaardiging invoeren wanneer ze worden geactiveerd. Als u rechtvaardiging wilt vereisen, schakelt u het vak **Eis voor actieve toewijzing** of het selectievakje **Motivering op activering vereisen in.**

## <a name="require-approval-to-activate"></a>Goedkeuring vereisen om te activeren

Als u meerdere fiatteurs instelt, wordt de goedkeuring voltooid zodra een van hen dit goedkeurt of weigert. U geen goedkeuring van ten minste twee gebruikers nodig hebben. Voer deze stappen uit om goedkeuring te vereisen om een rol te activeren.

1. Schakel het selectievakje **Goedkeuring vereisen in om te activeren.**

1. Selecteer **Select van de goeders**.

    ![Een gebruiker of groepsvenster selecteren om fiatteurs te selecteren](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecteer ten minste één gebruiker en klik op **Selecteren**. U moet ten minste één goedkeurder selecteren. Er zijn geen standaard fiatteurs.

    Uw selecties worden weergegeven in de lijst met geselecteerde goedkeurders.

1. Zodra u al uw rolinstellingen hebt opgegeven, selecteert u **Bijwerken** om uw wijzigingen op te slaan.

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure AD-rol te openen.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Instellingen**.

    ![Azure AD-rollen - Instellingen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecteer **Rollen**.

1. Selecteer de rol waarvan u de instellingen wilt configureren.

    ![Azure AD-rollen - Rollen instellen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Op de instellingenpagina voor elke rol zijn er verschillende instellingen die u configureren. Deze instellingen hebben alleen betrekking op gebruikers die **in aanmerking komen** voor toewijzingen, niet op **permanente** toewijzingen.

## <a name="activations"></a>Activeringen

Gebruik de schuifregelaar **Activeringen** om de maximale tijd in te stellen, in uren, dat een rol actief blijft voordat deze verloopt. Deze waarde kan tussen de 1 en 72 uur liggen.

## <a name="notifications"></a>Meldingen

Gebruik de **overschakeling meldingen** om aan te geven of beheerders e-mailmeldingen ontvangen wanneer rollen worden geactiveerd. Deze melding kan handig zijn voor het detecteren van ongeautoriseerde of onwettige activeringen.

Wanneer u bent ingesteld op **Inschakelen,** worden meldingen verzonden naar:

- Bevoegde rolbeheerder
- Beveiligingsbeheerder
- Globale beheerder

Zie [E-mailmeldingen in Privileged Identity Management](pim-email-notifications.md)voor meer informatie.

## <a name="incidentrequest-ticket"></a>Incident/Request ticket

Gebruik de **ticketswitch Incident/Request** om in aanmerking komende beheerders te verplichten een ticketnummer op te nemen wanneer ze hun rol activeren. Deze praktijk kan roltoegangsaudits effectiever maken.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Gebruik de **multi-factor verificatieschakelaar** om aan te geven of gebruikers hun identiteit met MFA moeten verifiëren voordat ze hun rollen kunnen activeren. Ze hoeven hun identiteit slechts één keer per sessie te verifiëren, niet elke keer dat ze een rol activeren. Er zijn twee tips om in gedachten te houden wanneer u MFA inschakelt:

- Gebruikers die Microsoft-accounts hebben voor hun @outlook.come-mailadressen (meestal , maar niet altijd) kunnen zich niet registreren voor Azure Multi-Factor Authentication. Als u rollen wilt toewijzen aan gebruikers met Microsoft-accounts, moet u ze permanente beheerders maken of meervoudige verificatie voor die rol uitschakelen.
- U Azure Multi-Factor Authentication niet uitschakelen voor zeer bevoorrechte rollen voor Azure AD en Office 365. Deze veiligheidsfunctie helpt de volgende rollen te beschermen:  
  
  - Azure Information Protection-beheerder
  - Factureringsbeheerder
  - Beheerder van cloudtoepassingen
  - Compliancebeheerder
  - Voorwaardelijke-toegangsbeheerder
  - Dynamics 365-beheerder
  - Klant LockBox-toegangsfiattor
  - Directory schrijvers
  - Exchange-beheerder
  - Globale beheerder
  - Intune-beheerder
  - Power BI-beheerder
  - Bevoegde rolbeheerder
  - Beveiligingsbeheerder
  - SharePoint-beheerder
  - Skype voor Bedrijven-beheerder
  - Gebruikersbeheerder

Zie [Multi-factor authenticatie en Privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="require-approval"></a>Goedkeuring vereisen

Als u de vereiste goedkeuring wilt delegeren om een rol te activeren, voert u deze stappen uit.

1. Stel de **goedkeuringsschakelaar vereisen** in **op Ingeschakeld**. Het deelvenster wordt uitgebreid met opties om fiatteurs te selecteren.

    ![Azure AD-rollen - Instellingen - Goedkeuring vereisen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Als u geen fiatteurs opgeeft, wordt de beheerder van de bevoegde rol de standaardfiatteur en moet deze vervolgens alle activeringsaanvragen voor deze rol goedkeuren.

1. Als u fiattes wilt toevoegen, klikt u op **Goederen selecteren**.

    ![Azure AD-rollen - Instellingen - Goedkeuring vereisen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecteer een of meer fiatteurs naast de beheerder van de bevoegde rol en klik op **Selecteren**. We raden u aan ten minste twee fiatteurs toe te voegen. Zelfs als je jezelf toevoegt als een goedkeurder, kun je een rolactivering niet zelf goedkeuren. Uw selecties worden weergegeven in de lijst met geselecteerde goedkeurders.

1. Nadat u al uw rolinstellingen hebt opgegeven, selecteert u **Opslaan** om uw wijzigingen op te slaan.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Beveiligingswaarschuwingen configureren voor Azure AD-rollen in Privileged Identity Management](pim-how-to-configure-security-alerts.md)
