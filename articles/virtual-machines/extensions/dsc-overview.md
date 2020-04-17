---
title: Overzicht van gewenste statusconfiguratie voor Azure
description: Meer informatie over het gebruik van de Microsoft Azure-extensiehandler voor De gewenste configuratie (PowerShell- statusconfiguratie) Het artikel bevat vereisten, architectuur en cmdlets.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: adb36e14bb7b772d3e28361bdc48d248bd84f41f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458961"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Inleiding tot de uitbreiding van de Desired State Configuration-handler

De Azure VM-agent en de bijbehorende extensies maken deel uit van Microsoft Azure-infrastructuurservices. VM-extensies zijn softwarecomponenten die de VM-functionaliteit uitbreiden en verschillende VM-beheerbewerkingen vereenvoudigen.

De primaire use case voor de Azure Desired State Configuration (DSC) extensie is om een VM op te start zetten naar de [Azure Automation State Configuration (DSC) service](../../automation/automation-dsc-overview.md).
De service biedt [voordelen](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) die het doorlopende beheer van de VM-configuratie en -integratie met andere operationele hulpprogramma's omvatten, zoals Azure Monitoring.
Het gebruik van de extensie om VM's te registreren voor de service biedt een flexibele oplossing die zelfs werkt voor Azure-abonnementen.

U de DSC-extensie onafhankelijk van de Automation DSC-service gebruiken.
Dit zal echter alleen een configuratie naar de VM pushen.
Er is geen doorlopende rapportage beschikbaar, behalve lokaal in de VM.

In dit artikel vindt u informatie over beide scenario's: de DSC-extensie voor onboarding voor automatisering gebruiken en de DSC-extensie gebruiken als hulpmiddel voor het toewijzen van configuraties aan VM's met behulp van de Azure SDK.

## <a name="prerequisites"></a>Vereisten

- **Lokale machine:** Als u wilt communiceren met de Azure VM-extensie, moet u de Azure-portal of de Azure PowerShell SDK gebruiken.
- **Gastagent**: De Azure VM die is geconfigureerd door de DSC-configuratie moet een besturingssysteem zijn dat Windows Management Framework (WMF) 4.0 of hoger ondersteunt. Zie de versiegeschiedenis van de [DSC-extensie](/powershell/scripting/dsc/getting-started/azuredscexthistory)voor de volledige lijst met ondersteunde OS-versies.

## <a name="terms-and-concepts"></a>Termen en concepten

Deze handleiding gaat uit van vertrouwdheid met de volgende concepten:

- **Configuratie:** een DSC-configuratiedocument.
- **Knooppunt**: een doel voor een DSC-configuratie. In dit document verwijst *knooppunt* altijd naar een Azure VM.
- **Configuratiegegevens**: een .psd1-bestand met omgevingsgegevens voor een configuratie.

## <a name="architecture"></a>Architectuur

De Azure DSC-extensie maakt gebruik van het Azure VM Agent-framework voor het leveren, uitvoeren en rapporteren over DSC-configuraties die worden uitgevoerd op Azure VM's. De DSC-extensie accepteert een configuratiedocument en een reeks parameters. Als er geen bestand is opgegeven, wordt een [standaardconfiguratiescript](#default-configuration-script) ingesloten met de extensie. Het standaardconfiguratiescript wordt alleen gebruikt om metagegevens in te stellen in [Lokaal Configuratiebeheer](/powershell/scripting/dsc/managing-nodes/metaConfig).

Wanneer de extensie voor de eerste keer wordt aangeroepen, installeert deze een versie van WMF met behulp van de volgende logica:

- Als het Azure VM-besturingssysteem Windows Server 2016 is, wordt er geen actie ondernomen. Windows Server 2016 heeft al de nieuwste versie van PowerShell geïnstalleerd.
- Als de eigenschap **wmfVersion** is opgegeven, is die versie van WMF geïnstalleerd, tenzij die versie niet compatibel is met het besturingssysteem van de VM.
- Als er geen **wmfVersion-eigenschap** is opgegeven, wordt de meest recente versie van WMF geïnstalleerd.

