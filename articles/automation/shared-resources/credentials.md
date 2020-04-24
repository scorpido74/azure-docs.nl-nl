---
title: Referenties beheren in Azure Automation
description: Referentie-assets in Azure Automation bevatten beveiligings referenties die kunnen worden gebruikt voor de verificatie van bronnen die toegankelijk zijn voor de runbook-of DSC-configuratie. In dit artikel wordt beschreven hoe u referentie-assets maakt en hoe u deze kunt gebruiken in een runbook-of DSC-configuratie.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 42abeba310e7a30364f93e998f12129a2d3c1f15
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114289"
---
# <a name="manage-credentials-in-azure-automation"></a>Referenties beheren in Azure Automation

Een Automation-referentie-Asset bevat een object dat beveiligings referenties bevat, zoals een gebruikers naam en een wacht woord. Runbooks en DSC-configuraties gebruiken cmdlets waarmee een [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) -object voor verificatie wordt geaccepteerd. Het is ook mogelijk om de gebruikers naam en het wacht woord van `PSCredential` het object op te halen om aan te geven aan bepaalde toepassingen of services waarvoor verificatie is vereist. 

> [!NOTE]
> Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Deze sleutel wordt opgeslagen in Key Vault. Voordat u een beveiligde Asset opslaat, wordt de sleutel geladen uit Key Vault en vervolgens gebruikt om de Asset te versleutelen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell AZ-cmdlets die worden gebruikt voor referentie-assets

