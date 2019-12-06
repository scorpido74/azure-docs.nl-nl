---
title: Referentie-assets in Azure Automation
description: Referentie-assets in Azure Automation bevatten beveiligings referenties die kunnen worden gebruikt voor de verificatie van bronnen die toegankelijk zijn voor de runbook-of DSC-configuratie. In dit artikel wordt beschreven hoe u referentie-assets maakt en hoe u deze kunt gebruiken in een runbook-of DSC-configuratie.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 582645919825c308fce4fe3211fa601955aaf37d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850173"
---
# <a name="credential-assets-in-azure-automation"></a>Referentie-assets in Azure Automation

Een Automation-referentie-Asset bevat een object dat beveiligings referenties bevat, zoals een gebruikers naam en wacht woord. Runbooks en DSC-configuraties kunnen gebruikmaken van cmdlets die een PSCredential-object voor authenticatie accepteren, of ze kunnen de gebruikers naam en het wacht woord van het PSCredential-object extra heren om te voorzien in een toepassing of service waarvoor authenticatie is vereist. De eigenschappen voor een referentie worden veilig opgeslagen in Azure Automation en kunnen worden geopend in het runbook of de DSC-configuratie met de activiteit [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Deze sleutel wordt opgeslagen in Key Vault. Voordat u een beveiligde Asset opslaat, wordt de sleutel geladen uit Key Vault en vervolgens gebruikt om de Asset te versleutelen.

## <a name="azure-classic-powershell-cmdlets"></a>Klassieke Azure Power shell-cmdlets

De cmdlets in de volgende tabel worden gebruikt voor het maken en beheren van automatiserings referentie-assets met Windows Power shell.  Ze worden geleverd als onderdeel van de [module Azure PowerShell](/powershell/azure/overview), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

| Cmdlets | Beschrijving |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Hiermee wordt informatie over een referentie-element opgehaald. U kunt de referentie zelf alleen ophalen uit de **Get-AutomationPSCredential-** activiteit. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Hiermee maakt u een nieuwe Automation-referentie. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Hiermee verwijdert u een Automation-referentie. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Hiermee stelt u de eigenschappen voor een bestaande Automation-referentie. |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM Power shell-cmdlets

De cmdlets in de volgende tabel worden gebruikt voor AzureRM om Automation-referentie assets te maken en te beheren met Windows Power shell.  Ze worden geleverd als onderdeel van de [AzureRM. Automation-module](/powershell/azure/overview), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

| Cmdlets | Beschrijving |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Hiermee wordt informatie over een referentie-element opgehaald. Hiermee wordt geen PSCredential-object geretourneerd.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Hiermee maakt u een nieuwe Automation-referentie. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Hiermee verwijdert u een Automation-referentie. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Hiermee stelt u de eigenschappen voor een bestaande Automation-referentie. |

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt voor toegang tot referenties in een runbook-en DSC-configuraties.

| Activiteiten | Beschrijving |
|:--- |:--- |
| Get-AutomationPSCredential |Hiermee haalt u een referentie op die moet worden gebruikt in een runbook of DSC-configuratie. Retourneert een [System. Management. Automation. PSCredential](/dotnet/api/system.management.automation.pscredential) -object. |

> [!NOTE]
> Vermijd het gebruik van variabelen in de para meter – name van Get-AutomationPSCredential, omdat dit het detecteren van afhankelijkheden tussen runbooks of DSC-configuraties en referentie assets tijdens de ontwerp fase kan bemoeilijken.

## <a name="python2-functions"></a>Python2-functies

De functie in de volgende tabel wordt gebruikt voor toegang tot referenties in een Python2-runbook.

| Functie | Beschrijving |
|:---|:---|
| automationassets.get_automation_credential | Hiermee wordt informatie over een referentie-element opgehaald. |

> [!NOTE]
> U moet de module ' automationassets ' boven aan uw python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-credential-asset"></a>Nieuwe referentie-Asset maken

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Een nieuw referentie-element maken met de Azure Portal

1. Selecteer in uw Automation-account **referenties** onder **gedeelde resources**.
1. Klik op **+ een referentie toevoegen**.
1. Vul het formulier in en klik op **maken** om de nieuwe referentie op te slaan.

> [!NOTE]
> Gebruikers accounts die gebruikmaken van multi-factor Authentication, worden niet ondersteund voor gebruik in Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Een nieuw referentie-element maken met Windows Power shell

De volgende voorbeeld opdrachten laten zien hoe u een nieuwe automatiserings referentie maakt. Er wordt eerst een PSCredential-object gemaakt met de naam en het wacht woord en vervolgens gebruikt om de referentie-Asset te maken. U kunt ook de cmdlet **Get-Credential** gebruiken om een naam en wacht woord in te voeren.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Een Power shell-referentie gebruiken

U haalt een referentie-element op in een runbook of DSC-configuratie met de activiteit **Get-AutomationPSCredential** . Hiermee wordt een [PSCredential-object](/dotnet/api/system.management.automation.pscredential) geretourneerd dat u met een activiteit of cmdlet kunt gebruiken waarvoor een PSCredential-para meter is vereist. U kunt ook de eigenschappen van het referentie object ophalen om het afzonderlijk te gebruiken. Het object heeft een eigenschap voor de gebruikers naam en het beveiligde wacht woord, of u kunt de methode **GetNetworkCredential** gebruiken om een [NetworkCredential](/dotnet/api/system.net.networkcredential) -object te retour neren dat een niet-beveiligde versie van het wacht woord geeft.

> [!NOTE]
> **Get-AzureRmAutomationCredential** retourneert geen **PSCredential** die voor verificatie kan worden gebruikt. Het bevat alleen informatie over de referentie. Als u een referentie in een runbook moet gebruiken, moet u de **Get-AutomationPSCredential** gebruiken om het object **PSCredential** op te halen.

### <a name="textual-runbook-sample"></a>Voor beeld van tekst-runbook

De volgende voorbeeldopdrachten laten zien hoe u een PowerShell-referentie gebruiken in een runbook. In dit voor beeld wordt de referentie opgehaald en de gebruikers naam en het wacht woord die aan de variabelen zijn toegewezen.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

U kunt ook een referentie gebruiken om te verifiëren bij Azure met [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). In de meeste gevallen moet u een [uitvoeren als-account](../manage-runas-account.md) gebruiken en dit ophalen met [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Voor beeld van grafisch runbook

U voegt een **Get-AutomationPSCredential-** activiteit toe aan een grafisch runbook door met de rechter muisknop op de referentie in het deel venster Bibliotheek van de grafische editor te klikken en **toevoegen aan canvas**te selecteren.

![Referentie toevoegen aan canvas](../media/credentials/credential-add-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een referentie in een grafisch runbook.  In dit geval wordt deze gebruikt om verificatie voor een runbook te bieden aan Azure-resources, zoals beschreven in [Runbooks verifiëren met Azure AD-gebruikers account](../automation-create-aduser-account.md).  Met de eerste activiteit wordt de referentie opgehaald die toegang heeft tot het Azure-abonnement.  De activiteit **add-AzureAccount** gebruikt vervolgens deze referentie om verificatie uit te voeren voor alle activiteiten die zich daarna voordoen.  Een [pijplijn koppeling](../automation-graphical-authoring-intro.md#links-and-workflow) is hier omdat **Get-AutomationPSCredential** een enkel object verwacht.  

![Referentie toevoegen aan canvas](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Een Power shell-referentie gebruiken in DSC

Hoewel DSC-configuraties in Azure Automation kunnen verwijzen naar referentie-assets met **Get-AutomationPSCredential**, kunnen referentie-assets ook worden door gegeven via para meters, indien gewenst. Zie [configuraties compileren in azure Automation DSC](../automation-dsc-compile.md#credential-assets)voor meer informatie.

## <a name="using-credentials-in-python2"></a>Referenties gebruiken in Python2

In het volgende voor beeld ziet u een voor beeld van toegang tot referenties in Python2 runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow) voor meer informatie over koppelingen in grafische ontwerpen
* Zie [Azure Automation Security](../automation-security-overview.md) (Engelstalig) voor meer informatie over de verschillende verificatie methoden met Automation.
* Zie [Mijn eerste grafische runbook](../automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Mijn eerste PowerShell Workflow-runbook](../automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
* Als u aan de slag wilt gaan met Python2 runbooks, raadpleegt u [mijn eerste Python2-runbook](../automation-first-runbook-textual-python2.md) 
