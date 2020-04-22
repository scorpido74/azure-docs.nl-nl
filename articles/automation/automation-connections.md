---
title: Verbindingen beheren in Azure Automation
description: Verbindingselementen in Azure Automation bevatten de informatie die nodig is om verbinding te maken met een externe service of toepassing vanuit een runbook- of DSC-configuratie. In dit artikel worden de details van verbindingen en hoe ermee te werken uitgelegd in zowel tekstuele als grafische authoring.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 90d4ec1bbfd0d76ffedf1505c9147376e3947c3c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729059"
---
# <a name="manage-connections-in-azure-automation"></a>Verbindingen beheren in Azure Automation

Een item voor een automatiseringsverbinding bevat de informatie die nodig is om verbinding te maken met een externe service of toepassing vanuit een runbook- of DSC-configuratie. Dit kan informatie omvatten die nodig is voor verificatie, zoals een gebruikersnaam en wachtwoord, naast verbindingsgegevens zoals een URL of een poort. De waarde van een verbinding houdt alle eigenschappen voor het verbinden met een bepaalde toepassing in één asset in plaats van het maken van meerdere variabelen. De gebruiker kan de waarden voor een verbinding op één plaats bewerken en u de naam van een verbinding doorgeven aan een runbook- of DSC-configuratie in één parameter. De eigenschappen voor een verbinding zijn toegankelijk in de runbook- of DSC-configuratie met de `Get-AutomationConnection` activiteit.

Wanneer u een verbinding maakt, moet u een verbindingstype opgeven. Het verbindingstype is een sjabloon die een set eigenschappen definieert. De verbinding definieert waarden voor elke eigenschap die is gedefinieerd in het verbindingstype. Verbindingstypen worden toegevoegd aan Azure Automation in integratiemodules of gemaakt met de [Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100)) als de integratiemodule een verbindingstype bevat en wordt geïmporteerd in uw Automatiseringsaccount. Anders moet u een metagegevensbestand maken om een type automatiseringsverbinding op te geven. Zie [Integratiemodules](automation-integration-modules.md)voor meer informatie hierover.

>[!NOTE]
>Beveiligde elementen in Azure Automation omvatten referenties, certificaten, verbindingen en versleutelde variabelen. Deze elementen worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automatiseringsaccount. Deze sleutel wordt opgeslagen in een systeembeheerde Key Vault. Voordat u een beveiligde asset opslaat, wordt de sleutel geladen vanuit Key Vault en vervolgens gebruikt om het item te versleutelen. Dit proces wordt beheerd door Azure Automation.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="connection-types"></a>Verbindingstypen

Er zijn drie typen ingebouwde verbindingen beschikbaar in Azure Automation:

* **Azure** - Deze verbinding kan worden gebruikt om klassieke bronnen te beheren.
* **AzureClassicCertificate** - Deze verbinding wordt gebruikt door het **AzureClassicRunAs-account.**
* **AzureServicePrincipal** - Deze verbinding wordt gebruikt door het **AzureRunAs-account.**

In de meeste gevallen hoeft u geen verbindingsbron te maken omdat deze wordt gemaakt wanneer u een [Run As-account maakt.](manage-runas-account.md)

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-cmdlets

De cmdlets in de volgende tabel worden gebruikt om automatiseringsverbindingen met Windows PowerShell te maken en te beheren. Ze worden verzonden als onderdeel van de [Azure PowerShell-module](/powershell/azure/overview), die beschikbaar is voor gebruik in automatiseringsrunbooks en DSC-configuraties.

