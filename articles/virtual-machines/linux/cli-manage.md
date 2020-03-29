---
title: Algemene Azure CLI-opdrachten
description: Enkele van de algemene Azure CLI-opdrachten om u aan de slag te helpen bij het beheren van uw VM's in de Azure Resource Manager-modus
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 253f2ab1b192d22f43e4082766adf4ec4f86fe71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969255"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Algemene Azure CLI-opdrachten voor het beheren van Azure-resources

Met de Azure CLI u uw Azure-bronnen maken en beheren op macOS, Linux en Windows. In dit artikel worden enkele van de meest voorkomende opdrachten beschreven om virtuele machines (VM's) te maken en te beheren.

Dit artikel vereist de Azure CLI-versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli)als u wilt upgraden. U [Cloud Shell](/azure/cloud-shell/quickstart) ook gebruiken vanuit uw browser.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Basisopdrachten van Azure Resource Manager in Azure CLI
Voor meer gedetailleerde hulp bij specifieke opdrachtregelswitches en -opties `az <command> <subcommand> --help`u de online opdrachthulp en -opties gebruiken door te typen.

### <a name="create-vms"></a>VM's maken
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Een resourcegroep maken | `az group create --name myResourceGroup --location eastus` |
| Een Linux-VM maken | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Een Windows-VM maken | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>VM-status beheren
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Een VM starten | `az vm start --resource-group myResourceGroup --name myVM` |
| Een VM stoppen | `az vm stop --resource-group myResourceGroup --name myVM` |
| De toewijzing van een VM ongedaan maken | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Een VM opnieuw opstarten | `az vm restart --resource-group myResourceGroup --name myVM` |
| Een virtuele machine opnieuw implementeren | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Een VM verwijderen | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>VM-informatie opvragen
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Lijst met VM's opvragen | `az vm list` |
| Informatie over een VM ophalen | `az vm show --resource-group myResourceGroup --name myVM` |
| Verbruik van VM-resources opvragen | `az vm list-usage --location eastus` |
| Alle beschikbare VM-grootten opvragen | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Schijven en afbeeldingen
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Een gegevensschijf toevoegen aan een VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Een gegevensschijf verwijderen van een VM | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| De grootte van een schijf wijzigen | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Een momentopname maken van een schijf | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Afbeelding van een vm maken | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| VM maken op afbeelding | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Volgende stappen
Zie de [linux-vm's maken en beheren met de azure CLI-zelfstudie](tutorial-manage-vm.md) voor aanvullende voorbeelden van de CLI-opdrachten.



