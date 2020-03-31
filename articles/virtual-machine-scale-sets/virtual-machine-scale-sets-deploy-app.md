---
title: Een toepassing implementeren op een Azure-schaalset voor virtuele machines
description: Meer informatie over het implementeren van toepassingen naar linux- en Windows-exemplaren van virtuele machines in een schaalset
author: cynthn
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 6bc319ea50da4ff6a654b2c9ab09bbe218695533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278108"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Deploy your application on virtual machine scale sets (Uw toepassing implementeren op virtuele-machineschaalsets)

Als u toepassingen wilt uitvoeren op de exemplaren van een virtuele machine (VM) in een schaalset, moet u eerst de toepassingsonderdelen en de vereiste bestanden installeren. In dit artikel worden manieren geïntroduceerd om een aangepaste VM-afbeelding te maken voor instanties in een schaalset of om automatisch installatiescripts uit te voeren op bestaande VM-exemplaren. U leert ook hoe u toepassings- of OS-updates beheert in een schaalset.


## <a name="build-a-custom-vm-image"></a>Een aangepaste VM-afbeelding maken
Wanneer u een van de Azure-platformafbeeldingen gebruikt om de exemplaren in uw schaalset te maken, wordt er geen extra software geïnstalleerd of geconfigureerd. U de installatie van deze componenten automatiseren, maar dat draagt bij aan de tijd die nodig is om VM-exemplaren in te richten op uw schaalsets. Als u veel configuratiewijzigingen toepast op de VM-exemplaren, is er beheeroverhead met deze configuratiescripts en -taken.

Als u het configuratiebeheer en de tijd voor het inrichten van een VM wilt verkorten, u een aangepaste VM-afbeelding maken die klaar is om uw toepassing uit te voeren zodra een instantie is ingericht in de schaalset. Zie de volgende zelfstudies voor meer informatie over het maken en gebruiken van een aangepaste VM-afbeelding met een schaalset:

- [Azure-CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Een app installeren met de aangepaste scriptextensie
Met de aangepaste scriptextensie kunnen scripts worden gedownload en uitgevoerd op virtuele machines in Azure. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. Zie de volgende zelfstudies voor meer informatie over het installeren van een app met een aangepaste scriptextensie:

- [Azure-CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sjabloon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Een app installeren op een Windows-vm met PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) is een beheerplatform om de configuratie van doelmachines te definiëren. DSC-configuraties definiëren wat u op een machine moet installeren en hoe u de host configureert. Een LCM-engine (Local Configuration Manager) draait op elk doelknooppunt dat gevraagde acties verwerkt op basis van gepushte configuraties.

Met de PowerShell DSC-extensie u VM-exemplaren aanpassen in een schaalset met PowerShell. Het volgende voorbeeld:

- Instrueert de VM-exemplaren om een DSC-pakket van GitHub te downloaden -*https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Stelt de extensie in om een installatiescript uit te voeren -`configure-http.ps1`
- Krijgt informatie over een schaalset met [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Past de extensie toe op de [VM-exemplaren met Update-AzVmss](/powershell/module/az.compute/update-azvmss)

De DSC-extensie wordt toegepast op de exemplaren *van de myScaleSet* VM in de resourcegroep *myResourceGroup*. Voer als volgt uw eigen namen in:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Als het upgradebeleid op uw schaalset *handmatig*is, werkt u uw VM-exemplaren bij met [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Deze cmdlet past de bijgewerkte schaalsetconfiguratie toe op de VM-exemplaren en installeert uw toepassing.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Een app installeren op een Linux-vm met cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Als de initialisatie van de cloud-init wordt uitgevoerd tijdens het opstartproces, zijn er geen extra stappen of agents vereist om uw configuratie toe te passen.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

Zie [Cloud-init gebruiken om Azure VM's aan te passen](../virtual-machines/linux/using-cloud-init.md)voor meer informatie, waaronder een voorbeeld *cloud-init.txt-bestand.*

Als u een schaalset wilt maken en een `--custom-data` cloud-init-bestand wilt gebruiken, voegt u de parameter toe aan de opdracht [az vmss en](/cli/azure/vmss) geeft u de naam op van een cloud-init-bestand. In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* in *myResourceGroup* geconfigureerd en worden VM-exemplaren geconfigureerd met een bestand met de naam *cloud-init.txt*. Voer als volgt uw eigen namen in:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Toepassingen installeren met OS-updates
Wanneer er nieuwe OS-releases beschikbaar zijn, u een nieuwe aangepaste afbeelding gebruiken of bouwen en [OS-upgrades implementeren](virtual-machine-scale-sets-upgrade-scale-set.md) in een schaalset. Elke VM-instantie wordt geüpgraded naar de nieuwste afbeelding die u opgeeft. U een aangepaste afbeelding gebruiken met de vooraf geïnstalleerde toepassing, de aangepaste scriptextensie of PowerShell DSC om uw toepassing automatisch beschikbaar te hebben terwijl u de upgrade uitvoert. Mogelijk moet u het onderhoud van toepassingen plannen terwijl u dit proces uitvoert om ervoor te zorgen dat er geen compatibiliteitsproblemen met de versie zijn.

Als u een aangepaste VM-afbeelding gebruikt waarbij de toepassing vooraf is geïnstalleerd, u de toepassingsupdates integreren met een implementatiepijplijn om de nieuwe afbeeldingen te bouwen en OS-upgrades in de schaalset te implementeren. Met deze aanpak kan de pijplijn de nieuwste toepassingsbuilds ophalen, een VM-afbeelding maken en valideren en vervolgens de VM-exemplaren in de schaalset upgraden. Als u een implementatiepijplijn wilt uitvoeren die toepassingsupdates voor aangepaste VM-afbeeldingen bouwt en implementeert, u [een Packer-afbeelding maken en implementeren met Azure DevOps-services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)of een ander platform gebruiken, zoals [Spinnaker](https://www.spinnaker.io/) of [Jenkins.](https://jenkins.io/)


## <a name="next-steps"></a>Volgende stappen
Terwijl u toepassingen bouwt en implementeert naar uw schaalsets, u het [ontwerpoverzicht schaalset bekijken.](virtual-machine-scale-sets-design-overview.md) Zie [PowerShell gebruiken om uw schaalset te beheren voor](virtual-machine-scale-sets-windows-manage.md)meer informatie over het beheren van uw schaalset.
