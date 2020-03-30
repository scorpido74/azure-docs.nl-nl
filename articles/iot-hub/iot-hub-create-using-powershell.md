---
title: Een Azure IoT-hub maken met een PowerShell-cmdlet | Microsoft Documenten
description: Meer informatie over het gebruik van de PowerShell-cmdlets om een resourcegroep te maken en vervolgens een IoT-hub in de resourcegroep te maken. Lees ook hoe u de hub verwijdert.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890593"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Maak een IoT-hub met de nieuwe AzIotHub-cmdlet

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Inleiding

U Azure PowerShell-cmdlets gebruiken om Azure IoT-hubs te maken en te beheren. In deze zelfstudie ziet u hoe u een IoT-hub maakt met PowerShell.

Om deze how-to te voltooien, hebt u een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement

Als u de Cloud Shell gebruikt, bent u al aangemeld bij uw abonnement. Als u PowerShell lokaal uitvoert, voert u de volgende opdracht in om u aan te melden bij uw Azure-abonnement:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

U hebt een resourcegroep nodig om een IoT-hub te implementeren. U een bestaande resourcegroep gebruiken of een nieuwe groep maken.

Als u een resourcegroep voor uw IoT-hub wilt maken, gebruikt u de opdracht [Nieuw-AzResourceGroep.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) In dit voorbeeld wordt een resourcegroep genaamd **MyIoTRG1** in de regio **Oost-VS:**

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Als u een IoT-hub wilt maken in de resourcegroep die u in de vorige stap hebt gemaakt, gebruikt u de opdracht [Nieuw-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) In dit voorbeeld wordt een **S1-hub** gemaakt met de naam **MyTestIoTHub** in de regio **Oost-VS:**

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

De naam van de IoT-hub moet wereldwijd uniek zijn.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

U alle IoT-hubs in uw abonnement aanbieden met de opdracht [Get-AzIotHub:](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)

```azurepowershell-interactive
Get-AzIotHub
```

In dit voorbeeld wordt de S1 Standard IoT Hub weergegeven die u in de vorige stap hebt gemaakt.

U de IoT-hub verwijderen met de opdracht [Remove-AzIotHub:](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

U ook een resourcegroep en alle bronnen die deze bevat verwijderen met de opdracht [Verwijderen-AzResourceGroep:](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT-hub hebt geïmplementeerd met een PowerShell-cmdlet, raadpleegt u de volgende artikelen als u verder wilt verkennen:

* [PowerShell-cmdlets voor het werken met uw IoT-hub.](https://docs.microsoft.com/powershell/module/az.iothub/)

* [REST API voor IoT Hub-bronprovider](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Zie de volgende artikelen voor meer informatie over het ontwikkelen voor IoT Hub:

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK’s voor Azure IoT](iot-hub-devguide-sdks.md)

Zie:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
