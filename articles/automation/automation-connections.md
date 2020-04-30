---
title: Verbindingen in Azure Automation beheren
description: Verbindings assets in Azure Automation bevatten de vereiste informatie om verbinding te maken met een externe service of toepassing vanuit een runbook of DSC-configuratie. In dit artikel worden de details van verbindingen beschreven en wordt uitgelegd hoe u deze kunt gebruiken in tekstuele en grafische ontwerpen.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097112"
---
# <a name="manage-connections-in-azure-automation"></a>Verbindingen in Azure Automation beheren

Een Automation-verbindings element bevat de informatie die nodig is om verbinding te maken met een externe service of toepassing vanuit een runbook of DSC-configuratie. Dit kan informatie bevatten die vereist is voor verificatie, zoals een gebruikers naam en wacht woord, naast de verbindings gegevens zoals een URL of poort. De waarde van een verbinding houdt in dat alle eigenschappen voor het verbinden met een bepaalde toepassing in één activum worden bewaard, in plaats van meerdere variabelen te maken. De gebruiker kan de waarden voor een verbinding op één plek bewerken en u kunt de naam van een verbinding met een runbook of DSC-configuratie door geven in één para meter. De eigenschappen voor een verbinding zijn toegankelijk in het runbook of de DSC-configuratie met `Get-AutomationConnection` de activiteit.

Wanneer u een verbinding maakt, moet u een verbindings type opgeven. Het verbindings type is een sjabloon die een set eigenschappen definieert. De verbinding definieert waarden voor elke eigenschap die in het verbindings type is gedefinieerd. Verbindings typen worden toegevoegd aan Azure Automation in integratie modules of worden gemaakt met de [Azure Automation-API](/previous-versions/azure/reference/mt163818(v=azure.100)) als de integratie module een verbindings type bevat en wordt geïmporteerd in uw Automation-account. Anders moet u een meta gegevensbestand maken om een type Automation-verbinding op te geven. Zie [integratie modules](automation-integration-modules.md)voor meer informatie hierover.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Deze sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, wordt de sleutel geladen uit Key Vault en vervolgens gebruikt om de Asset te versleutelen. Dit proces wordt beheerd door Azure Automation.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="connection-types"></a>Verbindingstypen

Er zijn drie soorten ingebouwde verbindingen beschikbaar in Azure Automation:

* **Azure** : deze verbinding kan worden gebruikt om klassieke resources te beheren.
* **AzureClassicCertificate** : deze verbinding wordt gebruikt door het **AzureClassicRunAs** -account.
* **AzureServicePrincipal** : deze verbinding wordt gebruikt door het **AzureRunAs** -account.

In de meeste gevallen hoeft u geen verbindings bron te maken omdat deze wordt gemaakt wanneer u een [uitvoeren als-account](manage-runas-account.md)maakt.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-cmdlets

De cmdlets in de volgende tabel worden gebruikt voor het maken en beheren van Automation-verbindingen met Windows Power shell. Ze worden geleverd als onderdeel van de [module Azure PowerShell](/powershell/azure/overview), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

|Cmdlet|Beschrijving|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Hiermee haalt u een verbinding op. Bevat een hashtabel met de waarden van de verbindings velden.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Hiermee maakt u een nieuwe verbinding.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Hiermee verwijdert u een bestaande verbinding.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Hiermee stelt u de waarde van een bepaald veld voor een bestaande verbinding in.|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt voor toegang tot verbindingen in een runbook of DSC-configuratie.

|Activiteiten|Beschrijving|
|---|---|
|`Get-AutomationConnection` | Hiermee haalt u een verbinding op die moet worden gebruikt. Retourneert een hashtabel met de eigenschappen van de verbinding.|

>[!NOTE]
>Vermijd het gebruik van variabelen `Name` met de `Get-AutomationConnection`para meter van. Het gebruik van deze para meter kan de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en verbindings assets tijdens ontwerp tijd bemoeilijken.

## <a name="python-2-functions"></a>Python 2-functies

