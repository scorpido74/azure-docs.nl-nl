---
title: Gevoeligheidslabels toewijzen aan groepen - Azure AD | Microsoft Documenten
description: Lidmaatschapsregels maken om groepen automatisch te vullen en een regelverwijzing.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329729"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Gevoeligheidslabels toewijzen aan Office 365-groepen in Azure Active Directory (voorbeeld)

Azure Active Directory (Azure AD) ondersteunt het toepassen van gevoeligheidslabels die zijn gepubliceerd door het [Microsoft 365-compliancecenter](https://sip.protection.office.com/homepage) op Office 365-groepen. Gevoeligheidslabels zijn van toepassing op groepen in services zoals Outlook, Microsoft Teams en SharePoint. Deze functie is momenteel beschikbaar als openbare preview-versie. Zie [Ondersteuning voor Office 365 voor gevoeligheidslabels voor](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)meer informatie over ondersteuning voor Office 365-apps.

> [!IMPORTANT]
> Om deze functie te configureren, moet er ten minste één actieve Azure Active Directory Premium P1-licentie in uw Azure AD-organisatie zijn.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Ondersteuning voor gevoeligheidslabel inschakelen in PowerShell

Als u gepubliceerde labels wilt toepassen op groepen, moet u eerst de functie inschakelen. Met deze stappen wordt de functie in Azure AD ingeschakeld.

1. Open een Windows PowerShell-venster op uw computer. Je het openen zonder verhoogde privileges.
1. Voer de volgende opdrachten uit als voorbereiding op het uitvoeren van de cmdlets.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Voer **op** de pagina Aanmelden bij uw account uw beheerdersaccount en wachtwoord in om u met uw service te verbinden en selecteer **Aanmelden.**
1. Haal de huidige groepsinstellingen voor de Azure AD-organisatie.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Als er geen groepsinstellingen zijn gemaakt voor deze Azure AD-organisatie, moet u eerst de instellingen maken. Volg de stappen in [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) om groepsinstellingen voor deze Azure AD-organisatie te maken.

1. Geef vervolgens de huidige groepsinstellingen weer.

    ```PowerShell
    $Setting.Values
    ```

1. Schakel vervolgens de functie in:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Sla vervolgens de wijzigingen op en pas de instellingen toe:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Dat is alles. U hebt de functie ingeschakeld en u gepubliceerde labels toepassen op groepen.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Een label toewijzen aan een nieuwe groep in Azure-portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com).
1. Selecteer **Groepen**en selecteer Vervolgens **Nieuwe groep**.
1. Selecteer op de pagina **Nieuwe groep** De optie **Office 365**en vul vervolgens de vereiste informatie voor de nieuwe groep in en selecteer een gevoeligheidslabel in de lijst.

   ![Een gevoeligheidslabel toewijzen op de pagina Nieuwe groepen](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Sla uw wijzigingen op en selecteer **Maken**.

Uw groep wordt gemaakt en de site- en groepsinstellingen die aan het geselecteerde label zijn gekoppeld, worden vervolgens automatisch afgedwongen.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Een label toewijzen aan een bestaande groep in Azure-portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een groepsbeheeraccount of als groepseigenaar.
1. Selecteer **Groepen**.
1. Selecteer **op** de pagina Alle groepen de groep die u wilt labelen.
1. Selecteer **eigenschappen** op de pagina van de geselecteerde groep en selecteer een gevoeligheidslabel in de lijst.

   ![Een gevoeligheidslabel toewijzen op de overzichtspagina voor een groep](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Selecteer **Opslaan** om uw wijzigingen op te slaan.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Een label verwijderen uit een bestaande groep in Azure-portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een globale beheerder- of groepsbeheerdersaccount of als groepseigenaar.
1. Selecteer **Groepen**.
1. Selecteer **op** de pagina Alle groepen de groep waaruit u het label wilt verwijderen.
1. Selecteer **eigenschappen**op de pagina **Groeperen** .
1. Selecteer **Verwijderen**.
1. Klik op **Opslaan** om uw wijzigingen toe te passen.

## <a name="using-classic-azure-ad-classifications"></a>Klassieke Azure AD-classificaties gebruiken

Nadat u deze functie hebt ingeschakeld, worden de 'klassieke' classificaties voor groepen alleen bestaande groepen en sites weergegeven en moet u deze alleen voor nieuwe groepen gebruiken als u groepen maakt in apps die geen gevoeligheidslabels ondersteunen. Uw beheerder kan ze later converteren naar gevoeligheidslabels indien nodig. Klassieke classificaties zijn de oude classificaties die u `ClassificationList` instelt door waarden voor de instelling in Azure AD PowerShell te definiëren. Wanneer deze functie is ingeschakeld, worden deze classificaties niet toegepast op groepen.

## <a name="troubleshooting-issues"></a>Problemen oplossen

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Gevoeligheidslabels zijn niet beschikbaar voor toewijzing op een groep

De optie gevoeligheidslabel wordt alleen weergegeven voor groepen wanneer aan alle volgende voorwaarden is voldaan:

1. Labels worden gepubliceerd in het Microsoft 365 Compliance Center voor deze tenant.
1. De functie is ingeschakeld, EnableMIPLabels is ingesteld op True in PowerShell.
1. De groep is een Office 365-groep.
1. De tenant heeft een actieve Azure Active Directory Premium P1-licentie.
1. De huidige aangemelde gebruiker heeft voldoende bevoegdheden om labels toe te wijzen. De gebruiker moet een globale beheerder, groepsbeheerder of groepseigenaar zijn.

Zorg ervoor dat aan alle voorwaarden is voldaan om labels aan een groep toe te wijzen.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Het label dat ik wil toewijzen, staat niet in de lijst

Als het label dat u zoekt niet in de lijst staat, kan dit om een van de volgende redenen het geval zijn:

- Het label wordt mogelijk niet gepubliceerd in het Microsoft 365 Compliance Center. Dit kan ook gelden voor labels die niet meer worden gepubliceerd. Neem contact op met uw beheerder voor meer informatie.
- Het label kan worden gepubliceerd, maar het is niet beschikbaar voor de gebruiker die is aangemeld. Neem contact op met uw beheerder voor meer informatie over hoe u toegang krijgt tot het label.

### <a name="how-to-change-the-label-on-a-group"></a>Het label op een groep wijzigen

Labels kunnen op elk gewenst moment worden verwisseld met dezelfde stappen als het toewijzen van een label aan een bestaande groep, als volgt:

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een algemeen of groepsbeheerdersaccount of als groepseigenaar.
1. Selecteer **Groepen**.
1. Selecteer **op** de pagina Alle groepen de groep die u wilt labelen.
1. Selecteer **eigenschappen** op de pagina van de geselecteerde groep en selecteer een nieuw gevoeligheidslabel in de lijst.
1. Selecteer **Opslaan**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Wijzigingen in groepsinstellingen in gepubliceerde labels worden niet bijgewerkt op de groepen

Als aanbevolen praktijk raden we u niet aan groepsinstellingen voor een label te wijzigen nadat het label op groepen is toegepast. Wanneer u wijzigingen aanbrengt in groepsinstellingen die zijn gekoppeld aan gepubliceerde labels in [het Microsoft 365-compliancecentrum,](https://sip.protection.office.com/homepage)worden deze beleidswijzigingen niet automatisch toegepast op de getroffen groepen.

Als u een wijziging moet aanbrengen, gebruikt u een [Azure AD PowerShell-script](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) om handmatig updates toe te passen op de getroffen groepen. Deze methode zorgt ervoor dat alle bestaande groepen de nieuwe instelling afdwingen.

## <a name="next-steps"></a>Volgende stappen

- [Gevoeligheidslabels gebruiken met Microsoft Teams, Office 365-groepen en SharePoint-sites](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Groepen bijwerken nadat het labelbeleid handmatig is gewijzigd met Azure AD PowerShell-script](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Instellingen van uw groep bewerken](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Groepen met behulp van PowerShell-opdrachten beheren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
