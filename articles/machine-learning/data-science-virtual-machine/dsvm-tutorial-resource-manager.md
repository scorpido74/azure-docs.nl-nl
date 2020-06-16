---
title: 'Quickstart: Een Data Science VM maken met behulp van een Resource Manager-sjabloon'
titleSuffix: Azure Data Science Virtual Machine
description: In deze quickstart gebruikt u een Azure Resource Manager-sjabloon om snel een Data Science Virtual Machine te implementeren.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: e89f3d85156081106f4e1dbb55ee6c895e7e7a2d
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636747"
---
# <a name="tutorial-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>Zelfstudie: Een Data Science Virtual Machine maken in Ubuntu met behulp van een Resource Manager-sjabloon
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze quickstart ziet u hoe u een Data Science Virtual Machine maakt voor Ubuntu 18.04 met behulp van een Azure Resource Manager-sjabloon. Data Science Virtual Machines zijn virtuele machines in de cloud waarop vooraf een reeks frameworks en hulpprogramma's voor gegevenswetenschappen en machine learning wordt geladen. Wanneer de VM wordt geïmplementeerd op rekenresources met een GPU, worden alle hulpprogramma's en bibliotheken geconfigureerd voor het gebruik van de GPU. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree) voordat u begint.

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Een werkruimte maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/). De volledige sjabloon voor dit artikel is te lang om hier weer te geven. Als u de volledige sjabloon wilt bekijken, raadpleegt u [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json). Het gedeelte waarin de specifieke kenmerken van de DSVM wordt gedefinieerd, ziet u hier:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

De volgende resources zijn gedefinieerd in de sjabloon:

* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Maak een virtuele machine in de cloud. In deze sjabloon wordt de virtuele machine geconfigureerd als een Data Science Virtual Machine met Ubuntu 18.04.

### <a name="deploy-the-template"></a>De sjabloon implementeren 

Als u de sjabloon uit de Azure CLI wilt gebruiken, meldt u zich aan en kiest u uw abonnement (Zie [Aanmelden met Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Voer vervolgens

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey && 
echo "Press [ENTER] to continue ..." &&
read
```

Wanneer u de bovenstaande opdracht uitvoert, voert u het volgende in:

1. De naam van de resourcegroep die u wilt maken voor het opslaan van de DSVM en de bijbehorende resources 
1. De Azure-locatie waarin u de implementatie wilt uitvoeren
1. Het type verificatie dat u wilt gebruiken (voer de tekenreeks `password` of `sshPublicKey` in)
1. De aanmeldingsnaam van het beheerdersaccount (deze waarde mag niet `admin` zijn)
1. De waarde van het wachtwoord of de openbare SSH-sleutel voor het account

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Uw Data Science Virtual Machine weergeven:

1. Ga naar https://portal.azure.com 
1. Aanmelden 
1. Kies de resourcegroep die u net hebt gemaakt

U ziet de gegevens van de resourcegroep: 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Schermopname van een basisresource groep met een DSVM":::

Klik op de resource van de virtuele machine om naar de pagina met gegevens van de VM te gaan. Hier vindt u informatie over de virtuele machine, waaronder verbindingsgegevens. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze virtuele machine wilt gebruiken, verwijdert u deze. Aangezien de DSVM is gekoppeld aan andere resources, zoals een opslagaccount, wilt u waarschijnlijk de gehele resourcegroep verwijderen die u hebt gemaakt. U kunt de resourcegroep verwijderen via de portal door op de knop Verwijderen te klikken en te bevestigen. U kunt de resourcegroep ook uit de CLI verwijderen met: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Data Science Virtual Machine gemaakt op basis van een Azure Resource Manager-sjabloon. 

> [!div class="nextstepaction"]
> [Voorbeelden van programma's en ML-scenario's](dsvm-samples-and-walkthroughs.md)