De functie in de volgende tabel wordt gebruikt voor toegang tot verbindingen in een python 2-runbook.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_connection` | Hiermee haalt u een verbinding op. Retourneert een woorden lijst met de eigenschappen van de verbinding. |

> [!NOTE]
> U moet de `automationassets` module boven aan het python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-connection"></a>Een nieuwe verbinding maken

### <a name="create-a-new-connection-with-the-azure-portal"></a>Een nieuwe verbinding maken met de Azure Portal

1. Klik in uw Automation-account op het onderdeel **assets** om de Blade **assets** te openen.
2. Klik op het onderdeel **verbindingen** om de Blade **verbindingen** te openen.
3. Klik boven aan de Blade op **een verbinding toevoegen** .
4. Selecteer in de vervolg keuzelijst **type** het type verbinding dat u wilt maken. In het formulier worden de eigenschappen voor dat specifieke type weer gegeven.
5. Vul het formulier in en klik op **maken** om de nieuwe verbinding op te slaan.

### <a name="create-a-new-connection-with-windows-powershell"></a>Een nieuwe verbinding maken met Windows Power shell

Een nieuwe verbinding maken met Windows Power shell met `New-AzAutomationConnection` de cmdlet. Deze cmdlet heeft een para meter `ConnectionFieldValues` met de naam die verwacht dat een [hashtabel](https://technet.microsoft.com/library/hh847780.aspx) waarden definieert voor elk van de eigenschappen die worden gedefinieerd door het verbindings type.

U kunt de volgende voorbeeld opdrachten gebruiken als alternatief voor het maken van het uitvoeren als-account vanuit de portal om een nieuw verbindings element te maken.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Wanneer u uw Automation-account maakt, bevat het standaard verschillende globale modules, samen met het verbindings `AzureServicePrincipal` type voor het `AzureRunAsConnection` maken van de verbindings Asset. Als u een nieuw verbindings element maakt om verbinding te maken met een service of toepassing met een andere verificatie methode, mislukt de bewerking omdat het verbindings type niet al is gedefinieerd in uw Automation-account. Zie [integratie modules](automation-integration-modules.md)voor meer informatie over het maken van uw eigen verbindings type voor een aangepaste [PowerShell Gallery](https://www.powershellgallery.com) -module.

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Een verbinding gebruiken in een runbook of DSC-configuratie

Een verbinding in een runbook of DSC-configuratie ophalen met `Get-AutomationConnection` de cmdlet. U kunt de `Get-AzAutomationConnection` activiteit niet gebruiken. Met deze activiteit worden de waarden van de verschillende velden in de verbinding opgehaald en geretourneerd als [hashtabel](https://go.microsoft.com/fwlink/?LinkID=324844). Deze hashtabel kan vervolgens worden gebruikt met de juiste opdrachten in het runbook of de DSC-configuratie.

### <a name="textual-runbook-sample"></a>Voor beeld van tekst-runbook

De volgende voorbeeld opdrachten laten zien hoe u het eerder genoemde run as-account gebruikt om te verifiëren met Azure Resource Manager-resources in uw runbook. Hierbij wordt gebruikgemaakt van het verbindings element van het run as-account, dat verwijst naar de Service-Principal op basis van certificaten en niet de referenties.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Voor niet-grafische power shell- `Add-AzAccount` runbooks `Add-AzureRMAccount` en aliassen voor [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). U kunt deze cmdlets gebruiken of u kunt [uw modules](automation-update-azure-modules.md) in uw Automation-account bijwerken naar de meest recente versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

### <a name="graphical-runbook-samples"></a>Voor beelden van grafische runbook

U voegt een `Get-AutomationConnection` activiteit aan een grafisch runbook toe door met de rechter muisknop op de verbinding in het deel venster Bibliotheek van de grafische editor te klikken en **toevoegen aan canvas**te selecteren.

![toevoegen aan canvas](media/automation-connections/connection-add-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een verbinding in een grafisch runbook. Dit is hetzelfde voor beeld zoals hierboven wordt weer gegeven voor verificatie met behulp van het uitvoeren als-account met een tekst runbook. In dit voor beeld `Constant value` wordt de gegevensset gebruikt `Get RunAs Connection` voor de activiteit die gebruikmaakt van een verbindings object voor authenticatie. Er wordt hier een [pijplijn koppeling](automation-graphical-authoring-intro.md#links-and-workflow) gebruikt omdat de `ServicePrincipalCertificate` parameterset een enkel object verwacht.

![verbindingen ophalen](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2-runbook-voor beeld

In het volgende voor beeld ziet u hoe u kunt verifiëren met behulp van de run as-verbinding in een python 2-runbook.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk [koppelingen in grafische ontwerpen](automation-graphical-authoring-intro.md#links-and-workflow) om te begrijpen hoe u de stroom van logica in uw runbooks kunt door sturen en beheren.
* Zie [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)(Engelstalig) voor een Power shell-cmdlet-verwijzing.
- Zie [integratie modules](automation-integration-modules.md)voor meer informatie Azure Automation over het gebruik van Power shell-modules en aanbevolen procedures voor het maken van uw eigen Power shell-modules voor het werken met integratie modules in azure Automation.