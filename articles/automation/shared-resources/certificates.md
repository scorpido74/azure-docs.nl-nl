---
title: Certificaat assets in Azure Automation
description: Certificaten zijn veilig in Azure Automation, zodat ze toegankelijk zijn voor runbooks of DSC-configuraties om te verifiëren tegen Azure en bronnen van derden.  In dit artikel worden de details van certificaten beschreven en wordt uitgelegd hoe u deze kunt gebruiken in tekstuele en grafische ontwerpen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849476"
---
# <a name="certificate-assets-in-azure-automation"></a>Certificaat assets in Azure Automation

Certificaten worden veilig opgeslagen in Azure Automation zodat ze toegankelijk zijn voor runbooks of DSC-configuraties met behulp van de **Get-AzureRmAutomationCertificate-** activiteit voor Azure Resource Manager resources. Met deze mogelijkheid kunt u runbooks en DSC-configuraties maken die gebruikmaken van certificaten voor verificatie of toevoegen aan Azure of bronnen van derden.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Deze sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, wordt de sleutel geladen uit Key Vault en vervolgens gebruikt om de Asset te versleutelen. Dit proces wordt beheerd door Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM Power shell-cmdlets

De cmdlets in de volgende tabel worden gebruikt voor AzureRM om Automation-referentie assets te maken en te beheren met Windows Power shell. Ze worden geleverd als onderdeel van de [AzureRM. Automation-module](/powershell/azure/overview), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

|Cmdlets|Beschrijving|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Haalt informatie op over een certificaat dat moet worden gebruikt in een runbook of DSC-configuratie. U kunt het certificaat zelf alleen ophalen via de Get-AutomationCertificate-activiteit.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Hiermee maakt u een nieuw certificaat in Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Hiermee verwijdert u een certificaat van Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Hiermee stelt u de eigenschappen van een bestaand certificaat in, zoals het uploaden van het certificaat bestand en het instellen van het wacht woord voor een. pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Hiermee wordt een service certificaat voor de opgegeven Cloud service geüpload.|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt voor toegang tot certificaten in een runbook en DSC-configuraties.

| Activiteiten | Beschrijving |
|:---|:---|
|Get-AutomationCertificate|Haalt een certificaat op dat moet worden gebruikt in een runbook-of DSC-configuratie. Retourneert een [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -object.|

> [!NOTE] 
> Vermijd het gebruik van variabelen in de para meter – name van **Get-AutomationCertificate** in een RUNBOOK of DSC-configuratie, omdat deze de detectie van afhankelijkheden tussen RUNBOOKS of DSC-configuratie en Automation-variabelen tijdens de ontwerp fase vermoeilijkt.

## <a name="python2-functions"></a>Python2-functies

De functie in de volgende tabel wordt gebruikt voor toegang tot certificaten in een Python2-runbook.

| Functie | Beschrijving |
|:---|:---|
| automationassets.get_automation_certificate | Haalt informatie op over een certificaat Asset. |

> [!NOTE]
> U moet de module **automationassets** in het begin van het python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-certificate"></a>Een nieuw certificaat maken

Wanneer u een nieuw certificaat maakt, uploadt u een CER-of PFX-bestand naar Azure Automation. Als u het certificaat als exporteerbaar markeert, kunt u het uit het Azure Automation-certificaat archief overzetten. Als de service niet kan worden geëxporteerd, kan deze alleen worden gebruikt voor ondertekening binnen het runbook of de DSC-configuratie. Azure Automation vereist dat het certificaat de provider heeft: **micro soft Enhanced RSA en AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Een nieuw certificaat maken met de Azure Portal

1. Klik vanuit uw Automation-account op de tegel **assets** om de pagina **assets** te openen.
2. Klik op de tegel **certificaten** om de pagina **certificaten** te openen.
3. Klik boven aan de pagina op **een certificaat toevoegen** .
4. Typ een naam voor het certificaat in de **naam** vak.
5. Als u wilt bladeren naar een CER-of PFX-bestand, klikt u op **een bestand selecteren** onder **een certificaat bestand uploaden**. Als u een pfx-bestand selecteert, geeft u een wacht woord op en of het kan worden geëxporteerd.
6. Klik op **maken** om het nieuwe certificaat activum op te slaan.

### <a name="to-create-a-new-certificate-with-powershell"></a>Een nieuw certificaat maken met Power shell

In het volgende voor beeld ziet u hoe u een nieuw Automation-certificaat maakt en dit markeert als exporteerbaar. Hiermee wordt een bestaand pfx-bestand geïmporteerd.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Een nieuw certificaat maken met de Resource Manager-sjabloon

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Een certificaat gebruiken

Als u een certificaat wilt gebruiken, gebruikt u de activiteit **Get-AutomationCertificate** . U kunt de cmdlet [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) niet gebruiken omdat deze informatie over de certificaat Asset retourneert, maar niet het certificaat zelf.

### <a name="textual-runbook-sample"></a>Voor beeld van tekst-runbook

De volgende voorbeeld code laat zien hoe u een certificaat toevoegt aan een Cloud service in een runbook. In dit voor beeld wordt het wacht woord opgehaald uit een versleutelde automatiserings variabele.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Voor beeld van grafisch runbook

U voegt een **Get-AutomationCertificate** toe aan een grafisch runbook door met de rechter muisknop op het certificaat in het deel venster Bibliotheek te klikken en **toevoegen aan canvas**te selecteren.

![Certificaat toevoegen aan het canvas](../media/certificates/automation-certificate-add-to-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een certificaat in een grafisch runbook. Dit is hetzelfde als het vorige voor beeld waarin wordt uitgelegd hoe u een certificaat toevoegt aan een Cloud service vanuit een tekst-runbook.

![Voor beeld van grafisch ontwerpen](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-voor beeld

Het volgende voor beeld laat zien hoe u toegang krijgt tot certificaten in Python2 runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Volgende stappen

- Zie [koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)voor meer informatie over het werken met koppelingen voor het beheren van de logische stroom van activiteiten die uw runbook is ontworpen om uit te voeren. 
