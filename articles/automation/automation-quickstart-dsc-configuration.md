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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457515"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Een virtuele machine configureren met gewenste statusconfiguratie

Door Azure Automation State Configuration in te schakelen, u de configuraties van uw Windows- en Linux-servers beheren en bewaken met behulp van gewenste statusconfiguratie (DSC). Configuraties die afwijken van een gewenste configuratie kunnen worden geïdentificeerd of automatisch worden gecorrigeerd. Deze quickstart bevat stappen voor het onboarden van een virtuele Linux-machine en voor de configuratie van een LAMP-stack met DSC.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/)aan.
* Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](automation-sec-configure-azure-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
* Een virtuele Azure Resource Manager-machine (niet Klassiek) waarop Red Hat Enterprise Linux, CentOS of Oracle Linux wordt uitgevoerd. Zie [Uw eerste virtuele Linux-machine maken met behulp van Azure Portal](../virtual-machines/linux/quick-create-portal.md) voor instructies voor het maken van een VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan https://portal.azure.combij Azure op .

## <a name="onboard-a-virtual-machine"></a>Een virtuele machine onboarden

Er zijn veel verschillende methoden om aan boord van een machine en inschakelen DSC. Deze quickstart bevat informatie over onboarding via een Automation-account. U meer informatie over verschillende methoden om aan boord van uw machines om configuratie te staten door het lezen van de [onboarding](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding) artikel.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Automation-accounts**. Als deze niet zichtbaar is in het linkerdeelvenster, klikt u op **Alle services** en zoekt u in de resulterende weergave.
1. Selecteer een Automation-account in de lijst.
1. Selecteer in het linkerdeelvenster van het Automation-account de optie **Statusconfiguratie (DSC)**.
2. Klik op **Toevoegen** om de selectiepagina van de virtuele machine te openen.
3. Zoek de virtuele machine waarvoor DSC in te schakelen. U kunt het zoekveld en de filteropties gebruiken om een specifieke virtuele machine te vinden.
4. Klik op de virtuele machine en klik vervolgens op **Verbinden**
5. Selecteer de juiste DSC-instellingen voor de virtuele machine. Als u al een configuratie hebt voorbereid, u deze opgeven als `Node Configuration Name`. U kunt de [configuratiemodus](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig) instellen om het configuratiegedrag voor de machine te bepalen.
6. Klik op **OK**. Terwijl de DSC-extensie wordt geïmplementeerd op de virtuele `Connecting`machine, wordt de status weergegeven als .

![De onboarding van virtuele Azure-machine voor DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Modules importeren

Modules bevatten DSC-bronnen en veel zijn te vinden in de [PowerShell Gallery.](https://www.powershellgallery.com) Alle resources die in uw configuraties worden gebruikt, moeten worden geïmporteerd naar het automatiseringsaccount voordat u deze samenstelt. Voor deze zelfstudie is de module met de naam **nx** vereist.

1. Selecteer **modulesgalerie** onder **Gedeelde bronnen**in het linkerdeelvenster van het automatiseringsaccount .
1. Zoek naar de module die u wilt `nx`importeren door een deel van de naam te typen: .
1. Klik op de module om te importeren.
1. Klik **op Importeren**.

![Een DSC-module importeren](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>De configuratie importeren

In deze quickstart wordt gebruikgemaakt van een DSC-configuratie waarmee Apache HTTP Server, MySQL en PHP op de computer worden geconfigureerd. Zie [DSC-configuraties](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

Typ in een teksteditor het volgende en sla deze lokaal op als **AMPServer.ps1**.

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
3. Selecteer het configuratiebestand dat u in de vorige stap hebt opgeslagen.
4. Klik op **OK**.

## <a name="compile-a-configuration"></a>Een configuratie compileren

U moet een DSC-configuratie compileren op een knooppuntconfiguratie (MOF-document) voordat deze aan een knooppunt kan worden toegewezen. Tijdens de compilatie wordt de configuratie gevalideerd en is het mogelijk parameterwaarden in te voeren. Zie [Configuraties compileren in staatsconfiguratie](automation-dsc-compile.md)voor meer informatie over het compileren van een configuratie.

1. Selecteer in het linkerdeelvenster van het automatiseringsaccount **De configuratie (DSC)** en klik vervolgens op het tabblad **Configuraties.**
1. Selecteer de `LAMPServer`configuratie .
1. Selecteer **compileren** in de menuopties en klik op **Ja.**
1. In de configuratieweergave ziet u een nieuwe compilatietaak in de wachtrij staan. Wanneer de taak is voltooid, bent u klaar om door te gaan met de volgende stap. Als er fouten zijn, u op de compilatietaak klikken voor meer informatie.

## <a name="assign-a-node-configuration"></a>Een knooppuntconfiguratie toewijzen

U een gecompileerde knooppuntconfiguratie toewijzen aan een DSC-knooppunt. Assignment past de configuratie toe op de machine en controleert of corrigeert automatisch voor elke afwijking van die configuratie.

1. Selecteer in het linkerdeelvenster van het automatiseringsaccount **De configuratie (DSC)** en klik vervolgens op het tabblad **Knooppunten.**
1. Selecteer het knooppunt waaraan u een configuratie wilt toewijzen.
1. Klik op **Een knooppuntconfiguratie toewijzen**.
1. Selecteer de knooppuntconfiguratie `LAMPServer.localhost` en klik op **OK**. Statusconfiguratie wijst nu de gecompileerde configuratie toe aan `Pending`het knooppunt en de status van het knooppunt wordt gewijzigd in . Bij de volgende periodieke controle haalt het knooppunt de configuratie op, past deze toe en rapporteert de status. Het kan tot 30 minuten duren voordat het knooppunt de configuratie ophaalt, afhankelijk van de instellingen van het knooppunt. 
1. Als u een onmiddellijke controle wilt afdwingen, kunt u de volgende opdracht lokaal uitvoeren op de virtuele Linux-machine: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Een knooppuntconfiguratie toewijzen](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Knooppuntstatus weergeven

U de status van alle door de status configuratie beheerde knooppunten in uw automatiseringsaccount bekijken. De informatie wordt weergegeven door **Statusconfiguratie (DSC)** te kiezen en op het tabblad **Knooppunten** te klikken. U de weergave filteren op status, knooppuntconfiguratie of zoeken naar een naam.

![Status van het DSC-knooppunt](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Linux VM aan de configuratie gekoppeld, een configuratie voor een LAMP-stack gemaakt en de configuratie geïmplementeerd op de VM. Ga naar het artikel voor meer informatie over hoe u Azure Automation State Configuration gebruiken om continue implementatie mogelijk te maken:

> [!div class="nextstepaction"]
> [Continuous deployment to a VM using DSC and Chocolatey](./automation-dsc-cd-chocolatey.md) (Continue implementatie naar een virtuele machine met behulp van DSC en Chocolatey)

* Zie [PowerShell Desired State Configuration Overview](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview)voor meer informatie over PowerShell DSC.
* Zie [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/)voor meer informatie over het beheren van statusconfiguratie vanuit PowerShell.
* Zie [DSC-rapportage doorsturen naar Azure Monitor-logboeken voor het doorsturen van DSC-rapporten naar Azure Monitor-logboeken](automation-dsc-diagnostics.md)voor meer informatie over het doorsturen van DSC-rapporten naar Azure Monitor-logboeken.