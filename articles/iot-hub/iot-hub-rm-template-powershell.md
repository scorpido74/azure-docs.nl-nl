---
title: Een Azure-IoT Hub maken met behulp van een sjabloon (Power shell) | Microsoft Docs
description: Een Azure Resource Manager sjabloon gebruiken om een IoT Hub met Azure PowerShell te maken.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75976612"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Een IoT-hub maken met Azure Resource Manager-sjabloon (Power shell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Meer informatie over het gebruik van een Azure Resource Manager sjabloon voor het maken van een IoT Hub en een consumenten groep. Resource Manager-sjablonen zijn JSON-bestanden die de resources definiëren die u voor uw oplossing moet implementeren. Zie [Azure Resource Manager-documentatie](https://docs.microsoft.com/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

De Resource Manager-sjabloon die in deze Quick Start wordt gebruikt, is van [Azure Quick](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)start-sjablonen. Hier volgt een kopie van de sjabloon:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Met de sjabloon wordt een Azure IOT-hub gemaakt met drie eind punten (eventhub, Cloud-naar-apparaat en Messa ging) en een consumenten groep. Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)start-sjablonen voor meer voor beelden van sjablonen. Het sjabloon schema voor IOT Hub vindt u [hier](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Er zijn verschillende methoden voor het implementeren van een sjabloon.  In deze zelf studie gebruikt u Azure PowerShell.

Als u het Power shell-script wilt uitvoeren, selecteert u **proberen** om de Azure Cloud shell te openen. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens plakken:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Zoals u kunt zien in het Power shell-script, is de sjabloon die wordt gebruikt van Azure Quick Start-sjablonen. Als u uw eigen wilt gebruiken, moet u eerst het sjabloon bestand uploaden naar de Cloud shell en vervolgens de `-TemplateFile` Schakel optie gebruiken om de bestands naam op te geven.  Zie [de sjabloon implementeren](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)voor een voor beeld.

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT-hub hebt geïmplementeerd met behulp van een Azure Resource Manager-sjabloon, kunt u het beste verder verkennen:

* Meer informatie over de mogelijkheden van de [IOT hub resource provider rest API][lnk-rest-api].
* Lees [Azure Resource Manager overzicht][lnk-azure-rm-overview] voor meer informatie over de mogelijkheden van Azure Resource Manager.
* Zie [resource typen van micro soft. devices](/azure/templates/microsoft.devices/iothub-allversions)voor de JSON-syntaxis en-eigenschappen die in sjablonen moeten worden gebruikt.

Raadpleeg de volgende artikelen voor meer informatie over het ontwikkelen van IoT Hub:

* [Inleiding tot C SDK][lnk-c-sdk]
* [SDK's voor Azure IoT][lnk-sdks]

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [AI implementeren op Edge-apparaten met Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
