---
title: Gevoeligheids labels toewijzen aan groepen-Azure AD | Microsoft Docs
description: Lidmaatschaps regels maken voor het automatisch invullen van groepen en een verwijzing naar een regel.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/19/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07859299805c5f7be869350adbdbfa675775888c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404812"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Gevoeligheids labels toewijzen aan Office 365-groepen in Azure Active Directory (preview)

Azure Active Directory (Azure AD) ondersteunt het Toep assen van gevoelige labels die worden gepubliceerd door het [Microsoft 365 compliance Center](https://sip.protection.office.com/homepage) naar Office 365-groepen. Gevoeligheids labels zijn van toepassing op groepen in verschillende services, zoals Outlook, micro soft teams en share point. Deze functie is momenteel beschikbaar als openbare preview-versie.

> [!IMPORTANT]
> Voor het gebruik van Azure AD sensitivity labels voor Office 365-groepen is een Azure Active Directory Premium P1-licentie vereist.

## <a name="group-settings-controlled-by-labels"></a>Groeps instellingen die worden beheerd door labels

Er zijn twee instellingen die kunnen worden gekoppeld aan een label:

- **Privacy**: beheerders kunnen een privacyinstelling koppelen aan het label om te bepalen of een groep openbaar of privé is.
- **Gast toegang**: beheerders kunnen het gast beleid afdwingen voor alle groepen waaraan het label is toegewezen. Dit beleid bepaalt of gasten kunnen worden toegevoegd als leden. Als het gast beleid is geconfigureerd voor een label, kan de instelling van de AllowToAddGuests niet worden gewijzigd door de groepen waaraan u het label toewijst.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Ondersteuning van gevoeligheids Labels inschakelen in Power shell

Als u gepubliceerde labels op groepen wilt Toep assen, moet u eerst de functie inschakelen. Met deze stappen wordt de functie in azure AD ingeschakeld.

1. Open een Windows Power shell-venster op de computer. U kunt deze openen zonder verhoogde bevoegdheden.
1. Voer de volgende opdrachten uit als voorbereiding op het uitvoeren van de cmdlets.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Voer op de pagina **Aanmelden bij uw account** uw beheerders account en wacht woord in om verbinding te maken met uw service en selecteer **Aanmelden**.
1. De huidige groeps instellingen voor de Azure AD-organisatie ophalen.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Als er geen groeps instellingen voor deze Azure AD-organisatie zijn gemaakt, moet u eerst de instellingen maken. Volg de stappen in [Azure Active Directory-cmdlets voor het configureren van groeps instellingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) voor het maken van groeps instellingen voor deze Azure AD-organisatie.

1. Vervolgens geeft u de instellingen van de huidige groep weer.

    ```PowerShell
    $Setting.Values
    ```

1. Schakel vervolgens de functie in:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Sla de wijzigingen op en pas de instellingen toe:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Dat is alles. U hebt de functie ingeschakeld en u kunt gepubliceerde labels Toep assen op groepen.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Een label toewijzen aan een nieuwe groep in Azure Portal

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com).
1. Selecteer **groepen**en selecteer vervolgens **nieuwe groep**.
1. Op de pagina **nieuwe groep** selecteert u **Office 365**, voert u de vereiste gegevens voor de nieuwe groep in en selecteert u een gevoeligheids label in de lijst.

   ![Een gevoeligheids label toewijzen op de pagina nieuwe groepen](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Sla de wijzigingen op en selecteer **maken**.

De groep wordt gemaakt en het beleid dat is gekoppeld aan het geselecteerde label wordt vervolgens automatisch afgedwongen.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Een label toewijzen aan een bestaande groep in Azure Portal

1. Meld u aan bij het [beheer centrum van Azure AD](https://aad.portal.azure.com) met een Administrator account voor globale beheerders of groepen, of als groeps eigenaar.
1. Selecteer **groepen**.
1. Selecteer op de pagina **alle groepen** de groep die u wilt labelen.
1. Selecteer op de pagina geselecteerde groep de optie **Eigenschappen** en selecteer een gevoeligheids label in de lijst.

   ![Een gevoeligheids label toewijzen op de overzichts pagina voor een groep](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Selecteer **Opslaan** om uw wijzigingen op te slaan.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Een label verwijderen uit een bestaande groep in Azure Portal

1. Meld u aan bij het [beheer centrum van Azure AD](https://aad.portal.azure.com) met een Administrator account voor globale beheerders of groepen, of als groeps eigenaar.
1. Selecteer **groepen**.
1. Selecteer op de pagina **alle groepen** de groep waarvan u het label wilt verwijderen.
1. Selecteer op de pagina **groep** **Eigenschappen**.
1. Selecteer **Verwijderen**.
1. Selecteer **Opslaan** om uw wijzigingen toe te passen.

## <a name="office-365-app-support-for-sensitivity-labels"></a>Office 365-app-ondersteuning voor gevoeligheids labels

De volgende Office 365-apps en-services ondersteunen de gevoeligheids labels in deze preview:

- Azure AD-beheer centrum
- Nalevings centrum Microsoft 365
- SharePoint
- Outlook op het web
- Teams
- Share point-beheer centrum

Zie voor meer informatie over de ondersteuning van Office 365-apps [office 365-ondersteuning voor gevoeligheids labels](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-new-sensitivity-labels).

## <a name="using-classic-azure-ad-classifications"></a>Klassieke Azure AD-classificaties gebruiken

Nadat u deze functie hebt ingeschakeld, ondersteunt Office 365 niet langer de ' klassieke ' classificaties voor nieuwe groepen. Klassieke classificaties zijn de oude classificaties die u instelt door waarden te definiëren voor de `ClassificationList` instelling in azure AD Power shell. Als deze functie is ingeschakeld, worden deze classificaties niet toegepast op groepen.

## <a name="troubleshooting-issues"></a>Problemen oplossen

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Er zijn geen gevoeligheids labels beschikbaar voor toewijzing voor een groep

De optie gevoeligheids label wordt alleen weer gegeven voor groepen wanneer aan de volgende voor waarden wordt voldaan:

1. Labels worden gepubliceerd in het Microsoft 365 compliance Center voor deze Tenant.
1. De functie is ingeschakeld, EnableMIPLabels is ingesteld op True in Power shell.
1. De groep is een Office 365-groep.
1. De Tenant heeft een actieve Azure Active Directory Premium P1-licentie.
1. De huidige aangemelde gebruiker heeft toegang tot gepubliceerde labels.
1. De huidige aangemelde gebruiker heeft voldoende bevoegdheden om labels toe te wijzen. De gebruiker moet een globale beheerder, groeps beheerder of de groeps eigenaar zijn.
1. Er is een Office 365-licentie toegewezen aan de huidige aangemelde gebruiker. Zie voor meer informatie over licentie vereisten [gevoeligheid-labels in Office-apps](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-office-apps).

Controleer of aan alle voor waarden wordt voldaan om labels toe te wijzen aan een groep.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Het label dat ik wil toewijzen, staat niet in de lijst

Als het label dat u zoekt, zich niet in de lijst bevindt, kan dit een van de volgende oorzaken hebben:

- Het label wordt mogelijk niet gepubliceerd in het Microsoft 365 compliance Center. Dit kan ook worden toegepast op labels die niet meer worden gepubliceerd. Neem contact op met de beheerder voor meer informatie.
- Het label kan echter wel worden gepubliceerd, maar het is niet beschikbaar voor de gebruiker die is aangemeld. Neem contact op met de beheerder voor meer informatie over het verkrijgen van toegang tot het label.

### <a name="how-can-i-change-the-label-on-a-group"></a>Hoe kan ik het label in een groep wijzigen?

Labels kunnen op elk gewenst moment worden gewisseld met behulp van dezelfde stappen als wanneer u een label aan een bestaande groep toewijst:

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een globaal of groeps beheerders account of als groeps eigenaar.
1. Selecteer **groepen**.
1. Selecteer op de pagina **alle groepen** de groep die u wilt labelen.
1. Op de pagina geselecteerde groep selecteert u **Eigenschappen** en selecteert u een nieuw gevoeligheids label in de lijst.
1. Selecteer **Opslaan**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Wijzigingen in de groeps instelling voor gepubliceerde labels worden niet bijgewerkt op de groepen

Als best practice wordt niet aangeraden om de groeps instellingen voor een label te wijzigen nadat het label is toegepast op groepen. Wanneer u wijzigingen aanbrengt in de groeps instellingen die zijn gekoppeld aan gepubliceerde labels in [Microsoft 365 compliance Center](https://sip.protection.office.com/homepage), worden deze beleids wijzigingen niet automatisch toegepast op de betrokken groepen.

Als u een wijziging wilt aanbrengen, moet u een [Azure AD Power shell-script](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) gebruiken om updates hand matig toe te passen op de betrokken groepen. Deze methode zorgt ervoor dat alle bestaande groepen de nieuwe instelling afdwingen.

## <a name="next-steps"></a>Volgende stappen

- [Gevoeligheids labels gebruiken met micro soft teams, Office 365-groepen en share point-sites](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Groeps beleidsobjecten hand matig bijwerken met Azure AD Power shell-script](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Instellingen van uw groep bewerken](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Groepen met behulp van PowerShell-opdrachten beheren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
