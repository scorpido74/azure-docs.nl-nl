---
title: Verbindingsreferentie in Azure Automation
description: Referentie-elementen in Azure Automation bevatten beveiligingsreferenties die kunnen worden gebruikt om te verifiëren voor resources die toegankelijk zijn voor de runbook- of DSC-configuratie. In dit artikel wordt beschreven hoe u referentie-elementen maakt en deze gebruikt in een runbook- of DSC-configuratie.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252699"
---
# <a name="credential-assets-in-azure-automation"></a>Verbindingsreferentie in Azure Automation

Een item als referentie-item voor automatisering bevat een object dat beveiligingsreferenties bevat, zoals een gebruikersnaam en wachtwoord. Runbooks- en DSC-configuraties kunnen cmdlets gebruiken die een PSCredential-object accepteren voor verificatie, of ze kunnen de gebruikersnaam en het wachtwoord van het PSCredential-object extraheren om een bepaalde toepassing of service te bieden die verificatie vereist. De eigenschappen voor een referentie worden veilig opgeslagen in Azure Automation en kunnen worden geopend in de runbook- of DSC-configuratie met de activiteit [Get-AutomationPSCredential.](#activities)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Beveiligde elementen in Azure Automation omvatten referenties, certificaten, verbindingen en versleutelde variabelen. Deze elementen worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk automatiseringsaccount. Deze sleutel wordt opgeslagen in Key Vault. Voordat u een beveiligde asset opslaat, wordt de sleutel geladen vanuit Key Vault en vervolgens gebruikt om het item te versleutelen.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell Az-cmdlets

Voor azure PowerShell Az-module worden de cmdlets in de volgende tabel gebruikt om automatiseringsreferentie-elementen te maken en te beheren met Windows PowerShell. Ze worden verzonden als onderdeel van de [AzureAz.Automation-module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), die beschikbaar is voor gebruik in automatiseringsloopboeken en DSC-configuraties.

| Cmdlets | Beschrijving |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hiermee haalt u informatie op over een referentieactief. Hiermee wordt geen PSCredential-object retourneert.  |
| [Nieuw-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Hiermee maakt u een nieuwe automatiseringsreferentie. |
| [Credential remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Hiermee verwijdert u een automatiseringsreferentie. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Hiermee stelt u de eigenschappen in voor een bestaande automatiseringsreferentie. |

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt om toegang te krijgen tot referenties in een runbook- en DSC-configuraties.

| Activiteiten | Beschrijving |
|:--- |:--- |
| Get-AutomationPSCredential |Hier wordt een referentie gebruikt in een runbook- of DSC-configuratie. Retourneert een object [System.Management.Automation.PSCredential.](/dotnet/api/system.management.automation.pscredential) |

> [!NOTE]
> U moet het gebruik van variabelen in de parameter -Name van Get-AutomationPSCredential vermijden, omdat dit het ontdekken van afhankelijkheden tussen runbooks of DSC-configuraties en referentieelementen tijdens het ontwerpkan bemoeilijken.

## <a name="python2-functions"></a>Python2-functies

De functie in de volgende tabel wordt gebruikt om toegang te krijgen tot referenties in een Python2-runbook.

| Functie | Beschrijving |
|:---|:---|
| automationassets.get_automation_credential | Hiermee haalt u informatie op over een referentieactief. |

> [!NOTE]
> U moet de module 'automatiseringsactiva' boven aan uw Python-runbook importeren om toegang te krijgen tot de assetfuncties.

## <a name="creating-a-new-credential-asset"></a>Een nieuw referentieactief maken

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Een nieuw referentieactief maken met de Azure-portal

1. Selecteer **referenties** onder **Gedeelde bronnen**in uw automatiseringsaccount.
1. Selecteer **Een referentie toevoegen**.
1. Vul het formulier in en selecteer **Maken** om de nieuwe referentie op te slaan.

> [!NOTE]
> Gebruikersaccounts die multi-factor authenticatie gebruiken, worden niet ondersteund voor gebruik in Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Een nieuw referentieactief maken met Windows PowerShell

In de volgende voorbeeldopdrachten ziet u hoe u een nieuwe automatiseringsreferentie maakt. Een PSCredential-object wordt eerst gemaakt met de naam en het wachtwoord en vervolgens gebruikt om het referentie-element te maken. U ook de cmdlet **Get-Credential** gebruiken om te worden gevraagd een naam en wachtwoord in te typen.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Een PowerShell-referentie gebruiken

U haalt een referentie-element op in een runbook- of DSC-configuratie met de activiteit **Get-AutomationPSCredential.** Hiermee wordt een [PSCredential-object](/dotnet/api/system.management.automation.pscredential) geretourneerd dat u gebruiken met een activiteit of cmdlet waarvoor een PARAMETER PSCredential vereist is. U ook de eigenschappen ophalen van het referentieobject dat u afzonderlijk wilt gebruiken. Het object heeft een eigenschap voor de gebruikersnaam en het beveiligde wachtwoord, of u de **GetNetworkCredential-methode** gebruiken om een [NetworkCredential-object](/dotnet/api/system.net.networkcredential) te retourneren dat een onbeveiligde versie van het wachtwoord biedt.

> [!NOTE]
> **Get-AzAutomationCredential** retourneert geen **PSCredential** die kan worden gebruikt voor verificatie. Het biedt alleen informatie over de referentie. Als u een referentie in een runbook moet gebruiken, moet u het **Get-AutomationPSCredential** gebruiken om het **PSCredential-object** op te halen.

### <a name="textual-runbook-sample"></a>Voorbeeld van tekstuele runbook

De volgende voorbeeldopdrachten laten zien hoe u een PowerShell-referentie gebruikt in een runbook. In dit voorbeeld wordt de referentie opgehaald en wordt de gebruikersnaam en het wachtwoord toegewezen aan variabelen.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

U ook een referentie gebruiken om te verifiëren aan Azure met [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Onder de meeste omstandigheden moet u een [Run As-account](../manage-runas-account.md) gebruiken en deze ophalen met [Get-AzAutomationConnection.](../automation-connections.md)

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Voorbeeld van grafische runbook

U voegt een activiteit **Get-AutomationPSCredential** toe aan een grafisch runbook door met de rechtermuisknop op de referenties in het deelvenster Bibliotheek van de grafische editor te klikken en **Toevoegen aan canvas te**selecteren.

![Referenties toevoegen aan canvas](../media/credentials/credential-add-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een referentie in een grafisch runbook. In dit geval wordt het gebruikt om verificatie te bieden voor een runbook naar Azure-resources zoals beschreven in [Runbooks verifiëren met Azure AD-gebruikersaccount.](../automation-create-aduser-account.md) De eerste activiteit haalt de referentie op die toegang heeft tot het Azure-abonnement. De **connect-AzureRmAccount-activiteit** gebruikt deze referentie vervolgens om verificatie te bieden voor alle activiteiten die erna komen. Een [pijplijnkoppeling](../automation-graphical-authoring-intro.md#links-and-workflow) is hier omdat **Get-AutomationPSCredential** één object verwacht.  

![Referenties toevoegen aan canvas](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Een PowerShell-referentie gebruiken in DSC

Hoewel DSC-configuraties in Azure Automation referentie-elementen kunnen nagaan met **Get-AutomationPSCredential,** kunnen referentie-elementen ook worden doorgegeven via parameters, indien gewenst. Zie [Configuraties samenstellen in Azure Automation DSC](../automation-dsc-compile.md#credential-assets)voor meer informatie.

## <a name="using-credentials-in-python2"></a>Referenties gebruiken in Python2

In het volgende voorbeeld ziet u een voorbeeld van toegang tot referenties in Python2-runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow) voor meer informatie over koppelingen in grafische ontwerpen
* Zie [Azure Automation Security](../automation-security-overview.md) voor de verschillende verificatiemethoden met Automatisering
* Zie [Mijn eerste grafische runbook](../automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Mijn eerste PowerShell-werkstroomrunboek](../automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell-werkstroomrunboeken
* Zie [Mijn eerste Python2-runbook om](../automation-first-runbook-textual-python2.md) aan de slag te gaan met Python2-runbooks 
