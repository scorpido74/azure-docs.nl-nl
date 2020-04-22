---
title: Referenties beheren in Azure Automation
description: Referentie-elementen in Azure Automation bevatten beveiligingsreferenties die kunnen worden gebruikt om te verifiëren voor resources die toegankelijk zijn voor de runbook- of DSC-configuratie. In dit artikel wordt beschreven hoe u referentie-elementen maakt en deze gebruikt in een runbook- of DSC-configuratie.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59e32087d4489cbb155a9cff7d40094c0606c0cf
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732849"
---
# <a name="manage-credentials-in-azure-automation"></a>Referenties beheren in Azure Automation

Een item als referentie-item voor automatisering bevat een object dat beveiligingsreferenties bevat, zoals een gebruikersnaam en een wachtwoord. Runbooks en DSC-configuraties maken gebruik van cmdlets die een [PSCredential-object](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) accepteren voor verificatie. Als alternatief kunnen ze de gebruikersnaam en `PSCredential` het wachtwoord van het object extraheren om te bieden aan een bepaalde toepassing of service waarvoor verificatie vereist is. 

> [!NOTE]
> Beveiligde elementen in Azure Automation omvatten referenties, certificaten, verbindingen en versleutelde variabelen. Deze elementen worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automatiseringsaccount. Deze sleutel wordt opgeslagen in Key Vault. Voordat u een beveiligde asset opslaat, wordt de sleutel geladen vanuit Key Vault en vervolgens gebruikt om het item te versleutelen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell Az-cmdlets die worden gebruikt voor referentie-elementen

