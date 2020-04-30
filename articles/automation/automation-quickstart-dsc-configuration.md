---
title: 'Azure-quickstart: een virtuele machine met DSC configureren | Microsoft Docs'
description: Een LAMP-stack op een virtuele Linux-machine met Desired State Configuration configureren
services: automation
ms.subservice: dsc
keywords: dsc, configuratie, automatiseren
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 1a146ab7c05d200b71a33a72fa6362c3cf62629a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81457515"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Een virtuele machine met desired state Configuration configureren

Door Azure Automation status configuratie in te scha kelen, kunt u de configuraties van uw Windows-en Linux-servers beheren en controleren met behulp van desired state Configuration (DSC). Configuraties die van een gewenste configuratie worden afgeleid, kunnen worden geïdentificeerd of automatisch worden gecorrigeerd. Deze quickstart bevat stappen voor het onboarden van een virtuele Linux-machine en voor de configuratie van een LAMP-stack met DSC.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/).
* Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](automation-sec-configure-azure-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
* Een virtuele Azure Resource Manager-machine (niet Klassiek) waarop Red Hat Enterprise Linux, CentOS of Oracle Linux wordt uitgevoerd. Zie [Uw eerste virtuele Linux-machine maken met behulp van Azure Portal](../virtual-machines/linux/quick-create-portal.md) voor instructies voor het maken van een VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij Azure https://portal.azure.comop.

## <a name="onboard-a-virtual-machine"></a>Een virtuele machine onboarden

Er zijn veel verschillende methoden om een machine vrij te maken en DSC in te scha kelen. Deze quickstart bevat informatie over onboarding via een Automation-account. Lees het artikel over de [onboarding](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding) voor meer informatie over de verschillende methoden voor het voorbereiden van de configuratie van uw machines.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Automation-accounts**. Als deze niet in het linkerdeel venster wordt weer gegeven, klikt u op **alle services** en zoekt u in de resulterende weer gave.
1. Selecteer een Automation-account in de lijst.
1. Selecteer in het linkerdeelvenster van het Automation-account de optie **Statusconfiguratie (DSC)**.
2. Klik op **Toevoegen** om de selectiepagina van de virtuele machine te openen.
3. Zoek de virtuele machine waarvoor DSC moet worden ingeschakeld. U kunt het zoekveld en de filteropties gebruiken om een specifieke virtuele machine te vinden.
4. Klik op de virtuele machine en klik vervolgens op **verbinding maken**
5. Selecteer de juiste DSC-instellingen voor de virtuele machine. Als u al een configuratie hebt voor bereid, kunt u deze opgeven `Node Configuration Name`als. U kunt de [configuratiemodus](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig) instellen om het configuratiegedrag voor de machine te bepalen.
6. Klik op **OK**. Terwijl de DSC-extensie op de virtuele machine wordt geïmplementeerd, wordt de status weer `Connecting`gegeven als.

![De onboarding van virtuele Azure-machine voor DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Modules importeren

Modules bevatten DSC-resources en veel kunnen worden gevonden in de [PowerShell Gallery](https://www.powershellgallery.com). Alle resources die in uw configuraties worden gebruikt, moeten worden geïmporteerd in het Automation-account voordat u het compileert. Voor deze zelfstudie is de module met de naam **nx** vereist.

1. Selecteer in het linkerdeel venster van het Automation-account de **Galerie modules** onder **gedeelde bronnen**.
1. Zoek naar de module die u wilt importeren door een deel van de `nx`naam te typen:.
1. Klik op de module die u wilt importeren.
1. Klik op **importeren**.

![Een DSC-module importeren](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>De configuratie importeren

In deze quickstart wordt gebruikgemaakt van een DSC-configuratie waarmee Apache HTTP Server, MySQL en PHP op de computer worden geconfigureerd. Zie [DSC-configuraties](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

Typ het volgende in een tekst editor en sla deze lokaal op als **AMPServer. ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

De configuratie importeren:

1. Selecteer in het linkerdeelvenster van het Automation-account de optie **Statusconfiguratie (DSC)** en klik vervolgens op het tabblad **Configuraties**.
2. Klik op **+ Toevoegen**.
3. Selecteer het configuratie bestand dat u in de vorige stap hebt opgeslagen.
4. Klik op **OK**.

## <a name="compile-a-configuration"></a>Een configuratie compileren

U moet een DSC-configuratie compileren naar een knooppunt configuratie (MOF-document) voordat deze aan een knoop punt kan worden toegewezen. Tijdens de compilatie wordt de configuratie gevalideerd en is het mogelijk parameterwaarden in te voeren. Zie [configuraties compileren in status configuratie](automation-dsc-compile.md)voor meer informatie over het compileren van een configuratie.

1. Selecteer in het linkerdeel venster van het Automation-account **State Configuration (DSC)** en klik vervolgens op het tabblad **configuraties** .
1. Selecteer de configuratie `LAMPServer`.
1. Selecteer in de menu opties **compileren** en klik vervolgens op **Ja**.
1. In de configuratie weergave ziet u een nieuwe compilatie taak in de wachtrij. Wanneer de taak is voltooid, bent u klaar om door te gaan met de volgende stap. Als er fouten zijn, kunt u op de compilatie taak klikken voor meer informatie.

## <a name="assign-a-node-configuration"></a>Een knooppuntconfiguratie toewijzen

U kunt een gecompileerde knooppunt configuratie toewijzen aan een DSC-knoop punt. Toewijzing past de configuratie toe op de computer en bewaakt of automatisch corrigeren voor een wille keurige drift van die configuratie.

1. Selecteer in het linkerdeel venster van het Automation-account **State Configuration (DSC)** en klik vervolgens op het tabblad **knoop punten** .
1. Selecteer het knoop punt waaraan u een configuratie wilt toewijzen.
1. Klik op **Een knooppuntconfiguratie toewijzen**.
1. Selecteer de knooppunt configuratie `LAMPServer.localhost` en klik op **OK**. Status configuratie wijst nu de gecompileerde configuratie toe aan het knoop punt en de knooppunt status wordt `Pending`gewijzigd in. Bij de volgende periodieke controle haalt het knoop punt de configuratie op, past deze toe en wordt de status gerapporteerd. Het kan tot 30 minuten duren voordat het knoop punt de configuratie heeft opgehaald, afhankelijk van de instellingen van het knoop punt. 
1. Als u een onmiddellijke controle wilt afdwingen, kunt u de volgende opdracht lokaal uitvoeren op de virtuele Linux-machine: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Een knooppuntconfiguratie toewijzen](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Knooppunt status weer geven

U kunt de status van alle status configuratie-beheerde knoop punten in uw Automation-account weer geven. De informatie wordt weer gegeven door **State Configuration (DSC)** te kiezen en op het tabblad **knoop punten** te klikken. U kunt de weer gave filteren op status, knooppunt configuratie of naam zoeken.

![Status van het DSC-knooppunt](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een virtuele Linux-machine in de configuratie geplaatst, een configuratie voor een LAMP-stack gemaakt en de configuratie geïmplementeerd op de VM. Ga door naar het artikel voor meer informatie over het gebruik van Azure Automation status configuratie om continue implementatie in te scha kelen:

> [!div class="nextstepaction"]
> [Continuous deployment to a VM using DSC and Chocolatey](./automation-dsc-cd-chocolatey.md) (Continue implementatie naar een virtuele machine met behulp van DSC en Chocolatey)

* Zie voor meer informatie over Power shell DSC- [overzicht van Power shell desired state Configuration](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* Zie [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/)voor meer informatie over het beheren van status configuratie vanuit Power shell.
* Zie [DSC-rapportage door sturen naar Azure monitor-logboeken](automation-dsc-diagnostics.md)voor meer informatie over het door sturen van DSC-rapporten naar Azure monitor logboeken voor rapportage en waarschuwingen.