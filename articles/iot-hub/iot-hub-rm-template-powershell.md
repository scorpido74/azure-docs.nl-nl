---
title: Een Azure IoT-hub maken met een sjabloon (PowerShell) | Microsoft Documenten
description: Een Azure Resource Manager-sjabloon gebruiken om een IoT-hub met Azure PowerShell te maken.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976612"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Een IoT-hub maken met Azure Resource Manager-sjabloon (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Meer informatie over het gebruik van een Azure Resource Manager-sjabloon om een IoT-hub en een consumentengroep te maken. Resource Manager-sjablonen zijn JSON-bestanden die de resources definiëren die u voor uw oplossing moet implementeren. Zie Azure Resource Manager-documentatie voor meer informatie over het ontwikkelen van Resource [Manager-sjablonen.](https://docs.microsoft.com/azure/azure-resource-manager/)

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

De sjabloon Resourcebeheer die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Hier is een kopie van de sjabloon:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

De sjabloon maakt een Azure Iot-hub met drie eindpunten (eventhub, cloud-to-device en messaging) en een consumentengroep. Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)voor meer sjabloonvoorbeelden. Het iot Hub-sjabloonschema is [hier](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)te vinden.

Er zijn verschillende methoden voor het implementeren van een sjabloon.  U gebruikt Azure PowerShell in deze zelfstudie.

Als u het PowerShell-script wilt uitvoeren, selecteert **u Proberen** om de Azure Cloud-shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u Plakken:

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

Zoals u zien in het PowerShell-script, is de gebruikte sjabloon afkomstig van Azure Quickstart-sjablonen. Als u uw eigen bestand wilt gebruiken, moet u het sjabloonbestand eerst uploaden naar de cloudshell en vervolgens de switch gebruiken om de `-TemplateFile` bestandsnaam op te geven.  Zie De [sjabloon implementeren](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)voor een voorbeeld .

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT-hub hebt geïmplementeerd met behulp van een Azure Resource Manager-sjabloon, u verder verkennen:

* Lees meer over de mogelijkheden van de REST API van [IoT Hub-bronprovider][lnk-rest-api].
* Lees [het overzicht van Azure Resource Manager][lnk-azure-rm-overview] voor meer informatie over de mogelijkheden van Azure Resource Manager.
* Zie [Microsoft.Devices-brontypen](/azure/templates/microsoft.devices/iothub-allversions)voor de syntaxis en eigenschappen van JSON in sjablonen.

Zie de volgende artikelen voor meer informatie over het ontwikkelen voor IoT Hub:

* [Inleiding tot C SDK][lnk-c-sdk]
* [SDK’s voor Azure IoT][lnk-sdks]

Zie:

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