Als onderdeel van de Azure PowerShell AZ-module worden de cmdlets in de volgende tabel gebruikt om Automation-referentie assets te maken en te beheren met Windows Power shell. Ze worden verzonden in de [module AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties. Zie [ondersteuning voor de module AZ in azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Beschrijving |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hiermee wordt een [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) -object opgehaald dat meta gegevens bevat over de referentie. De cmdlet haalt het `PSCredential` object zelf niet op.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Hiermee maakt u een nieuwe Automation-referentie. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Hiermee verwijdert u een Automation-referentie. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Hiermee stelt u de eigenschappen voor een bestaande Automation-referentie. |

## <a name="activities-used-to-access-credentials"></a>Activiteiten die worden gebruikt voor toegang tot referenties

De activiteiten in de volgende tabel worden gebruikt voor toegang tot referenties in runbooks en DSC-configuraties.

| Activiteit | Beschrijving |
|:--- |:--- |
| `Get-AutomationPSCredential` |Hiermee wordt `PSCredential` een object opgehaald dat in een runbook-of DSC-configuratie kan worden gebruikt. Meestal moet u deze activiteit gebruiken in plaats van de `Get-AzAutomationCredential` cmdlet, aangezien de laatste alleen referentie gegevens ophaalt. Deze informatie is doorgaans niet nuttig voor het door geven van een andere cmdlet. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Hiermee wordt een referentie opgehaald met de vraag naar een gebruikers naam en wacht woord. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Hiermee maakt u een referentie-element. |

Als u `PSCredential` objecten in uw code wilt ophalen, moet u `Orchestrator.AssetManagement.Cmdlets` de module importeren. Zie [modules beheren in azure Automation](modules.md)voor meer informatie.

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Vermijd het gebruik van variabelen in de `Name` para meter `Get-AutomationPSCredential`van. Hun gebruik kan de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en referentie-assets tijdens het ontwerp bemoeilijken.

## <a name="python-2-functions-that-access-credentials"></a>Python 2-functies die toegang hebben tot referenties

De functie in de volgende tabel wordt gebruikt voor toegang tot referenties in een python 2-runbook.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_credential` | Hiermee wordt informatie over een referentie-element opgehaald. |

> [!NOTE]
> Importeer de `automationassets` module boven aan het python-runbook om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-credential-asset"></a>Nieuwe referentie-Asset maken

U kunt een nieuw referentie-element maken met behulp van de Azure Portal of met behulp van Windows Power shell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Maak een nieuw referentie-element met de Azure Portal

1. Selecteer in uw Automation-account **referenties** onder **gedeelde resources**.
1. Selecteer **een referentie toevoegen**.
2. Voer in het deel venster nieuwe referentie de juiste referentie naam in volgens uw naamgevings standaarden. 
3. Typ uw toegangs-ID in het veld **gebruikers naam** . 
4. Voor beide wachtwoord velden voert u uw geheime toegangs sleutel in.

    ![Nieuwe referentie maken](../media/credentials/credential-create.png)

5. Als het selectie vakje multi-factor Authentication is ingeschakeld, schakelt u dit uit. 
6. Klik op **maken** om het nieuwe referentie-element op te slaan.

> [!NOTE]
> Azure Automation biedt geen ondersteuning voor gebruikers accounts die gebruikmaken van multi-factor Authentication.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Een nieuw referentie-element maken met Windows Power shell

In het volgende voor beeld ziet u hoe u een nieuw Automation-referentie-element maakt. Er `PSCredential` wordt eerst een object gemaakt met de naam en het wacht woord en vervolgens gebruikt voor het maken van de referentie-Asset. In plaats daarvan kunt u de `Get-Credential` -cmdlet gebruiken om de gebruiker te vragen een naam en wacht woord in te voeren.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Een Power shell-referentie gebruiken

Een runbook-of DSC-configuratie haalt een referentie- `Get-AutomationPSCredential` element op met de activiteit. Met deze activiteit wordt `PSCredential` een object opgehaald dat u kunt gebruiken met een activiteit of cmdlet waarvoor een referentie is vereist. U kunt ook de eigenschappen van het referentie object ophalen om het afzonderlijk te gebruiken. Het object heeft eigenschappen voor de gebruikers naam en het beveiligde wacht woord. U kunt ook de methode [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) gebruiken om een [NetworkCredential](/dotnet/api/system.net.networkcredential) -object op te halen dat een niet-beveiligde versie vertegenwoordigt van het wacht woord.

> [!NOTE]
> `Get-AzAutomationCredential`haalt geen `PSCredential` object op dat voor verificatie kan worden gebruikt. Het bevat alleen informatie over de referentie. Als u een referentie in een runbook moet gebruiken, moet u deze als een `PSCredential` object ophalen met. `Get-AutomationPSCredential`

### <a name="textual-runbook-example"></a>Voor beeld van een tekst-runbook

In het volgende voor beeld ziet u hoe u een Power shell-referentie gebruikt in een runbook. De referentie wordt opgehaald en de gebruikers naam en het wacht woord worden toegewezen aan variabelen.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

U kunt ook een referentie gebruiken om te verifiëren bij Azure met [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). In de meeste gevallen moet u een [uitvoeren als-account](../manage-runas-account.md) gebruiken en de verbinding ophalen met [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Voor beeld van grafisch runbook

U kunt een `Get-AutomationPSCredential` activiteit aan een grafisch runbook toevoegen door met de rechter muisknop op de referentie in het deel venster Bibliotheek van de grafische editor te klikken en **toevoegen aan canvas**te selecteren.

![Referentie toevoegen aan canvas](../media/credentials/credential-add-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een referentie in een grafisch runbook. In dit geval biedt de referentie verificatie voor een runbook aan Azure-resources, zoals beschreven in [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](../automation-use-azure-ad.md). Met de eerste activiteit wordt de referentie opgehaald die toegang heeft tot het Azure-abonnement. De activiteit account verbinding gebruikt vervolgens deze referentie om verificatie uit te voeren voor alle activiteiten die zich daarna voordoen. Er wordt hier een [pijplijn koppeling](../automation-graphical-authoring-intro.md#links-and-workflow) gebruikt omdat `Get-AutomationPSCredential` deze een enkel object verwacht.  

![Referentie toevoegen aan canvas](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Referenties gebruiken in een DSC-configuratie

DSC-configuraties in Azure Automation kunnen werken met referentie-assets `Get-AutomationPSCredential`met, maar ze kunnen ook referentie-assets door geven via para meters. Zie [configuraties compileren in azure Automation DSC](../automation-dsc-compile.md#credential-assets)voor meer informatie.

## <a name="using-credentials-in-python-2"></a>Referenties gebruiken in Python 2

In het volgende voor beeld ziet u een voor beeld van toegang tot referenties in Python 2-runbooks.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)voor meer informatie over koppelingen in grafische ontwerpen.
* Zie [Azure Automation Security](../automation-security-overview.md)voor meer informatie over de verschillende verificatie methoden voor automatisering.
* Zie [mijn eerste grafische runbook](../automation-first-runbook-graphical.md)om aan de slag te gaan met grafische runbooks.
* Zie [mijn eerste Power shell workflow-runbook](../automation-first-runbook-textual.md)om aan de slag te gaan met Power shell workflow-runbooks.
* Zie [mijn eerste python 2-runbook](../automation-first-runbook-textual-python2.md)om aan de slag te gaan met python 2-runbooks. 