Het installeren van WMF vereist een herstart. Nadat de extensie opnieuw is opgestart, wordt het zip-bestand gedownload dat is opgegeven in de eigenschap **modulesUrl,** indien aanwezig. Als deze locatie zich in Azure Blob-opslag bevindt, u een SAS-token opgeven in de eigenschap **sasToken** om toegang te krijgen tot het bestand. Nadat de .zip is gedownload en uitgepakt, wordt de configuratiefunctie gedefinieerd in **configuratieFunctie** wordt uitgevoerd om een .mof(Managed[Object Format)](https://docs.microsoft.com/windows/win32/wmisdk/managed-object-format--mof-)bestand te genereren. De extensie `Start-DscConfiguration -Force` wordt vervolgens uitgevoerd met behulp van het gegenereerde .mof-bestand. De extensie legt de uitvoer vast en schrijft deze naar het Azure-statuskanaal.

### <a name="default-configuration-script"></a>Standaardconfiguratiescript

De Azure DSC-extensie bevat een standaardconfiguratiescript dat is bedoeld om te worden gebruikt wanneer u een VM aan boord neemt van de Azure Automation DSC-service. De scriptparameters zijn uitgelijnd met de configureerbare eigenschappen van [Lokaal Configuratiebeheer](/powershell/scripting/dsc/managing-nodes/metaConfig). Zie [Standaardconfiguratiescript](dsc-template.md#default-configuration-script) voor scriptparameters in [de configuratie-extensie Gewenste status met Azure Resource Manager-sjablonen](dsc-template.md). Zie de [azure-snelstartsjabloon in GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)voor het volledige script .

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informatie voor het registreren bij azure automation state configuration (DSC) service

Wanneer u de DSC-extensie gebruikt om een knooppunt te registreren bij de statusconfiguratieservice, moeten drie waarden worden opgegeven.

- RegistrationUrl - het https-adres van het Azure Automation-account
- RegistrationKey - een gedeeld geheim dat wordt gebruikt om knooppunten te registreren bij de service
- NodeConfigurationName - de naam van de knooppuntconfiguratie (MOF) die u uit de service moet halen om de serverrol te configureren

Deze informatie is te zien in de Azure-portal of u PowerShell gebruiken.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Controleer voor de naam Knooppuntconfiguratie of de knooppuntconfiguratie bestaat in azure-statusconfiguratie.  Als dit niet het zo is, wordt een fout in de extensie geïmplementeerd.  Zorg er ook voor dat u de naam van de *knooppuntconfiguratie* gebruikt en niet de configuratie.
Een configuratie wordt gedefinieerd in een script dat wordt gebruikt [om het Knooppuntconfiguratie (MOF-bestand) samen te stellen.](https://docs.microsoft.com/azure/automation/automation-dsc-compile)
De naam is altijd de configuratie `.` gevolgd `localhost` door een periode en een bepaalde computernaam.

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-extensie in Resource Manager-sjablonen

In de meeste scenario's zijn resourcemanager-implementatiesjablonen de verwachte manier om met de DSC-extensie te werken. Zie [De extensie Gewenste statusconfiguratie met Azure Resource Manager-sjablonen](dsc-template.md)voor meer informatie en voorbeelden van het opnemen van de DSC-extensie in de implementatiesjablonen van Resource Manager.

## <a name="dsc-extension-powershell-cmdlets"></a>DSC-extensie PowerShell-cmdlets

De PowerShell-cmdlets die worden gebruikt om de DSC-extensie te beheren, kunnen het beste worden gebruikt in interactieve scenario's voor het oplossen van problemen en het verzamelen van informatie. U de cmdlets gebruiken om implementaties van DSC-extensies te verpakken, te publiceren en te controleren. Cmdlets voor de DSC-extensie zijn nog niet bijgewerkt om te werken met het [standaardconfiguratiescript.](#default-configuration-script)

De **cmdlet Publish-AzVMDscConfiguration** neemt een configuratiebestand in beslag, scant het op afhankelijke DSC-bronnen en maakt vervolgens een .zip-bestand. Het .zip-bestand bevat de configuratie- en DSC-bronnen die nodig zijn om de configuratie uit te voeren. De cmdlet kan het pakket ook lokaal maken met de parameter *-OutputArchivePath.* Anders publiceert de cmdlet het .zip-bestand naar blobopslag en beveiligt het vervolgens met een SAS-token.

Het .ps1-configuratiescript dat de cmdlet maakt, bevindt zich in het .zip-bestand aan de hoofdmap van de archiefmap. De modulemap wordt in de archiefmap in bronnen geplaatst.

De **cmdlet Set-AzVMDscExtension** injecteert de instellingen die de PowerShell DSC-extensie nodig heeft in een VM-configuratieobject.

De **cmdlet Get-AzVMDscExtension** haalt de DSC-extensiestatus van een specifieke VM op.

De cmdlet **Get-AzVMDscExtensionStatus** haalt de status op van de DSC-configuratie die wordt uitgevoerd door de DSC-extensiehandler. Deze actie kan worden uitgevoerd op één vm of op een groep VM's.

Met de cmdlet **Remove-AzVMDscExtension** wordt de extensiehandler verwijderd uit een specifieke VM. Deze cmdlet verwijdert de configuratie *niet,* verwijdert WMF niet of wijzigt de toegepaste instellingen op de VM niet. Het verwijdert alleen de extensiehandler. 

Belangrijke informatie over de cmdlets van Resource Manager DSC-extensie:

- Azure Resource Manager-cmdlets zijn synchroon.
- De parameters *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName,* *Version*en *Location* zijn allemaal vereist.
- *ArchiveResourceGroupName* is een optionele parameter. U deze parameter opgeven wanneer uw opslagaccount tot een andere resourcegroep behoort dan de parameter waar de vm is gemaakt.
- Gebruik de switch **AutoUpdate** om de extensiehandler automatisch bij te werken naar de nieuwste versie wanneer deze beschikbaar is. Deze parameter kan leiden tot een herstart van de VM wanneer een nieuwe versie van WMF wordt uitgebracht.

### <a name="get-started-with-cmdlets"></a>Aan de slag met cmdlets

De Azure DSC-extensie kan DSC-configuratiedocumenten gebruiken om Azure VM's tijdens de implementatie rechtstreeks te configureren. Met deze stap wordt het knooppunt niet geregistreerd voor Automatisering. Het knooppunt wordt *niet* centraal beheerd.

In het volgende voorbeeld ziet u een eenvoudig voorbeeld van een configuratie. Sla de configuratie lokaal op als iisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Met de volgende opdrachten wordt het iisInstall.ps1-script op de opgegeven VM geïnstalleerd. De opdrachten voeren ook de configuratie uit en rapporteren vervolgens over de status.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om de DSC-extensie te implementeren op een bestaande virtuele machine.

Voor een virtuele machine met Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Voor een virtuele machine met Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure-portalfunctionaliteit

Ga als bedoeld als u DSC in de portal instelt:

1. Ga naar een VM.
2. Selecteer onder **Instellingen** de optie **Extensies**.
3. Selecteer op de nieuwe pagina die is gemaakt de optie **+ Toevoegen**en selecteer vervolgens de gewenste configuratie van **PowerShell.**
4. Klik onder aan de pagina extensie-informatie **op Maken.**

De portal verzamelt de volgende invoer:

- **Configuratiemodules of script:** dit veld is verplicht (het formulier is niet bijgewerkt voor het [standaardconfiguratiescript).](#default-configuration-script) Configuratiemodules en scripts vereisen een .ps1-bestand met een configuratiescript of een .zip-bestand met een .ps1-configuratiescript aan de hoofdmap. Als u een .zip-bestand gebruikt, moeten alle afhankelijke bronnen worden opgenomen in modulemappen in de .zip. U het .zip-bestand maken met de cmdlet **Publish-AzureVMDscConfiguration -OutputArchivePath** die is opgenomen in de Azure PowerShell SDK. Het .zip-bestand wordt geüpload naar de blob-opslag van uw gebruiker en beveiligd met een SAS-token.

- **Module-gekwalificeerde naam van configuratie:** U meerdere configuratiefuncties opnemen in een .ps1-bestand. Voer de naam in van het \\ ps1-script van de configuratie .ps1, gevolgd door en de naam van de configuratiefunctie. Als uw .ps1-script bijvoorbeeld de naamconfiguration.ps1 heeft en de configuratie **IisInstall**is, voert u **configuration.ps1\IisInstall**in.

- **Configuratieargumenten:** Als de configuratiefunctie argumenten aanneemt, voert u deze hier in de indeling **argumentName1=value1,argumentName2=value2**. Deze indeling is een andere indeling waarin configuratieargumenten worden geaccepteerd in PowerShell-cmdlets- of ResourceManager-sjablonen.

- **Configuratiegegevens PSD1-bestand:** dit veld is optioneel. Als voor uw configuratie een configuratiegegevensbestand in .psd1 nodig is, gebruikt u dit veld om het gegevensveld te selecteren en te uploaden naar de blob-opslag van uw gebruiker. Het configuratiegegevensbestand wordt beveiligd door een SAS-token in blob-opslag.

- **WMF-versie**: hiermee geeft u de versie van Windows Management Framework (WMF) op die op uw vm moet worden geïnstalleerd. Als u deze eigenschap instelt op de meest recente installatie van de meest recente versie van WMF. Momenteel zijn de enige mogelijke waarden voor deze eigenschap 4.0, 5.0, 5.1 en laatste. Deze mogelijke waarden zijn onderhevig aan updates. De standaardwaarde is **de laatste**.

- **Gegevensverzameling:** bepaalt of de extensie telemetrie verzamelt. Zie Azure [DSC-extensiegegevensverzameling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)voor meer informatie.

- **Versie**: Hiermee geeft u de versie van de Te installeren DSC-extensie op. Zie [Versiegeschiedenis van de DSC-extensie voor](/powershell/scripting/dsc/getting-started/azuredscexthistory)informatie over versies.

- **Auto Upgrade Minor Version**: Dit veld wordt toegewezen aan de **AutoUpdate-schakelaar** in de cmdlets en stelt de extensie in staat om tijdens de installatie automatisch te updaten naar de nieuwste versie. **Ja** zal de extensiehandler instrueren om de laatst beschikbare versie te gebruiken en **Nee** dwingt de opgegeven **versie** om te worden geïnstalleerd. Het selecteren van geen **ja** of **nee** is hetzelfde als het selecteren van **Nee.**

## <a name="logs"></a>Logboeken

Logboeken voor de extensie worden op de volgende locatie opgeslagen:`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Volgende stappen

- Ga voor meer informatie over PowerShell DSC naar het [PowerShell-documentatiecentrum.](/powershell/scripting/dsc/overview/overview)
- Onderzoek de [sjabloon Resourcemanager voor de DSC-extensie](dsc-template.md).
- Blader door de [PowerShell-galerie](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)voor meer functionaliteit die u beheren met PowerShell DSC en voor meer DSC-bronnen.
- Zie [Referenties veilig beheren met de DSC-extensiehandler](dsc-credentials.md)voor meer informatie over het doorgeven van gevoelige parameters in configuraties.