|Cmdlet|Beschrijving|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Hiermee haalt u een verbinding op. Bevat een hashtable met de waarden van de verbindingsvelden.|
|[Nieuwe-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Hiermee maakt u een nieuwe verbinding.|
|[Verbinding verwijderen-azautomation](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Hiermee verwijdert u een bestaande verbinding.|
|[Set-azAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Hiermee stelt u de waarde van een bepaald veld voor een bestaande verbinding in.|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt om toegang te krijgen tot verbindingen in een runbook- of DSC-configuratie.

|Activiteiten|Beschrijving|
|---|---|
|`Get-AutomationConnection` | Krijgt een verbinding te gebruiken. Retourneert een hashtable met de eigenschappen van de verbinding.|

>[!NOTE]
>Vermijd het gebruik `Name` van `Get-AutomationConnection`variabelen met de parameter van . Het gebruik van deze parameter kan het ontdekken van afhankelijkheden tussen runbooks of DSC-configuraties en verbindingselementen tijdens het ontwerp bemoeilijkt.

## <a name="python-2-functions"></a>Python 2- functies

De functie in de volgende tabel wordt gebruikt om toegang te krijgen tot verbindingen in een Python 2-runbook.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_connection` | Hiermee haalt u een verbinding op. Retourneert een woordenboek met de eigenschappen van de verbinding. |

> [!NOTE]
> U moet `automationassets` de module boven aan uw Python-runbook importeren om toegang te krijgen tot de assetfuncties.

## <a name="creating-a-new-connection"></a>Een nieuwe verbinding maken

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Een nieuwe verbinding maken met de Azure-portal

1. Klik vanuit uw automatiseringsaccount op het onderdeel **Activa** om het blad **Activa** te openen.
2. Klik op het onderdeel **Verbindingen** om het blad **Verbindingen** te openen.
3. Klik op Een verbinding boven aan het blad **toevoegen.**
4. Selecteer in de **vervolgkeuzelijst Type** het type verbinding dat u wilt maken. Het formulier presenteert de eigenschappen voor dat specifieke type.
5. Vul het formulier in en klik op **Maken** om de nieuwe verbinding op te slaan.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Een nieuwe verbinding met Windows PowerShell maken

Maak een nieuwe verbinding met `New-AzAutomationConnection` Windows PowerShell met de cmdlet. Deze cmdlet heeft `ConnectionFieldValues` een parameter met de naam die een [hashtabel](https://technet.microsoft.com/library/hh847780.aspx) verwacht die waarden definieert voor elk van de eigenschappen die door het verbindingstype worden gedefinieerd.

U de volgende voorbeeldopdrachten gebruiken als alternatief voor het maken van het Run As-account vanuit de portal om een nieuw verbindingsitem te maken.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

U het script gebruiken om het verbindingsitem te maken, omdat het automatisch meerdere globale modules `AzureServicePrincipal` bevat, `AzureRunAsConnection` samen met het verbindingstype om het verbindingsitem te maken. Dit is belangrijk om in gedachten te houden, want als u probeert een nieuw verbindingsitem te maken om verbinding te maken met een service of toepassing met een andere verificatiemethode, mislukt dit omdat het verbindingstype nog niet is gedefinieerd in uw Automatiseringsaccount. Zie [Integratiemodules](automation-integration-modules.md)voor meer informatie over het maken van uw eigen verbindingstype voor uw aangepaste of module in de [PowerShell-galerie.](https://www.powershellgallery.com)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Een verbinding gebruiken in een runbook- of DSC-configuratie

Haal een verbinding op in een runbook- of DSC-configuratie met de `Get-AutomationConnection` cmdlet. Je de `Get-AzAutomationConnection` activiteit niet gebruiken. Met deze activiteit worden de waarden van de verschillende velden in de verbinding opgehaald en als [hashtabel geretourneerd.](https://go.microsoft.com/fwlink/?LinkID=324844) Deze hashtable kan vervolgens worden gebruikt met de juiste opdrachten in de runbook- of DSC-configuratie.

### <a name="textual-runbook-sample"></a>Voorbeeld van tekstuele runbook

In de volgende voorbeeldopdrachten wordt aangegeven hoe u het eerder genoemde run-account gebruiken om te verifiëren met Azure Resource Manager-resources in uw runbook. Het maakt gebruik van de verbindingsasset die de Run As-account vertegenwoordigt, die verwijst naar de op certificaten gebaseerde serviceprincipal, niet referenties.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Voor niet-grafische PowerShell-runbooks `Add-AzAccount` en `Add-AzureRMAccount` zijn aliassen voor [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). U deze cmdlets gebruiken of u [uw modules](automation-update-azure-modules.md) in uw Automation-account updaten naar de nieuwste versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

### <a name="graphical-runbook-samples"></a>Grafische runbook-voorbeelden

U voegt `Get-AutomationConnection` een activiteit toe aan een grafisch runbook door met de rechtermuisknop op de verbinding in het deelvenster Bibliotheek van de grafische editor te klikken en **Toevoegen aan canvas te**selecteren.

![toevoegen aan canvas](media/automation-connections/connection-add-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een verbinding in een grafisch runbook. Dit is hetzelfde voorbeeld als hierboven voor het verifiëren van het run As-account met een tekstueel runbook. In dit `Constant value` voorbeeld wordt `Get RunAs Connection` de gegevensset gebruikt voor de activiteit die een verbindingsobject gebruikt voor verificatie. Hier wordt een [pijplijnkoppeling](automation-graphical-authoring-intro.md#links-and-workflow) gebruikt omdat de `ServicePrincipalCertificate` parameterset één object verwacht.

![verbindingen krijgen](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Voorbeeld van Python 2-runbook

In het volgende voorbeeld ziet u hoe u de run As-verbinding in een Python 2-runbook verifiëren.

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

- Bekijk [koppelingen in grafische ontwerpen](automation-graphical-authoring-intro.md#links-and-workflow) om te begrijpen hoe u de logicastroom in uw runbooks sturen en beheren.
* Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
- Zie [Integratiemodules](automation-integration-modules.md)voor meer informatie over het gebruik van PowerShell-modules en best practices voor het maken van uw eigen PowerShell-modules als integratiemodules binnen Azure Automation.