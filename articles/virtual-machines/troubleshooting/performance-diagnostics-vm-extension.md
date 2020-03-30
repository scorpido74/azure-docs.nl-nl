---
title: VM-extensie Azure Performance Diagnostics voor Windows| Microsoft Documenten
description: Introduceert Azure Performance Diagnostics VM Extension voor Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f104fc6513874bfef5f4bf9fe7f536c3e3d69cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057551"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>VM-extensie voor Windows voor diagnostische gegevens over Azure-prestaties

Azure Performance Diagnostics VM Extension helpt bij het verzamelen van diagnostische prestatiegegevens van Windows VM's. De extensie voert analyses uit en biedt een rapport met bevindingen en aanbevelingen om prestatieproblemen op de virtuele machine te identificeren en op te lossen. Deze extensie installeert een hulpprogramma voor het oplossen van problemen genaamd [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Als u diagnostische gegevens op uw VM wilt uitvoeren vanuit de Azure-portal voor niet-klassieke VM's, wordt aanbevolen om de nieuwe ervaring te gebruiken. Zie [Prestatiediagnose voor virtuele Azure-machines voor](performance-diagnostics.md) meer informatie 

## <a name="prerequisites"></a>Vereisten

Deze extensie kan worden geïnstalleerd op Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 en Windows Server 2016. Het kan ook worden geïnstalleerd op Windows 8.1 en Windows 10.

## <a name="extension-schema"></a>Extensieschema
In de volgende JSON wordt het schema voor Azure Performance Diagnostics VM Extension weergegeven. Voor deze extensie zijn de naam en de sleutel voor een opslagaccount vereist om de diagnostische uitvoer en het rapport op te slaan. Deze waarden zijn gevoelig. Opslagaccountsleutel moet worden opgeslagen in een configuratie met beveiligde instelling. Azure VM-extensie beveiligde instellinggegevens worden versleuteld en worden alleen gedecodeerd op de virtuele doelmachine. Houd er rekening mee dat **storageAccountName** en **storageAccountKey** hoofdlettergevoelig zijn. Andere vereiste parameters worden vermeld in de volgende sectie.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Eigenschapswaarden

|   **Naam**   |**Waarde / Voorbeeld**|       **Beschrijving**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|De versie van de API.
|uitgever|Microsoft.Azure.Performance.Diagnostics|De naamruimte van de uitgever voor de extensie.
|type|AzurePerformanceDiagnostics|Het type VM-extensie.
|typeHandlerVersie|1.0|De versie van de extensiehandler.
|prestatiesScenario|Basic|Het prestatiescenario waarvoor gegevens kunnen worden vastgelegd. Geldige waarden zijn: **basic,** **vmslow,** **azurefiles**en **custom**.
|traceDurationInSeconds|300|De duur van de sporen, als een van de traceringsopties is geselecteerd.
|perfCounterTrace|p|Optie om Prestatiemetertracering in te schakelen. Geldige waarden zijn **p** of lege waarde. Als u dit spoor niet wilt vastleggen, laat u de waarde als leeg.
|networkTrace|n|Optie om Netwerktracering in te schakelen. Geldige waarden zijn **n** of lege waarde. Als u dit spoor niet wilt vastleggen, laat u de waarde als leeg.
|xperfTrace xperfTrace|x|Optie om XPerf Trace in te schakelen. Geldige waarden zijn **x** of lege waarde. Als u dit spoor niet wilt vastleggen, laat u de waarde als leeg.
|storPortTrace storPortTrace|s|Optie om StorPort Trace in te schakelen. Geldige waarden zijn **s** of lege waarde. Als u dit spoor niet wilt vastleggen, laat u de waarde als leeg.
|srAantal|123452016365929|Het ondersteuningsticketnummer, indien beschikbaar. Laat de waarde leeg als je het niet hebt.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Huidige datumtijd in Utc. Als u de portal gebruikt om deze extensie te installeren, hoeft u deze waarde niet op te geven.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|De unieke id van een VM.
|storageAccountName|mystorageaccount|De naam van het opslagaccount om de diagnostische logboeken en resultaten op te slaan.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc==|De sleutel voor de opslagrekening.

## <a name="install-the-extension"></a>De extensie installeren