Als onderdeel van de Azure PowerShell Az-module worden de cmdlets in de volgende tabel gebruikt om automatiseringsreferentie-elementen te maken en te beheren met Windows PowerShell. Ze verzenden in de [Az.Automation-module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), die beschikbaar is voor gebruik in automation runbooks en DSC-configuraties. Zie [ondersteuning voor AZ-modules in Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Beschrijving |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hiermee haalt u informatie op over een referentieactief. Deze cmdlet geeft geen `PSCredential` object terug.  |
| [Nieuw-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Hiermee maakt u een nieuwe automatiseringsreferentie. |
| [Credential remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Hiermee verwijdert u een automatiseringsreferentie. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Hiermee stelt u de eigenschappen in voor een bestaande automatiseringsreferentie. |

## <a name="activities-used-to-access-credentials"></a>Activiteiten die worden gebruikt om toegang te krijgen tot referenties

De activiteiten in de volgende tabel worden gebruikt om toegang te krijgen tot referenties in runbooks en DSC-configuraties.

| Activiteit | Beschrijving |
|:--- |:--- |
| `Get-AutomationPSCredential` |Hier wordt een referentie gebruikt in een runbook- of DSC-configuratie. De referentie is in `PSCredential` de vorm van een object. Zie [Module-elementen in Azure Automation](modules.md)voor meer informatie over de cmdlet die overeenkomt met deze activiteit. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Krijgt een referentie met een prompt voor gebruikersnaam en wachtwoord. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Hiermee maakt u een referentie-asset. |

Als `PSCredential` u objecten in uw code wilt ophalen, u de Microsoft Azure Automation ISE-invoegtoepassing voor de PowerShell ISE installeren. Zie [Module-elementen in Azure Automation voor](modules.md)meer informatie.

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Uw script kan ook de vereiste module importeren waar nodig, zoals in het volgende voorbeeld: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> U moet het gebruik `Name` van `Get-AutomationPSCredential`variabelen in de parameter van . Het gebruik ervan kan het ontdekken van afhankelijkheden tussen runbooks of DSC-configuraties en referentie-elementen tijdens het ontwerp bemoeilijkt.

## <a name="python-2-functions-that-access-credentials"></a>Python 2-functies die toegang hebben tot referenties

De functie in de volgende tabel wordt gebruikt om toegang te krijgen tot referenties in een Python 2-runbook.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_credential` | Hiermee haalt u informatie op over een referentieactief. |

> [!NOTE]
> Importeer `automationassets` de module boven aan uw Python-runbook om toegang te krijgen tot de assetfuncties.

## <a name="creating-a-new-credential-asset"></a>Een nieuw referentieactief maken

U een nieuw referentieactief maken met behulp van de Azure-portal of met Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Een nieuw referentieactief maken met de Azure-portal

1. Selecteer **referenties** onder **Gedeelde bronnen**in uw automatiseringsaccount.
1. Selecteer **Een referentie toevoegen**.
2. Voer in het deelvenster Nieuwe referenties een geschikte referentienaam in volgens uw naamgevingsstandaarden. 
3. Typ uw toegangs-id in het veld **Gebruikersnaam.** 
4. Voer voor beide wachtwoordvelden uw geheime toegangssleutel in.

    ![Nieuwe referenties maken](../media/credentials/credential-create.png)

5. Als het vak met meervoudige verificatie is ingeschakeld, schakelt u het selectievakje uit. 
6. Klik **op Maken** om het nieuwe referentie-item op te slaan.

> [!NOTE]
> Azure Automation biedt geen ondersteuning voor gebruikersaccounts die multi-factor authenticatie gebruiken.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Een nieuw referentieactief maken met Windows PowerShell

In het volgende voorbeeld ziet u hoe u een nieuw automatiseringsreferentieactief maakt. Een `PSCredential` object wordt eerst gemaakt met de naam en het wachtwoord en vervolgens gebruikt om het referentie-element te maken. In plaats daarvan `Get-Credential` kunt u de cmdlet gebruiken om de gebruiker te vragen een naam en wachtwoord in te typen.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Een PowerShell-referentie gebruiken

Een runbook- of DSC-configuratie haalt `Get-AutomationPSCredential` een referentie-element op met de activiteit. Met deze activiteit `PSCredential` wordt een object opgehaald dat u gebruiken met een activiteit of cmdlet waarvoor een referentie vereist is. U ook de eigenschappen ophalen van het referentieobject dat u afzonderlijk wilt gebruiken. Het object heeft eigenschappen voor de gebruikersnaam en het beveiligde wachtwoord. U ook de [getNetworkCredential-methode](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) gebruiken om een [NetworkCredential-object](/dotnet/api/system.net.networkcredential) op te halen dat een onbeveiligde versie van het wachtwoord vertegenwoordigt.

> [!NOTE]
> `Get-AzAutomationCredential`haalt geen `PSCredential` object op dat kan worden gebruikt voor verificatie. Het biedt alleen informatie over de referentie. Als u een referentie in een runbook moet gebruiken, moet u deze ophalen als object `PSCredential` met behulp van `Get-AutomationPSCredential`.

### <a name="textual-runbook-example"></a>Voorbeeld van tekstuele runbook

In het volgende voorbeeld ziet u hoe u een PowerShell-referentie in een runbook gebruikt. Het haalt de referentie en wijst de gebruikersnaam en het wachtwoord toe aan variabelen.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

U ook een referentie gebruiken om te verifiëren aan Azure met [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Onder de meeste omstandigheden moet u een [Run As-account](../manage-runas-account.md) gebruiken en de verbinding ophalen met [Get-AzAutomationConnection.](../automation-connections.md)


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Voorbeeld van grafisch loopboek

U `Get-AutomationPSCredential` een activiteit toevoegen aan een grafisch runbook door met de rechtermuisknop op de referenties in het deelvenster Bibliotheek van de grafische editor te klikken en **Toevoegen aan canvas te**selecteren.

![Referenties toevoegen aan canvas](../media/credentials/credential-add-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een referentie in een grafisch runbook. In dit geval biedt de referentie verificatie voor een runbook naar Azure-resources, zoals beschreven in [Azure AD gebruiken in Azure Automation om te verifiëren naar Azure](../automation-use-azure-ad.md). De eerste activiteit haalt de referentie op die toegang heeft tot het Azure-abonnement. De activiteit accountverbinding gebruikt deze referentie vervolgens om verificatie te bieden voor alle activiteiten die erna komen. Hier wordt een [pijplijnkoppeling](../automation-graphical-authoring-intro.md#links-and-workflow) gebruikt, omdat `Get-AutomationPSCredential` er één object wordt verwacht.  

![Referenties toevoegen aan canvas](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Referenties gebruiken in een DSC-configuratie

Hoewel DSC-configuraties in Azure Automation kunnen `Get-AutomationPSCredential`werken met referentie-elementen met behulp van , kunnen ze ook referentie-elementen doorgeven via parameters. Zie [Configuraties samenstellen in Azure Automation DSC](../automation-dsc-compile.md#credential-assets)voor meer informatie.

## <a name="using-credentials-in-python-2"></a>Referenties gebruiken in Python 2

In het volgende voorbeeld ziet u een voorbeeld van toegang tot referenties in Python 2-runbooks.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)voor meer informatie over koppelingen in grafische ontwerpen.
* Zie [Azure Automation Security](../automation-security-overview.md)voor het begrijpen van de verschillende verificatiemethoden voor automatisering.
* Zie Mijn eerste grafische runbook om aan de slag te gaan met grafische [runbooks.](../automation-first-runbook-graphical.md)
* Zie [Mijn eerste PowerShell-werkstroomrunboek](../automation-first-runbook-textual.md)om aan de slag te gaan met PowerShell-werkstroom.
* Zie [Mijn eerste Python 2-runbook om](../automation-first-runbook-textual-python2.md)aan de slag te gaan met Python 2 runbooks. 
