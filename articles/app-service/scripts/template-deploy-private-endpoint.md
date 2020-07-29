---
title: Met deze Azure Resource Manager-sjabloon kunt u een privé-eindpunt voor uw web-app implementeren.
description: Meer informatie over het gebruik van ARM-sjabloon voor het implementeren van een privé-eindpunt voor uw web-app
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524796"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Een App Service-app maken en een privé-eindpunt implementeren met behulp van Azure Resource Manager-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager-sjabloon om een web-app te maken en deze open te stellen met een privé-eindpunt.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Vereiste

U hebt een Azure-account met een actief abonnement nodig. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

Met deze sjabloon maakt u een privé-eindpunt voor een Azure-web-app.

### <a name="review-the-template"></a>De sjabloon controleren

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>De sjabloon implementeren

Dit is hoe u de Azure Resource Manager-sjabloon implementeert in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u het VNet, de web-app, het privé-eindpunt en de privé-DNS-zone.

   [Implementeren naar Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Selecteer of maak een resourcegroep.
3. Typ de naam van uw web-app, App Service-abonnement en privé-eindpunt in.
5. Lees de algemene voorwaarden. Als u akkoord gaat, selecteert u Ik ga akkoord met de bovenstaande voorwaarden > Kopen. De implementatie kan enkele minuten duren om te voltooien.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met het privé-eindpunt hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u het privé-eindpunt en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

- Meer voorbeelden voor Resource Manager-sjablonen voor Azure App Service Web Apps vindt u in de [voorbeelden van ARM-sjabloon](../samples-resource-manager-templates.md).
