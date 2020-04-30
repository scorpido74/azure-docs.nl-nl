---
title: Certificaat in Azure Automation beheren
description: Certificaten worden veilig opgeslagen in Azure Automation zodat runbooks of DSC-configuraties deze kunnen gebruiken om te verifiëren tegen Azure en bronnen van derden. In dit artikel worden de details van certificaten beschreven en wordt uitgelegd hoe u deze kunt gebruiken in tekstuele en grafische ontwerpen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732831"
---
# <a name="manage-certificates-in-azure-automation"></a>Certificaten in Azure Automation beheren

Certificaten worden veilig opgeslagen in Azure Automation zodat ze toegankelijk zijn voor runbooks of DSC-configuraties met behulp van de [Get-AzAutomationCertificate-](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) activiteit voor Azure Resource Manager resources. Met beveiligde certificaat opslag kunt u runbooks en DSC-configuraties maken die gebruikmaken van certificaten voor verificatie of toevoegen aan Azure of bronnen van derden.

Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Deze sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, wordt de sleutel geladen uit Key Vault en vervolgens gebruikt om de Asset te versleutelen. Dit proces wordt beheerd door Azure Automation.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>AZ Power shell-cmdlets

Voor AZ worden de cmdlets in de volgende tabel gebruikt om Automation-referentie-assets te maken en te beheren met Windows Power shell. Ze worden geleverd als onderdeel van de [module AZ. Automation](/powershell/azure/overview), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

|Cmdlet |Beschrijving|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Hiermee wordt een service certificaat voor de opgegeven Cloud service geüpload.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Haalt informatie op over een certificaat dat moet worden gebruikt in een runbook of DSC-configuratie. U kunt het certificaat zelf alleen ophalen met behulp van de `Get-AutomationCertificate` activiteit.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Hiermee maakt u een nieuw certificaat in Azure Automation.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Hiermee verwijdert u een certificaat van Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Hiermee stelt u de eigenschappen van een bestaand certificaat in, zoals het uploaden van het certificaat bestand en het instellen van het wacht woord voor een **PFX** -bestand.|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt voor toegang tot certificaten in een runbook en DSC-configuraties.

| Activiteiten | Beschrijving |
|:---|:---|
|`Get-AutomationCertificate`|Haalt een certificaat op dat moet worden gebruikt in een runbook-of DSC-configuratie. Retourneert een [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -object.|

> [!NOTE] 
> Vermijd het gebruik van variabelen in de `Name` para meter `Get-AutomationCertificate` van in een runbook of DSC-configuratie. Het gebruik van variabelen in deze para meter bemoeilijkt de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en Automation-variabelen tijdens de ontwerp fase.

## <a name="python-2-functions"></a>Python 2-functies

De functie in de volgende tabel wordt gebruikt voor toegang tot certificaten in een python 2-runbook.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_certificate` | Haalt informatie op over een certificaat Asset. |

> [!NOTE]
> U moet de `automationassets` module op het begin van het python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-certificate"></a>Een nieuw certificaat maken

Wanneer u een nieuw certificaat maakt, uploadt u een CER-of PFX-bestand naar Azure Automation. Als u het certificaat als exporteerbaar markeert, kunt u het uit het Azure Automation-certificaat archief overzetten. Als de service niet kan worden geëxporteerd, kan deze alleen worden gebruikt voor ondertekening binnen het runbook of de DSC-configuratie. Azure Automation vereist dat het certificaat de provider **micro soft Enhanced RSA en AES Cryptographic Provider**heeft.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Een nieuw certificaat maken met de Azure Portal

1. Klik vanuit uw Automation-account op **assets** om de pagina assets te openen.
2. Selecteer **certificaten** om de pagina certificaten te openen.
3. Klik boven aan de pagina op **een certificaat toevoegen** .
4. Typ in het veld **naam** een naam voor het certificaat.
5. Als u wilt bladeren naar een **CER** -of **PFX** -bestand, klikt u op **een bestand selecteren** onder **een certificaat bestand uploaden**. Als u een **PFX**-bestand selecteert, geeft u een wacht woord op en geeft u aan of het kan worden geëxporteerd.
6. Klik op **maken** om het nieuwe certificaat activum op te slaan.

### <a name="create-a-new-certificate-with-powershell"></a>Een nieuw certificaat maken met Power shell

In het volgende voor beeld ziet u hoe u een nieuw Automation-certificaat maakt en dit markeert als exporteerbaar. In dit voor beeld wordt een bestaand **PFX** -bestand geïmporteerd.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Een nieuw certificaat maken met een resource manager-sjabloon

In het volgende voor beeld ziet u hoe u een certificaat implementeert in uw Automation-account met behulp van een resource manager-sjabloon via Power shell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Een certificaat gebruiken

Gebruik de `Get-AutomationCertificate` activiteit om een certificaat te gebruiken. U kunt de cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) niet gebruiken, omdat deze informatie over de certificaat Asset retourneert, maar niet het certificaat zelf.

### <a name="textual-runbook-example"></a>Voor beeld van een tekst-runbook

In het volgende voor beeld ziet u hoe u een certificaat toevoegt aan een Cloud service in een runbook. In dit voor beeld wordt het wacht woord opgehaald uit een versleutelde automatiserings variabele.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Voor beeld van grafisch runbook

Voeg een `Get-AutomationCertificate` activiteit aan een grafisch runbook toe door met de rechter muisknop op het certificaat in het deel venster Bibliotheek te klikken en **toevoegen aan canvas**te selecteren.

![Certificaat toevoegen aan het canvas](../media/certificates/automation-certificate-add-to-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een certificaat in een grafisch runbook. Dit is hetzelfde als het vorige voor beeld waarin wordt uitgelegd hoe u een certificaat toevoegt aan een Cloud service vanuit een tekst-runbook.

![Voor beeld van grafisch ontwerpen](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2-voor beeld

Het volgende voor beeld laat zien hoe u toegang krijgt tot certificaten in Python2 runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Volgende stappen

- Zie [koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)voor meer informatie over het werken met koppelingen voor het beheren van de logische stroom van activiteiten die worden uitgevoerd door uw runbook. 
