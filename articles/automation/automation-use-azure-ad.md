---
title: Azure AD gebruiken in Azure Automation om te verifiëren naar Azure
description: Meer informatie over het gebruik van Azure AD binnen Azure Automation als leverancier voor verificatie naar Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548344"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Azure AD gebruiken in Azure Automation om te verifiëren naar Azure

De [AD-service (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) maakt een aantal administratieve taken mogelijk, zoals gebruikersbeheer, domeinbeheer en eenmalige aanmeldingsconfiguratie. In dit artikel wordt beschreven hoe u Azure AD binnen Azure Automation gebruikt als leverancier voor verificatie naar Azure. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="installing-azure-ad-modules"></a>Azure AD-modules installeren

U Azure AD inschakelen via de volgende PowerShell-modules:

* Azure Active Directory PowerShell voor Grafiek (AzureRM- en Az-modules). Azure Automation wordt geleverd met de AzureRM-module en de recente upgrade, de Az-module. Functionaliteit omvat niet-interactieve verificatie naar Azure met behulp van Azure AD-gebruikersverificatie (OrgId) op basis van referenties. Zie [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory voor Windows PowerShell (MSOnline-module). Deze module maakt interacties met Microsoft Online mogelijk, waaronder Office 365.

>[!NOTE]
>PowerShell Core ondersteunt de MSOnline-module niet. Als u de modulecmdlets wilt gebruiken, moet u ze uitvoeren vanuit Windows PowerShell. U wordt aangemoedigd om de nieuwere Azure Active Directory PowerShell te gebruiken voor Grafiekmodules in plaats van de MSOnline-module. 

### <a name="preinstallation"></a>Voorinstallatie

Voordat u de Azure AD-modules op uw computer installeert:

* Verwijder eerdere versies van de AzureRM/Az-module en de MSOnline-module. 

* Verwijder de Aanmeldingsassistent van Microsoft Online Services om de nieuwe PowerShell-modules correct te bedienen.  

### <a name="install-the-azurerm-and-az-modules"></a>De AzureRM- en Az-modules installeren

>[!NOTE]
>Als u met deze modules wilt werken, moet u PowerShell-versie 5.1 of hoger gebruiken met een 64-bits versie van Windows. 

1. Installeer Windows Management Framework (WMF) 5.1. Zie [WMF 5.1 installeren en configureren](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Installeer AzureRM en/of Az met instructies in [Azure PowerShell installeren op Windows met PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>De MSOnline-module installeren

>[!NOTE]
>Als u de MSOnline-module wilt installeren, moet u lid zijn van een Office 365-beheerrol. Zie [Over beheerdersrollen](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Controleer of de functie Microsoft .NET Framework 3.5.x is ingeschakeld op uw computer. Het is waarschijnlijk dat uw computer een nieuwere versie heeft geïnstalleerd, maar achterwaartse compatibiliteit met oudere versies van het .NET Framework kan worden ingeschakeld of uitgeschakeld. 

2. Installeer de 64-bits versie van de [Aanmeldingsassistent](https://www.microsoft.com/download/details.aspx?id=41950)van Microsoft Online Services .

3. Voer Windows PowerShell uit als beheerder om een verhoogde Windows PowerShell-opdrachtprompt te maken.

4. Azure Active Directory implementeren vanaf [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Als u wordt gevraagd de NuGet-provider te installeren, typt u Y en drukt u op ENTER.

6. Als u wordt gevraagd de module vanuit [PSGallery](https://www.powershellgallery.com/)te installeren, typt u Y en drukt u op ENTER.

### <a name="install-support-for-pscredential"></a>Ondersteuning voor PSCredential installeren

Azure Automation gebruikt de klasse [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) om een referentie-asset weer te geven. Uw scripts `PSCredential` halen `Get-AutomationPSCredential` objecten op met de cmdlet. Zie [Credential assets in Azure Automation](shared-resources/credentials.md)voor meer informatie.

## <a name="assigning-a-subscription-administrator"></a>Een abonnementsbeheerder toewijzen

U moet een beheerder toewijzen voor het Azure-abonnement. Deze persoon heeft de rol van eigenaar voor het abonnementsbereik. Zie [Op rollen gebaseerdtoegangsbeheer in Azure Automation](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Het wachtwoord van de Azure AD-gebruiker wijzigen

Ga als ander over het wachtwoord van de Azure AD-gebruiker:

1. Afmelden bij Azure.

2. Laat de beheerder zich aanmelden bij Azure als de Azure AD-gebruiker die zojuist is gemaakt, met de volledige gebruikersnaam (inclusief het domein) en een tijdelijk wachtwoord. 

3. Vraag de beheerder om het wachtwoord te wijzigen wanneer daarom wordt gevraagd.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Azure Automation configureren om de Azure AD-gebruiker te gebruiken om het Azure-abonnement te beheren

Als Azure Automation wilt communiceren met Azure AD, moet u de referenties ophalen die zijn gekoppeld aan de Azure-verbinding met Azure AD. Voorbeelden van deze referenties zijn tenant-ID, abonnements-ID en dergelijke. Zie Uw organisatie verbinden met Azure [Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)voor meer informatie over de verbinding tussen Azure en Azure AD.

## <a name="creating-a-credential-asset"></a>Een referentieactief maken

Nu de Azure-referenties voor Azure AD beschikbaar zijn, is het tijd om een Azure Automation-referentieasset te maken om de Azure AD-referenties veilig op te slaan, zodat runbooks en Desire State Configuration (DSC)-scripts er toegang toe hebben. U dit doen met de Azure-portal of PowerShell-cmdlets.

### <a name="create-the-credential-asset-in-azure-portal"></a>De referentieasset maken in Azure-portal

U de Azure-portal gebruiken om het referentie-item te maken. Doe deze bewerking vanuit uw Automatiseringsaccount met **referenties** onder **Gedeelde resources**. Zie [Referentie-elementen in Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Het referentie-element maken met Windows PowerShell

Als u een nieuw referentieactief in Windows PowerShell wilt voorbereiden, maakt uw script eerst een `PSCredential` object met de toegewezen gebruikersnaam en wachtwoord. Het script gebruikt dit object vervolgens om het item te maken via een aanroep naar de cmdlet [Nieuw-AzureAutomationCredential.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Als alternatief kan het script de cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) aanroepen om de gebruiker te vragen een naam en wachtwoord in te typen. Zie [Referentie-elementen in Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Azure-resources beheren vanuit een Azure Automation-runbook

U Azure-resources beheren vanuit Azure Automation-runbooks met behulp van de referentieasset. Hieronder vindt u een voorbeeld van PowerShell-runbook dat het referentie-item verzamelt dat moet worden gebruikt voor het stoppen en starten van virtuele machines in een Azure-abonnement. Deze runbook `Get-AutomationPSCredential` wordt voor het eerst gebruikt om de referentie op te halen die moet worden gebruikt om te verifiëren naar Azure. Vervolgens wordt de [cmdlet Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) aangeroepen om verbinding te maken met Azure met behulp van de referentie. Het script gebruikt de cmdlet [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) om het abonnement te kiezen waarmee u wilt werken. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Volgende stappen

* U vindt informatie over automatiseringsreferenties in [Referentie-elementen in Azure Automation](shared-resources/credentials.md).
* Zie [Modules beheren in Azure Automation](shared-resources/modules.md) om erachter te komen hoe u met automatiseringsmodules werken.
* Zie [Een runbook starten in Azure Automation](automation-starting-a-runbook.md)voor meer informatie over de methoden die kunnen worden gebruikt om een runbook te starten in Azure Automation.
* Zie de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over PowerShell, inclusief taalverwijzing en leermodules.