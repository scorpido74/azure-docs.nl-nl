---
title: Een Linux-vm maken in Azure op basis van een sjabloon
description: De Azure CLI gebruiken om een Linux-VM te maken op basis van een Resource Manager-sjabloon
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969529"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Een virtuele Linux-machine maken met Azure Resource Manager-sjablonen

Meer informatie over het maken van een Virtuele Linux-machine (VM) met behulp van een Azure Resource Manager-sjabloon en de Azure CLI vanuit de Azure Cloud-shell. Zie [Een virtuele Windows-machine maken op basis van een sjabloon ResourceManager](../windows/ps-template.md)als u een virtuele windows-machine wilt maken.

## <a name="templates-overview"></a>Overzicht van sjablonen

Azure Resource Manager-sjablonen zijn JSON-bestanden die de infrastructuur en configuratie van uw Azure-oplossing definiëren. Door het gebruik van een sjabloon kunt u gedurende de levenscyclus de oplossing herhaaldelijk implementeren en erop vertrouwen dat uw resources consistent worden geïmplementeerd. Zie [Quickstart: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)voor meer informatie over de indeling van de sjabloon en hoe u deze maakt. Als u de JSON-syntaxis voor resourcetypen wilt bekijken, raadpleegt u [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Resources definiëren in Azure Resource Manager-sjablonen).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Het maken van een virtuele Azure-machine bevat meestal twee stappen:

1. Maak een resourcegroep. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt.
1. Hiermee maakt u een virtuele machine.

In het volgende voorbeeld wordt een VM gemaakt op basis van een [Azure Quickstart-sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Alleen SSH-verificatie is toegestaan voor deze implementatie. Geef de waarde van uw eigen SSH-openbare sleutel op, zoals de inhoud van *~/.ssh/id_rsa.pub*. Als u een SSH-sleutelpaar wilt maken, raadpleegt u [Hoe u een SSH-sleutelpaar voor Linux-VM's in Azure maken en gebruiken.](mac-create-ssh-keys.md) Hier is een kopie van de sjabloon:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Als u het CLI-script wilt uitvoeren, selecteert **u Proberen** om de Azure Cloud-shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken:**

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

De laatste opdracht Azure CLI toont het openbare IP-adres van de nieuw gemaakte VM. U hebt het openbare IP-adres nodig om verbinding te maken met de virtuele machine. Zie het volgende gedeelte van dit artikel.

In het vorige voorbeeld hebt u een sjabloon opgegeven die is opgeslagen in GitHub. U ook een sjabloon downloaden of maken `--template-file` en het lokale pad met de parameter opgeven.

Hier volgen enkele aanvullende bronnen:

- Zie [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [Azure-sjabloonverwijzing](/azure/templates/microsoft.compute/allversions)voor de programma's voor virtuele machines van Azure.
- Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)voor meer voorbeelden van virtuele machines.

## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

U dan SSH naar uw VM als normaal. Geef u een eigen openbaar IP-adres op van de vorige opdracht:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Volgende stappen

In dit voorbeeld hebt u een eenvoudige Linux-VM gemaakt. Blader door de [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)voor meer Resource Manager-sjablonen die toepassingsframeworks bevatten of complexere omgevingen maken.

Voor meer informatie over het maken van sjablonen u de syntaxis en eigenschappen van JSON weergeven voor de resourcestypen die u hebt geïmplementeerd:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