Volg deze instructies om de extensie op virtuele Windows-machines te installeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de virtuele machine waar u deze extensie wilt installeren.

    ![Schermafbeelding van Azure-portal, met virtuele machines gemarkeerd](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecteer het blad **Extensies** en selecteer **Toevoegen**.

    ![Schermafbeelding van het blad Extensies, met Markeren toevoegen](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecteer **Azure Performance Diagnostics,** bekijk de algemene voorwaarden en selecteer **Maken**.

    ![Schermafbeelding van het scherm Nieuwe bron, met Azure Performance Diagnostics gemarkeerd](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Geef de parameterwaarden voor de installatie op en selecteer **OK** om de extensie te installeren. Zie [PerfInsights gebruiken](how-to-use-perfinsights.md#supported-troubleshooting-scenarios)voor meer informatie over ondersteunde scenario's. 

    ![Schermafbeelding van het dialoogvenster Extensie installeren](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Wanneer de installatie is geslaagd, ziet u een bericht met deze status.

    ![Schermafbeelding van het geslaagde bericht voor het inrichten](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > De uitbreiding wordt uitgevoerd wanneer de inrichting is geslaagd. Het duurt twee minuten of minder om te voltooien voor het basisscenario. Voor andere scenario's loopt het door de duur die tijdens de installatie is opgegeven.

## <a name="remove-the-extension"></a>De extensie verwijderen
Voer de volgende stappen uit om de extensie uit een virtuele machine te verwijderen:

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer de virtuele machine waaruit u deze extensie wilt verwijderen en selecteer vervolgens het **blad Extensies.** 
2. Selecteer de (**...**) voor de vermelding Prestatiediagnostische extensie in de lijst en selecteer **Verwijderen**.

    ![Schermafbeelding van het blad Extensies, met Verwijderen gemarkeerd](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > U ook de extensievermelding selecteren en de optie **Verwijderen** selecteren.

## <a name="template-deployment"></a>Sjabloonimplementatie
Azure virtual machine extensions kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie is beschreven, kan worden gebruikt in een Azure Resource Manager-sjabloon. Hiermee wordt de VM-extensie Azure Performance Diagnostics uitgevoerd tijdens een azure resource manager-sjabloonimplementatie. Hier is een voorbeeldsjabloon:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>PowerShell-implementatie
De `Set-AzVMExtension` opdracht kan worden gebruikt om De VM-extensie azure performance diagnostics te implementeren in een bestaande virtuele machine.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informatie over de vastgelegde gegevens
Het gereedschap PerfInsights verzamelt verschillende logboeken, configuratie- en diagnostische gegevens, afhankelijk van het geselecteerde scenario. Zie voor meer informatie de [documentatie van PerfInsights.](https://aka.ms/perfinsights)

## <a name="view-and-share-the-results"></a>De resultaten weergeven en delen

Uitvoer van de extensie is te vinden in een zip-bestand dat is geüpload naar het opslagaccount dat tijdens de installatie is opgegeven en gedurende 30 dagen wordt gedeeld met Behulp van [Shared Access Signatures (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) Dit zip-bestand bevat diagnostische logboeken en een rapport met bevindingen en aanbevelingen. Een SAS-koppeling naar het uitvoerzip-bestand is te vinden in een tekstbestand met de naam *zipfilename*_saslink.txt onder de map **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics-versie\\\<>**. Iedereen die deze link heeft, kan het zip-bestand downloaden.

Om de ondersteuningstechnicus te helpen die aan uw ondersteuningsticket werkt, kan Microsoft deze SAS-koppeling gebruiken om de diagnostische gegevens te downloaden.

Als u het rapport wilt bekijken, haalt u het zip-bestand uit en opent u het bestand **PerfInsights Report.html.**

U moet ook in staat zijn om de zip-bestand rechtstreeks downloaden van de portal door het selecteren van de extensie.

![Schermafbeelding van de gedetailleerde status van Prestatiediagnostiek](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> De SAS-koppeling die in de portal wordt weergegeven, werkt soms mogelijk niet. Dit kan worden veroorzaakt door een misvormde URL tijdens de coderings- en decoderingsbewerkingen. U de koppeling in plaats daarvan rechtstreeks van het *_saslink.txt-bestand van de VM ophalen.

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

- De status van extensieimplementatie (in het meldingsgebied) kan 'Implementatie in uitvoering' weergeven, ook al is de extensie met succes ingericht.

    Dit probleem kan veilig worden genegeerd, zolang de extensiestatus aangeeft dat de extensie is ingericht.
- U een aantal problemen tijdens de installatie oplossen met behulp van de extensielogboeken. De uitvoer van extensieuitvoering wordt vastgelegd in bestanden in de volgende map:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen**. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure-ondersteuning.
