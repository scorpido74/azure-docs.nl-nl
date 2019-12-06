---
title: Verbindings assets in Azure Automation
description: Verbindings assets in Azure Automation bevatten de vereiste informatie om verbinding te maken met een externe service of toepassing vanuit een runbook of DSC-configuratie. In dit artikel worden de details van verbindingen beschreven en wordt uitgelegd hoe u deze kunt gebruiken in tekstuele en grafische ontwerpen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 621441afaa9bef08a8ebf3b0af082c6a17c77b1b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850428"
---
# <a name="connection-assets-in-azure-automation"></a>Verbindings assets in Azure Automation

Een Automation-verbindings element bevat de informatie die nodig is om verbinding te maken met een externe service of toepassing vanuit een runbook of DSC-configuratie. Dit kan informatie bevatten die vereist is voor verificatie, zoals een gebruikers naam en wacht woord, naast de verbindings gegevens zoals een URL of poort. De waarde van een verbinding houdt in dat alle eigenschappen voor het verbinden met een bepaalde toepassing in één activum worden bewaard, in plaats van meerdere variabelen te maken. De gebruiker kan de waarden voor een verbinding op één plek bewerken en u kunt de naam van een verbinding met een runbook of DSC-configuratie door geven in één para meter. De eigenschappen voor een verbinding zijn toegankelijk in het runbook of de DSC-configuratie met de activiteit **Get-AutomationConnection** .

Wanneer u een verbinding maakt, moet u een *verbindings type*opgeven. Het verbindings type is een sjabloon die een set eigenschappen definieert. De verbinding definieert waarden voor elke eigenschap die in het verbindings type is gedefinieerd. Verbindings typen worden toegevoegd aan Azure Automation in integratie modules of worden gemaakt met de [Azure Automation-API](/previous-versions/azure/reference/mt163818(v=azure.100)) als de integratie module een verbindings type bevat en wordt geïmporteerd in uw Automation-account. Anders moet u een meta gegevensbestand maken om een type Automation-verbinding op te geven.  Zie [integratie modules](automation-integration-modules.md)voor meer informatie hierover.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Deze sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, wordt de sleutel geladen uit Key Vault en vervolgens gebruikt om de Asset te versleutelen. Dit proces wordt beheerd door Azure Automation.

## <a name="connection-types"></a>Verbindingstypen

Er zijn drie typen ingebouwde verbindingen beschikbaar in Azure Automation:

* **Azure** : deze verbinding kan worden gebruikt om klassieke resources te beheren.
* **AzureClassicCertificate** : deze verbinding wordt gebruikt door het **AzureClassicRunAs** -account.
* **AzureServicePrincipal** : deze verbinding wordt gebruikt door het **AzureRunAs** -account.

In de meeste gevallen hoeft u geen verbindings bron te maken omdat deze wordt gemaakt wanneer u een runas- [account](manage-runas-account.md)maakt.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets

De cmdlets in de volgende tabel worden gebruikt voor het maken en beheren van Automation-verbindingen met Windows Power shell. Ze worden geleverd als onderdeel van de [Azure PowerShell-module](/powershell/azure/overview) die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

|Cmdlet|Beschrijving|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Hiermee haalt u een verbinding op. Bevat een hash-tabel met de waarden van de velden van de verbinding.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Hiermee maakt u een nieuwe verbinding.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Verwijder een bestaande verbinding.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Hiermee stelt u de waarde van een bepaald veld voor een bestaande verbinding.|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt voor toegang tot verbindingen in een runbook of DSC-configuratie.

|Activiteiten|Beschrijving|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Hiermee haalt u een verbinding op die moet worden gebruikt. Retourneert een hash-tabel met de eigenschappen van de verbinding.|

>[!NOTE]
>Vermijd het gebruik van variabelen met de para meter – name van **Get-AutomationConnection** , omdat dit het detecteren van afhankelijkheden tussen RUNBOOKS of DSC-configuraties en verbindings assets tijdens de ontwerp fase kan bemoeilijken.


## <a name="python2-functions"></a>Python2-functies
De functie in de volgende tabel wordt gebruikt voor toegang tot verbindingen in een Python2-runbook.

| Functie | Beschrijving |
|:---|:---|
| automationassets.get_automation_connection | Hiermee haalt u een verbinding op. Retourneert een woorden lijst met de eigenschappen van de verbinding. |

> [!NOTE]
> U moet de module ' automationassets ' boven aan uw python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-connection"></a>Een nieuwe verbinding maken

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Een nieuwe verbinding maken met de Azure Portal

