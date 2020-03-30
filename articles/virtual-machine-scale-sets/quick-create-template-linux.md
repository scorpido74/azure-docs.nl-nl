---
title: Snelstart - Een Linux-sjabloon voor virtuele machines maken met een Azure-sjabloon
description: Leer hoe u snel een virtuele-machineschaalset in Linux maakt met behulp van een Azure Resource Manager-sjabloon waarmee een voorbeeld-app wordt geïmplementeerd en regels voor automatisch schalen worden geconfigureerd
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: a2712bc4a758a0cac6fe8357a0d4c14c594978c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76279174"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Snelstartgids: Een schaalset voor virtuele Linux-machines maken met een Azure-sjabloon
Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gebruikt u een Azure Resource Manager-sjabloon om een schaalset voor virtuele machines te maken en een voorbeeldtoepassing te implementeren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.29 of hoger gebruiken. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).


## <a name="define-a-scale-set-in-a-template"></a>Een schaalset definiëren in een sjabloon
Met Azure Resource Manager-sjablonen kunt u groepen gerelateerde resources implementeren. Sjablonen zijn geschreven in JSON (JavaScript Object Notation). Deze definiëren de hele omgeving van de Azure-infrastructuur voor uw toepassing. In één sjabloon kunt u de virtuele-machineschaalset maken, toepassingen installeren en regels voor automatisch schalen configureren. Met behulp van variabelen en parameters kan deze sjabloon opnieuw worden gebruikt voor het bijwerken van bestaande schaalsets, of om extra schaalsets te maken. U kunt sjablonen implementeren via de Azure-portal, Azure CLI of Azure PowerShell of via CI/CD-pijplijnen (Continuous Integration/Continuous Delivery).

Zie [overzicht van Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process)voor meer informatie over sjablonen. Zie de sjabloonverwijzing [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets) voor de JSON-syntaxis en eigenschappen.

Als u een schaal wilt maken met behulp van een sjabloon, definieert u de juiste resources. De basisonderdelen van het resourcetype voor de virtuele-machineschaalset zijn:

| Eigenschap                     | Beschrijving van eigenschap                                  | Voorbeeldwaarde van sjabloon                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Azure-resourcetype dat moet worden gemaakt                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | De naam van de schaalset                                       | myScaleSet                                |
| location                     | De locatie voor het maken van de schaalset                     | VS - oost                                   |
| sku.name                     | De VM-grootte voor elke instantie van de schaalset                  | Standard_A1                               |
| sku.capacity                 | Het aantal VM-instanties dat in het begin moet worden gemaakt           | 2                                         |
| upgradePolicy.mode           | Upgrademodus voor VM-instantie wanneer er wijzigingen optreden              | Automatisch                                 |
| imageReference               | Het platform of de aangepaste afbeelding die moet worden gebruikt voor de VM-instanties | Canonical Ubuntu-server 16.04-LTS         |
| osProfile.computerNamePrefix | Het naamvoorvoegsel voor elke VM-instantie                     | myvmss                                    |
| osProfile.adminUsername      | De gebruikersnaam voor elke VM-instantie                        | azureuser                                 |
| osProfile.adminPassword      | Het wachtwoord voor elke VM-instantie                        | P@ssw0rd!                                 |

 In het volgende voorbeeld ziet u de resourcedefinitie voor een basisschaalset. Als u een schaalsetsjabloon wilt aanpassen, kunt u de VM-grootte of initiële capaciteit wijzigen, of een ander platform of aangepaste afbeelding gebruiken.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 De virtuele NIC-configuratie (Network Interface Card) wordt niet weergegeven om het voorbeeld kort te houden. Extra onderdelen, zoals een load balancer, worden ook niet weergegeven. [Aan het eind van dit artikel](#deploy-the-template) wordt een volledige schaalsetsjabloon weergegeven.


## <a name="add-a-sample-application"></a>Voorbeeldtoepassing toevoegen
Als u de schaalset wilt testen, installeert u een eenvoudige webtoepassing. Wanneer u een schaalset implementeert, kunnen VM-extensies worden gebruikt voor configuratie- en automatiseringstaken na de implementatie, zoals het installeren van een app. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. Als u een extensie wilt toepassen op uw schaalset, voegt u de sectie *extensionProfile* toe aan het voorgaande resourcevoorbeeld. Met het extensieprofiel worden doorgaans de volgende eigenschappen gedefinieerd:

- Type extensie
- Uitgever van de extensie
- Versie van de extensie
- Locatie van de configuratie of installatiescripts
- Opdrachten om uit te voeren op de VM-instanties

De sjabloon [Python HTTP-server in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) maakt gebruik van de aangepaste scriptextensie om [Bottle](https://bottlepy.org/docs/dev/), een Python-webframework, en een eenvoudige HTTP-server te installeren. 

Twee scripts worden gedefinieerd in **fileUris** - *installserver.sh*en *workserver.py*. Deze bestanden worden gedownload uit GitHub. Vervolgens wordt `bash installserver.sh` uitgevoerd met *commandToExecute* om de app te installeren en configureren:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>De sjabloon implementeren
U kunt de sjabloon [Python HTTP-server in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) implementeren met de volgende knop **Implementeren in Azure**. Met deze knop wordt Azure Portal geopend, de volledige sjabloon geladen, en wordt u gevraagd om een aantal parameters op te geven, zoals een naam voor de schaalset, het aantal instanties en de beheerdersreferenties.

[![Sjabloon implementeren in Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

U kunt ook Azure CLI gebruiken om de Python HTTP-server te installeren in Linux met [az group deployment create](/cli/azure/group/deployment). Dit doet u als volgt:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Geef de waarden op voor de naam van de schaalset, het aantal instanties en de beheerdersreferenties voor de VM-instanties, wanneer u hierom wordt gevraagd. Het duurt enkele minuten voordat de schaalset en ondersteunende resources zijn gemaakt.


## <a name="test-your-scale-set"></a>Uw schaalset testen
U kunt de schaalset in actie zien door in een webbrowser naar de voorbeeldwebtoepassing te gaan. Vraag als volgt het openbare IP-adres van de load balancer op met [az network public-ip list](/cli/azure/network/public-ip):

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Voer het openbare IP-adres van de load balancer in op een webbrowser in de indeling *\/http: /publicIpAddress:9000/do_work*. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

![Standaardwebpagina in NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Resources opschonen
Gebruik [az group delete](/cli/azure/group) als volgt om de resourcegroep, schaalset en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt. De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u een Linux-schaalset gemaakt met behulp van een Azure-sjabloon en de aangepaste scriptextensie gebruikt om een standaard Python-webserver te installeren op de VM-exemplaren. Als u meer wilt weten over schaalsets, gaat u verder met de zelfstudie voor het maken en beheren van schaalsets voor virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Schaalsets voor virtuele Azure-machines maken en beheren](tutorial-create-and-manage-cli.md)
