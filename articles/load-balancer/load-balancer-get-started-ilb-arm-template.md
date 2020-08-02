---
title: Een interne Load Balancer maken-Azure-sjabloon
titleSuffix: Azure Load Balancer
description: Meer informatie over hoe u met een sjabloon een interne load balancer maakt in Resource Manager
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: fc64f4b3d338704568cb70a542a82fe58d08d4d9
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502304"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Een interne load balancer maken met behulp van een sjabloon

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure-CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Sjabloon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>De sjabloon implementeren met Klik om te implementeren

De voorbeeldsjabloon in de openbare opslagplaats maakt gebruik van een parameterbestand dat de standaardwaarden bevat voor het genereren van het hierboven beschreven scenario. Als u deze sjabloon wilt implementeren met behulp van Klik om te implementeren, volgt u [deze koppeling](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer). Klik op **Distribueren naar Azure**, vervang indien nodig de standaardparameterwaarden en volg de instructies in de portal.

## <a name="deploy-the-template-by-using-powershell"></a>De sjabloon implementeren met PowerShell

Volg onderstaande stappen als u de sjabloon die u hebt gedownload, wilt implementeren met PowerShell.

1. Als u Azure PowerShell nooit hebt gebruikt, raadpleegt u [Azure PowerShell installeren en configureren](/powershell/azure/) en volgt u de instructies helemaal tot aan het einde om u aan te melden bij Azure en uw abonnement te selecteren.
2. Download het parameterbestand naar de lokale schijf.
3. Bewerk het bestand en sla het op.
4. Voer de cmdlet **New-AzResourceGroupDeployment** uit om een resource groep te maken met behulp van de sjabloon.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>De sjabloon implementeren met de Azure CLI

Volg onderstaande stappen als u de sjabloon wilt implementeren met de Azure CLI.

1. Als u Azure CLI nog nooit hebt gebruikt, raadpleegt u [de Azure cli installeren en configureren](../cli-install-nodejs.md) en volgt u de instructies tot het punt waar u uw Azure-account en-abonnement selecteert.
2. Ga naar [https://shell.azure.com](https://shell.azure.com) om Cloud Shell in uw browser te openen. Voer de opdracht **azure config mode** uit om over te schakelen naar de modus Resource Manager, zoals hieronder weergegeven.

    ```console
    azure config mode arm
    ```

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

    ```output
    info:    New mode is arm
    ```

3. Open het parameterbestand, selecteer de inhoud en sla deze op in een bestand op uw computer. Hier is het parameterbestand opgeslagen als *parameters.json*.
4. Voer de opdracht **azure group deployment create** uit om de nieuwe interne load balancer te implementeren met behulp van de sjabloon en de parameterbestanden die u hebt gedownload en hierboven hebt gewijzigd. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.

    ```console
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>Volgende stappen

[Een distributiemodus voor de load balancer configureren met bron-IP-affiniteit](load-balancer-distribution-mode.md)

[TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)

Zie [micro soft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)voor de JSON-syntaxis en-eigenschappen van een Load Balancer in een sjabloon.