1. Klik in uw Automation-account op het onderdeel **assets** om de Blade **assets** te openen.
2. Klik op het onderdeel **verbindingen** om de Blade **verbindingen** te openen.
3. Klik boven aan de Blade op **een verbinding toevoegen** .
4. Selecteer in de vervolg keuzelijst **type** het type verbinding dat u wilt maken. In het formulier worden de eigenschappen voor dat specifieke type weer gegeven.
5. Vul het formulier in en klik op **maken** om de nieuwe verbinding op te slaan.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Een nieuwe verbinding maken met Windows PowerShell

Maak een nieuwe verbinding met Windows Power shell met de cmdlet [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) . Deze cmdlet heeft een para meter met de naam **ConnectionFieldValues** die een [hash-tabel](https://technet.microsoft.com/library/hh847780.aspx) verwacht die waarden definieert voor elk van de eigenschappen die worden gedefinieerd door het verbindings type.

Als u bekend bent met het [uitvoeren als-account](automation-sec-configure-azure-runas-account.md) van Automation om runbooks te verifiëren met behulp van de Service-Principal, het Power shell-script, dat als alternatief voor het maken van het uitvoeren als-account uit de portal wordt gebruikt, maakt u een nieuw verbindings element met behulp van de volgende voorbeeld opdrachten.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

U kunt het script gebruiken om het verbindings element te maken. Wanneer u uw Automation-account maakt, bevat het automatisch diverse algemene modules, samen met het verbindings type **AzureServicePrincipal** om de **AzureRunAsConnection** -verbindings Asset te maken.  Het is belang rijk dat u rekening houdt met het volgende: als u een nieuw verbindings element maakt om verbinding te maken met een service of toepassing met een andere verificatie methode, mislukt dit omdat het verbindings type niet al is gedefinieerd in uw Automation-account.  Zie [integratie modules](automation-integration-modules.md) voor meer informatie over het maken van uw eigen verbindings type voor uw aangepaste of module van de [PowerShell Gallery](https://www.powershellgallery.com).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Een verbinding gebruiken in een runbook of DSC-configuratie

U haalt een verbinding op in een runbook of DSC-configuratie met de cmdlet **Get-AutomationConnection** .  U kunt de activiteit [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) niet gebruiken.  Met deze activiteit worden de waarden van de verschillende velden in de verbinding opgehaald en geretourneerd als een [hash-tabel](https://go.microsoft.com/fwlink/?LinkID=324844) die vervolgens kan worden gebruikt met de juiste opdrachten in het runbook of de DSC-configuratie.

### <a name="textual-runbook-sample"></a>Voor beeld van tekst-runbook

De volgende voorbeeld opdrachten laten zien hoe u het eerder genoemde run as-account gebruikt om te verifiëren met Azure Resource Manager-resources in uw runbook.  Hierbij wordt gebruikgemaakt van het verbindings element van het run as-account, dat verwijst naar de Service-Principal op basis van certificaten en niet de referenties.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRmAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzureRMAccount**ziet, kunt u **add-AzureRMAccount**gebruiken, maar u kunt ook uw modules bijwerken in uw Automation-account.

### <a name="graphical-runbook-samples"></a>Voor beelden van grafische runbook

U voegt een **Get-AutomationConnection-** activiteit toe aan een grafisch runbook door met de rechter muisknop op de verbinding in het deel venster Bibliotheek van de grafische editor te klikken en **toevoegen aan canvas**te selecteren.

![toevoegen aan canvas](media/automation-connections/connection-add-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een verbinding in een grafisch runbook.  Dit is hetzelfde voor beeld dat hierboven wordt weer gegeven voor verificatie met behulp van het uitvoeren als-account met een tekst runbook.  In dit voor beeld wordt de gegevensset voor **constante waarden** gebruikt voor de activiteit **runas-verbinding ophalen** die gebruikmaakt van een verbindings object voor verificatie.  Er wordt hier een [pijplijn koppeling](automation-graphical-authoring-intro.md#links-and-workflow) gebruikt omdat de parameterset serviceprincipalcertificate-parameterset één object verwacht.

![verbindingen ophalen](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 runbook-voor beeld
In het volgende voor beeld ziet u hoe u kunt verifiëren met behulp van de run as-verbinding in een Python2-runbook.

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

- Zie [integratie modules](automation-integration-modules.md)voor meer informatie Azure Automation over het gebruik van Power shell-modules en aanbevolen procedures voor het maken van uw eigen Power shell-modules voor het werken met integratie modules in azure Automation.

