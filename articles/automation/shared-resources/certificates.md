---
title: Verbindingsassets in Azure Automation
description: Certificaten zijn veilig in Azure Automation, zodat ze toegankelijk zijn via runbooks of DSC-configuraties om te verifiëren tegen Azure- en externe resources.  In dit artikel worden de details van certificaten uitgelegd en hoe u ermee werken in zowel tekstuele als grafische ontwerpen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849476"
---
# <a name="certificate-assets-in-azure-automation"></a>Verbindingsassets in Azure Automation

Certificaten worden veilig opgeslagen in Azure Automation, zodat ze toegankelijk zijn via runbooks of DSC-configuraties met de activiteit **Get-AzureRmAutomationCertificate** voor Azure Resource Manager-resources. Met deze mogelijkheid u runbooks- en DSC-configuraties maken die certificaten gebruiken voor verificatie of deze toevoegt aan Azure- of externe bronnen.

>[!NOTE]
>Beveiligde elementen in Azure Automation omvatten referenties, certificaten, verbindingen en versleutelde variabelen. Deze elementen worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk automatiseringsaccount. Deze sleutel wordt opgeslagen in een systeembeheerde Key Vault. Voordat u een beveiligde asset opslaat, wordt de sleutel geladen vanuit Key Vault en vervolgens gebruikt om het item te versleutelen. Dit proces wordt beheerd door Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdlets

Voor AzureRM worden de cmdlets in de volgende tabel gebruikt om automatiseringsreferentie-elementen te maken en te beheren met Windows PowerShell. Ze worden verzonden als onderdeel van de [AzureRM.Automation-module](/powershell/azure/overview), die beschikbaar is voor gebruik in automatiseringsloopboeken en DSC-configuraties.

|Cmdlets|Beschrijving|
|:---|:---|
|[Get-AzurermautomationCertificaat](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Hiermee haalt u informatie op over een certificaat dat moet worden gebruikt in een runbook- of DSC-configuratie. U het certificaat alleen ophalen bij Get-AutomationCertificate.|
|[Nieuw-Azurermautomationcertificaat](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Hiermee maakt u een nieuw certificaat in Azure Automation.|
[Certificaat voor verwijderen-azurermautomations](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Hiermee verwijdert u een certificaat uit Azure Automation.|
|[Set-Azurermautomationcertificaat](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Hiermee stelt u de eigenschappen in voor een bestaand certificaat, waaronder het uploaden van het certificaatbestand en het instellen van het wachtwoord voor een .pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Uploadt een servicecertificaat voor de opgegeven cloudservice.|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt om toegang te krijgen tot certificaten in een runbook- en DSC-configuraties.

| Activiteiten | Beschrijving |
|:---|:---|
|Get-AutomationCertificate|Hier wordt een certificaat ingeschakeld dat kan worden gebruikt in een runbook- of DSC-configuratie. Retourneert een object [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> U moet het gebruik van variabelen in de parameter -Name van **Get-AutomationCertificate** in een runbook- of DSC-configuratie vermijden, omdat dit het ontdekken van afhankelijkheden tussen runbooks of DSC-configuratie en automatiseringsvariabelen tijdens het ontwerp bemoeilijkt.

## <a name="python2-functions"></a>Python2-functies

De functie in de volgende tabel wordt gebruikt om toegang te krijgen tot certificaten in een Python2-runbook.

| Functie | Beschrijving |
|:---|:---|
| automationassets.get_automation_certificate | Hiermee haalt u informatie op over een certificaatactief. |

> [!NOTE]
> U moet de **module automationassets** importeren in het begin van uw Python-runbook om toegang te krijgen tot de assetfuncties.

## <a name="creating-a-new-certificate"></a>Een nieuw certificaat maken

Wanneer u een nieuw certificaat maakt, uploadt u een .cer- of .pfx-bestand naar Azure Automation. Als u het certificaat als exporteerbaar markeert, u het overbrengen uit het Azure Automation-certificaatarchief. Als het niet exporteerbaar is, kan het alleen worden gebruikt voor ondertekening binnen de runbook- of DSC-configuratie. Azure Automation vereist dat het certificaat de provider heeft: **Microsoft Enhanced RSA en AES Cryptographic Provider.**

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Een nieuw certificaat maken met de Azure-portal

1. Klik in uw automatiseringsaccount op de tegel **Activa** om de pagina **Activa** te openen.
2. Klik op de tegel **Certificaten** om de pagina Certificaten te **openen.**
3. Klik boven aan de pagina op **Een certificaat toevoegen.**
4. Typ een naam voor het certificaat in het vak **Naam.**
5. Als u naar een .cer- of .pfx-bestand wilt zoeken, klikt u op **Een bestand selecteren** onder Een **certificaatbestand uploaden**. Als u een .pfx-bestand selecteert, geeft u een wachtwoord op en of het kan worden geëxporteerd.
6. Klik **op Maken** om het nieuwe certificaatitem op te slaan.

### <a name="to-create-a-new-certificate-with-powershell"></a>Een nieuw certificaat maken met PowerShell

In het volgende voorbeeld wordt uitgelegd hoe u een nieuw automatiseringscertificaat maakt en het exporteerbaar markeert. Hiermee wordt een bestaand .pfx-bestand geïmporteerd.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Een nieuw certificaat maken met sjabloon Resourcemanager

In het volgende voorbeeld wordt uitgelegd hoe u een certificaat implementeert naar uw automatiseringsaccount met behulp van een resourcemanagersjabloon via PowerShell:

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

Als u een certificaat wilt gebruiken, gebruikt u de activiteit **Get-AutomationCertificate.** U de cmdlet [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) niet gebruiken omdat het informatie over het certificaatactief retourneert, maar niet het certificaat zelf.

### <a name="textual-runbook-sample"></a>Voorbeeld van tekstuele runbook

In de volgende voorbeeldcode ziet u hoe u een certificaat toevoegt aan een cloudservice in een runbook. In dit voorbeeld wordt het wachtwoord opgehaald uit een gecodeerde automatiseringsvariabele.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Voorbeeld van grafische runbook

U voegt een **Get-AutomationCertificate** toe aan een grafisch runbook door met de rechtermuisknop op het certificaat in het deelvenster Bibliotheek te klikken en **Toevoegen aan canvas te**selecteren.

![Certificaat toevoegen aan het canvas](../media/certificates/automation-certificate-add-to-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een certificaat in een grafisch runbook. Dit is hetzelfde als het vorige voorbeeld dat laat zien hoe u een certificaat toevoegt aan een cloudservice vanuit een tekstueel runbook.

![Voorbeeld grafische ontwerpen](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-voorbeeld

In het volgende voorbeeld ziet u hoe u toegang krijgt tot certificaten in Python2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)voor meer informatie over het werken met koppelingen om de logische stroom van activiteiten te beheren die uw runbook is ontworpen om uit te voeren. 
