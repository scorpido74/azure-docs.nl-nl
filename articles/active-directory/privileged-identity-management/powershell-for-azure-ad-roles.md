---
title: PowerShell voor Ad-rollen in AZURE- ad-rollen in PIM - Azure AD | Microsoft Documenten
description: Azure AD-rollen beheren met PowerShell-cmdlets in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519656"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell voor Azure AD-rollen in Privileged Identity Management

Dit artikel bevat instructies voor het gebruik van Azure Active Directory (Azure AD) PowerShell-cmdlets voor het beheren van Azure AD-rollen in Privileged Identity Management (PIM). Het vertelt u ook hoe u instellen met de Azure AD PowerShell-module.

> [!Note]
> Onze officiële PowerShell wordt alleen ondersteund als u de nieuwe versie van Azure AD Privileged Identity Management bevindt. Ga naar Privileged Identity Management en zorg ervoor dat je de volgende banner op het snelle startblad hebt.
> [![controleer de versie van Privileged Identity Management die u hebt](media/pim-how-to-add-role-to-user/pim-new-version.png "Azure AD-> privileged Identity Management selecteren")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Als u deze banner niet hebt, wacht dan zoals we momenteel bezig zijn met het uitrollen van deze bijgewerkte ervaring in de komende weken.
> De PowerShell-cmdlets van Privileged Identity Management worden ondersteund via de Azure AD Preview-module. Als u een andere module hebt gebruikt en die module nu een foutmelding retourt, u deze nieuwe module gebruiken. Als er productiesystemen zijn gebouwd bovenop een andere module, neem dan contact op metpim_preview@microsoft.com

## <a name="installation-and-setup"></a>Installatie en setup

1. De Azure AD Preview-module installeren

        Install-module AzureADPreview

1. Zorg ervoor dat u over de vereiste functiemachtigingen beschikt voordat u verdergaat. Als u beheertaken probeert uit te voeren, zoals het geven van een roltoewijzing of het bijwerken van de rolinstelling, moet u ervoor zorgen dat u de rol globale beheerder of bevoorrechte rolbeheerder hebt. Als u alleen uw eigen toewijzing probeert te activeren, zijn er geen machtigingen vereist die verder gaan dan de standaardgebruikersmachtigingen.

1. Verbinding maken met Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Zoek uw tenant-id door naar **Azure Active Directory** > **Properties** > **Directory ID**te gaan . Gebruik deze id in de cmdletssectie wanneer u de resourceId moet leveren.

    ![De tenant-id zoeken in de eigenschappen voor de Azure AD-organisatie](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> De volgende secties zijn eenvoudige voorbeelden die u aan de slag kunnen helpen. Meer gedetailleerde documentatie over de volgende cmdlets vindt u op https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management. U moet echter "azureResources" in de parameter providerID vervangen door "aadRoles". U moet ook niet vergeten de tenant-id voor uw Azure AD-organisatie te gebruiken als parameter resourceId.

## <a name="retrieving-role-definitions"></a>Roldefinities ophalen

Gebruik de volgende cmdlet om alle ingebouwde en aangepaste Azure AD-rollen in uw Azure AD-organisatie (tenant) op te halen. Deze belangrijke stap geeft u de mapping tussen de rolnaam en de roleDefinitionId. De rolDefinitionId wordt gebruikt in deze cmdlets om te verwijzen naar een specifieke rol.

De roleDefinitionId is specifiek voor uw Azure AD-organisatie en verschilt van de roleDefinitionId die wordt geretourneerd door de API voor rolbeheer.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Resultaat:

![Alle rollen voor de Azure AD-organisatie opvragen](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Roltoewijzingen ophalen

Gebruik de volgende cmdlet om alle roltoewijzingen in uw Azure AD-organisatie op te halen.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Gebruik de volgende cmdlet om alle roltoewijzingen voor een bepaalde gebruiker op te halen. Deze lijst wordt ook wel 'Mijn rollen' genoemd in de Azure AD-portal. Het enige verschil hier is dat je een filter hebt toegevoegd voor de onderwerp-ID. De onderwerp-id in deze context is de gebruikersnaam of de groeps-ID.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Gebruik de volgende cmdlet om alle roltoewijzingen voor een bepaalde rol op te halen. De roleDefinitionId hier is de ID die wordt geretourneerd door de vorige cmdlet.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

De cmdlets resulteren in een lijst met onderstaande roltoewijzingsobjecten. De onderwerp-id is de gebruikersnaam van de gebruiker aan wie de rol is toegewezen. De toewijzingsstatus kan actief zijn of in aanmerking komen. Als de gebruiker actief is en er een ID is in het veld LinkedEligibleRoleAssignmentId, betekent dit dat de rol momenteel is geactiveerd.

Resultaat:

![Alle roltoewijzingen voor de Azure AD-organisatie ophalen](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Een rol toewijzen

Gebruik de volgende cmdlet om een in aanmerking komende toewijzing te maken.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

De planning, waarin de begin- en eindtijd van de toewijzing wordt gedefinieerd, is een object dat kan worden gemaakt zoals het volgende voorbeeld:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Als de waarde van endDateTime is ingesteld op null, duidt dit op een permanente toewijzing.

## <a name="activate-a-role-assignment"></a>Een roltoewijzing activeren

Gebruik de volgende cmdlet om een in aanmerking komende toewijzing te activeren.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Deze cmdlet is bijna identiek aan de cmdlet voor het maken van een roltoewijzing. Het belangrijkste verschil tussen de cmdlets is dat voor de parameter -Type activering "userAdd" is in plaats van "adminAdd". Het andere verschil is dat de parameter -AssignmentState 'Actief' is in plaats van 'In aanmerking komen'.

> [!Note]
> Er zijn twee beperkende scenario's voor rolactivering via PowerShell.
> 1. Als u ticketsysteem / ticketnummer nodig hebt in uw rolinstelling, is er geen manier om deze als parameter te leveren. Het zou dus niet mogelijk zijn om de rol buiten de Azure-portal te activeren. Deze functie wordt de komende maanden uitgerold naar PowerShell.
> 1. Als u multi-factor authenticatie nodig hebt voor rolactivering, is er momenteel geen manier voor PowerShell om de gebruiker uit te dagen wanneer ze hun rol activeren. In plaats daarvan moeten gebruikers de MFA-uitdaging activeren wanneer ze verbinding maken met Azure AD door [deze blogpost](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) van een van onze technici te volgen. Als u een app voor PIM ontwikkelt, is een mogelijke implementatie om gebruikers uit te dagen en opnieuw aan de module te verbinden nadat ze een fout in "MfaRule" hebben ontvangen.

## <a name="retrieving-and-updating-role-settings"></a>Functie-instellingen ophalen en bijwerken

Gebruik de volgende cmdlet om alle rolinstellingen in uw Azure AD-organisatie op te halen.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Er zijn vier belangrijke objecten in de instelling. Slechts drie van deze objecten worden momenteel gebruikt door PIM. De Gebruikerslidinstellingen zijn activeringsinstellingen, Instellingen voor beheerdersin aanmerking komen de toewijzingsinstellingen voor in aanmerking komende toewijzingen en de instellingen voor beheerdersleden zijn toewijzingsinstellingen voor actieve toewijzingen.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Als u de rolinstelling wilt bijwerken, moet u eerst een instellingsobject als volgt definiëren:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

U dan doorgaan en de instelling toepassen op een van de objecten voor een bepaalde rol zoals hieronder weergegeven. De ID hier is de functie-instelling ID die kan worden opgehaald uit het resultaat van de lijst role instellingen cmdlet.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste azure-roltoewijzing verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste azure-functietoewijzing configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
