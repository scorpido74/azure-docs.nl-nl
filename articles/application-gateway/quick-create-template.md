---
title: 'Snelstart: webverkeer leiden met een resourcebeheersjabloon'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van een Resource Manager-sjabloon om een Azure Application Gateway te maken die webverkeer naar virtuele machines in een backendpool leidt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0612371605f6b216eadeef49d9adb1497ba31591
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80135970"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Snelstart: direct webverkeer met Azure Application Gateway - Resource Manager-sjabloon

In deze snelstart gebruikt u een resourcebeheersjabloon om een Azure Application Gateway te maken. Vervolgens test u de toepassingsgateway om ervoor te zorgen dat deze correct werkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U deze quickstart ook voltooien met de [Azure-portal,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)of [Azure CLI.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Omwille van de eenvoud, deze sjabloon creëert een eenvoudige setup met een openbare front-end IP, een basislistener voor het hosten van een enkele site op de toepassingsgateway, een basisaanvraag routing regel, en twee virtuele machines in de backend pool.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

Meerdere Azure-bronnen worden gedefinieerd in de sjabloon:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) één voor de toepassingsgateway en twee voor de virtuele machines.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : twee virtuele machines
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : twee voor de virtuele machines
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : IIS en de webpagina's configureren


### <a name="deploy-the-template"></a>De sjabloon implementeren

Resourcebeheersjabloon implementeren in Azure:

1. Selecteer **Implementeren naar Azure** om u aan te melden bij Azure en open de sjabloon. De sjabloon maakt een toepassingsgateway, de netwerkinfrastructuur en twee virtuele machines in de backendpool met IIS.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecteer of maak uw resourcegroep, typ de gebruikersnaam en het wachtwoord van de beheerder van de virtuele machinebeheerder.
3. Selecteer **Ik ga akkoord met de hierboven vermelde algemene voorwaarden** en selecteer Vervolgens **Kopen**. Het kan 20 minuten of langer duren voordat de implementatie is voltooid.

## <a name="validate-the-deployment"></a>De implementatie valideren

Hoewel IIS niet vereist is om de toepassingsgateway te maken, is deze geïnstalleerd om te controleren of Azure de toepassingsgateway heeft gemaakt. Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het openbare IP-adres voor de toepassingsgateway op de **pagina Overzicht.** ![Openbare IP-adres](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) van de toepassingsgateway opnemen Of u **Alle bronnen**selecteren, *myAGPublicIPAddress* invoeren in het zoekvak en het vervolgens selecteren in de zoekresultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het openbare IP-adres en plak het vervolgens in de adresbalk van uw browser om door dat IP-adres te bladeren.
3. Controleer het antwoord. Een geldig antwoord controleert of de toepassingsgateway is gemaakt en kan met de backend verbinding maken.

   ![Toepassingsgateway testen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Vernieuw de browser meerdere keren en u ziet verbindingen met zowel myVM1 als myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de toepassingsgateway hebt gemaakt, niet meer nodig hebt, verwijdert u de brongroep. Hiermee worden de toepassingsgateway en alle bijbehorende bronnen verwijderd.

Als u de brongroep `Remove-AzResourceGroup` wilt verwijderen, roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)
