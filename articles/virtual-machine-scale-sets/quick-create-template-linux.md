---
title: Snelstart - Een computerschaalset voor Linux maken met een Azure Resource Manager-sjabloon
description: Leer hoe u snel een virtuele-machineschaalset in Linux maakt met behulp van een Azure Resource Manager-sjabloon waarmee een voorbeeld-app wordt geïmplementeerd en regels voor automatisch schalen worden geconfigureerd
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 03/27/2020
ms.author: jushiman
ms.openlocfilehash: 4c0bac943be996c02436824334bd79a270f9a2e2
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010457"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-resource-manager-template"></a>Snelstart: een Linux-sjabloon voor virtuele machineschaal maken met een Azure Resource Manager-sjabloon

Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gebruikt u een Azure Resource Manager-sjabloon om een schaalset voor virtuele machines te maken en een voorbeeldtoepassing te implementeren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-a-scale-set"></a>Een schaalset maken

Met Azure Resource Manager-sjablonen kunt u groepen gerelateerde resources implementeren. In één sjabloon kunt u de virtuele-machineschaalset maken, toepassingen installeren en regels voor automatisch schalen configureren. Met behulp van variabelen en parameters kan deze sjabloon opnieuw worden gebruikt voor het bijwerken van bestaande schaalsets, of om extra schaalsets te maken. U kunt sjablonen implementeren via de Azure-portal, Azure CLI of Azure PowerShell of via CI/CD-pijplijnen (Continuous Integration/Continuous Delivery).

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/201-vmss-bottle-autoscale/)

:::code language="json" source="~/quickstart-templates/201-vmss-bottle-autoscale/azuredeploy.json" range="1-330" highlight="176-264":::

Deze bronnen worden gedefinieerd in de sjabloon:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleInstellingen**](/azure/templates/microsoft.insights/autoscalesettings)

#### <a name="define-a-scale-set"></a>Een schaalset definiëren

Het gemarkeerde gedeelte is de definitie van de schaalsetresource. Als u een schaal wilt maken met behulp van een sjabloon, definieert u de juiste resources. De basisonderdelen van het resourcetype voor de virtuele-machineschaalset zijn:

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

Als u een sjabloon voor een schaalset wilt aanpassen, u de vm-grootte of de initiële capaciteit wijzigen. Een andere optie is het gebruik van een ander platform of een aangepaste afbeelding.

#### <a name="add-a-sample-application"></a>Voorbeeldtoepassing toevoegen

Als u de schaalset wilt testen, installeert u een eenvoudige webtoepassing. Wanneer u een schaalset implementeert, kunnen VM-extensies worden gebruikt voor configuratie- en automatiseringstaken na de implementatie, zoals het installeren van een app. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. Als u een extensie wilt toepassen op uw schaalset, voegt u de sectie *extensionProfile* toe aan het voorgaande resourcevoorbeeld. Met het extensieprofiel worden doorgaans de volgende eigenschappen gedefinieerd:

- Type extensie
- Uitgever van de extensie
- Versie van de extensie
- Locatie van de configuratie of installatiescripts
- Opdrachten om uit te voeren op de VM-instanties

De sjabloon maakt gebruik van de Custom Script-extensie om [Bottle,](https://bottlepy.org/docs/dev/)een Python-webframework en een eenvoudige HTTP-server te installeren.

Twee scripts worden gedefinieerd in **fileUris** - *installserver.sh*en *workserver.py*. Deze bestanden worden gedownload van GitHub en `bash installserver.sh` vervolgens *commandToExecute* uitgevoerd om de app te installeren en te configureren.

### <a name="deploy-the-template"></a>De sjabloon implementeren

U de sjabloon implementeren door de volgende knop **Implementeren naar Azure te** selecteren. Met deze knop wordt Azure Portal geopend, de volledige sjabloon geladen, en wordt u gevraagd om een aantal parameters op te geven, zoals een naam voor de schaalset, het aantal instanties en de beheerdersreferenties.

[![Sjabloon implementeren in Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

U ook een resourcemanagersjabloon implementeren met Azure CLI:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Geef de waarden op voor de naam van de schaalset, het aantal instanties en de beheerdersreferenties voor de VM-instanties, wanneer u hierom wordt gevraagd. Het duurt enkele minuten voordat de schaalset en ondersteunende resources zijn gemaakt.

## <a name="test-the-deployment"></a>De implementatie testen

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
