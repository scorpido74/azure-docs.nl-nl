---
title: Certificaat beheren in Azure Automation
description: Certificaten worden veilig opgeslagen in Azure Automation, zodat runbooks of DSC-configuraties toegang hebben tot deze certificaten om te verifiëren tegen Azure- en externe resources. In dit artikel worden de details van certificaten uitgelegd en hoe u ermee werken in zowel tekstuele als grafische ontwerpen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732831"
---
# <a name="manage-certificates-in-azure-automation"></a>Certificaten beheren in Azure Automation

Certificaten worden veilig opgeslagen in Azure Automation, zodat ze toegankelijk zijn via runbooks of DSC-configuraties met de [activiteit Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) voor Azure Resource Manager-resources. Met veilige certificaatopslag u runbooks- en DSC-configuraties maken die certificaten gebruiken voor verificatie of deze toevoegen aan Azure- of bronnen van derden.

Beveiligde elementen in Azure Automation omvatten referenties, certificaten, verbindingen en versleutelde variabelen. Deze elementen worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk automatiseringsaccount. Deze sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde asset opslaat, wordt de sleutel geladen vanuit Key Vault en vervolgens gebruikt om het item te versleutelen. Dit proces wordt beheerd door Azure Automation.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="az-powershell-cmdlets"></a>Az PowerShell-cmdlets

Voor Az worden de cmdlets in de volgende tabel gebruikt voor het maken en beheren van automatiseringsreferentie-elementen met Windows PowerShell. Ze worden verzonden als onderdeel van de [Az.Automation-module](/powershell/azure/overview), die beschikbaar is voor gebruik in automation runbooks en DSC-configuraties.

|Cmdlet |Beschrijving|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Uploadt een servicecertificaat voor de opgegeven cloudservice.|
|[Get-AzAutomationCertificaat](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Hiermee haalt u informatie op over een certificaat dat moet worden gebruikt in een runbook- of DSC-configuratie. U het certificaat alleen `Get-AutomationCertificate` zelf ophalen met behulp van de activiteit.|
|[Nieuw-AzAutomationCertificaat](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Hiermee maakt u een nieuw certificaat in Azure Automation.|
|[Certificaat voor het verwijderen van azautomation](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Hiermee verwijdert u een certificaat uit Azure Automation.|
|[Set-azautomationcertificaat](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Hiermee stelt u de eigenschappen in voor een bestaand certificaat, waaronder het uploaden van het certificaatbestand en het instellen van het wachtwoord voor een **PFX-bestand.**|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt om toegang te krijgen tot certificaten in een runbook- en DSC-configuraties.

| Activiteiten | Beschrijving |
|:---|:---|
|`Get-AutomationCertificate`|Hier wordt een certificaat ingeschakeld dat kan worden gebruikt in een runbook- of DSC-configuratie. Retourneert een object [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> U moet het gebruik `Name` van `Get-AutomationCertificate` variabelen in de parameter van in een runbook of DSC-configuratie vermijden. Het gebruik van variabelen in deze parameter bemoeilijkt de ontdekking van afhankelijkheden tussen runbooks of DSC-configuraties en automatiseringsvariabelen tijdens het ontwerp.

## <a name="python-2-functions"></a>Python 2- functies

De functie in de volgende tabel wordt gebruikt om toegang te krijgen tot certificaten in een Python 2-runbook.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_certificate` | Hiermee haalt u informatie op over een certificaatactief. |

> [!NOTE]
> U moet `automationassets` de module aan het begin van uw Python-runbook importeren om toegang te krijgen tot de assetfuncties.

## <a name="creating-a-new-certificate"></a>Een nieuw certificaat maken

Wanneer u een nieuw certificaat maakt, uploadt u een .cer- of .pfx-bestand naar Azure Automation. Als u het certificaat als exporteerbaar markeert, u het overbrengen uit het Azure Automation-certificaatarchief. Als het niet exporteerbaar is, kan het alleen worden gebruikt voor ondertekening binnen de runbook- of DSC-configuratie. Azure Automation vereist dat het certificaat de provider **Microsoft Enhanced RSA en AES Cryptographic Provider heeft.**

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Een nieuw certificaat maken met de Azure-portal

1. Klik in uw automatiseringsaccount op **Activa** om de pagina Activa te openen.
2. Selecteer **Certificaten** om de pagina Certificaten te openen.
3. Klik boven aan de pagina op **Een certificaat toevoegen.**
4. Typ een naam voor het certificaat in het veld **Naam.**
5. Als u naar een **.cer-** of **.pfx-bestand** wilt zoeken, klikt u op **Een bestand selecteren** onder Een **certificaatbestand uploaden**. Als u een **.pfx-bestand**selecteert, geeft u een wachtwoord op en geeft u aan of het kan worden geëxporteerd.
6. Klik **op Maken** om het nieuwe certificaatitem op te slaan.

### <a name="create-a-new-certificate-with-powershell"></a>Een nieuw certificaat maken met PowerShell

In het volgende voorbeeld wordt uitgelegd hoe u een nieuw automatiseringscertificaat maakt en het exporteerbaar markeert. In dit voorbeeld wordt een bestaand **.pfx-bestand** geïmporteerd.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Een nieuw certificaat maken met een resourcemanagersjabloon

In het volgende voorbeeld wordt uitgelegd hoe u een certificaat implementeert naar uw automatiseringsaccount met behulp van een Resource Manager-sjabloon via PowerShell:

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

Als u een certificaat `Get-AutomationCertificate` wilt gebruiken, gebruikt u de activiteit. U de cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) niet gebruiken, omdat het informatie over het certificaatactief retourneert, maar niet het certificaat zelf.

### <a name="textual-runbook-example"></a>Voorbeeld van tekstuele runbook

In het volgende voorbeeld ziet u hoe u een certificaat toevoegt aan een cloudservice in een runbook. In dit voorbeeld wordt het wachtwoord opgehaald uit een gecodeerde automatiseringsvariabele.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Voorbeeld van grafisch loopboek

Voeg `Get-AutomationCertificate` een activiteit toe aan een grafisch runbook door met de rechtermuisknop op het certificaat in het deelvenster Bibliotheek te klikken en **Toevoegen aan canvas te**selecteren.

![Certificaat toevoegen aan het canvas](../media/certificates/automation-certificate-add-to-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een certificaat in een grafisch runbook. Dit is hetzelfde als het vorige voorbeeld dat laat zien hoe u een certificaat toevoegt aan een cloudservice vanuit een tekstueel runbook.

![Voorbeeld grafische ontwerpen](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 voorbeeld

In het volgende voorbeeld ziet u hoe u toegang krijgt tot certificaten in Python2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Volgende stappen

- Zie Koppelingen in grafische ontwerpen voor meer informatie over het werken met koppelingen om de logische stroom van activiteiten die door uw runbook worden uitgevoerd, [te beheren.](../automation-graphical-authoring-intro.md#links-and-workflow) 
