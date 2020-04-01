---
title: IoT Central beheren vanuit Azure PowerShell | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassingen maken en beheren vanuit Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 42d853c9cf53c1c6921fbd1816ec2298c9c3583e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365545"
---
# <a name="manage-iot-central-from-azure-powershell"></a>IoT Central beheren vanuit Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de azure [IoT Central-toepassingsbeheerwebsite,](https://aka.ms/iotcentral) u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) gebruiken om uw toepassingen te beheren.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)als u Azure PowerShell liever op uw lokale machine uitvoert. Wanneer u Azure PowerShell lokaal uitvoert, gebruikt u de cmdlet **Connect-AzAccount** om u aan te melden bij Azure voordat u de cmdlets in dit artikel probeert.

> [!TIP]
> Zie [Het actieve abonnement wijzigen](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription)als u uw PowerShell-opdrachten in een ander Azure-abonnement moet uitvoeren.

## <a name="install-the-iot-central-module"></a>De IoT Central-module installeren

Voer de volgende opdracht uit om te controleren of de [IoT Central-module](https://docs.microsoft.com/powershell/module/az.iotcentral/) is geïnstalleerd in uw PowerShell-omgeving:

```powershell
Get-InstalledModule -name Az.I*
```

Als de lijst met geïnstalleerde modules **az.iotCentral**niet bevat, voert u de volgende opdracht uit:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Een app maken

Gebruik de cmdlet [Nieuw-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) om een IoT Central-toepassing in uw Azure-abonnement te maken. Bijvoorbeeld:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Het script maakt eerst een resourcegroep in de regio Oost-VS voor de toepassing. In de volgende tabel worden de parameters beschreven die worden gebruikt met de opdracht **Nieuw-AzIotCentralApp:**

|Parameter         |Beschrijving |
|------------------|------------|
|ResourceGroupName |De resourcegroep die de toepassing bevat. Deze brongroep moet al in uw abonnement bestaan. |
|Locatie |Standaard gebruikt deze cmdlet de locatie uit de resourcegroep. Momenteel u een IoT Central-toepassing maken in de regio's **Australië,** **Azië-Pacific,** **Europa,** **de Verenigde Staten,** **het Verenigd Koninkrijk**en **Japan.** |
|Name              |De naam van de toepassing in de Azure-portal. |
|Subdomein         |Het subdomein in de URL van de toepassing. In het voorbeeld is `https://mysubdomain.azureiotcentral.com`de URL van de toepassing . |
|Sku               |Momenteel u gebruik maken van **ST1** of **ST2**. Zie [Azure IoT Central-prijzen](https://azure.microsoft.com/pricing/details/iot-central/). |
|Template          | De toepassingssjabloon die u wilt gebruiken. Zie de volgende tabel voor meer informatie. |
|DisplayName       |De naam van de toepassing zoals weergegeven in de gebruikersinterface. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Uw IoT Central-toepassingen bekijken

Gebruik de [cmdlet Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) om uw IoT Central-toepassingen weer te geven en metadata weer te geven.

## <a name="modify-an-application"></a>Een toepassing wijzigen

Gebruik de [cmdlet Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) om de metadata van een IoT Central-toepassing bij te werken. U bijvoorbeeld de weergavenaam van uw toepassing wijzigen:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Gebruik de cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) om een IoT Central-toepassing te verwijderen. Bijvoorbeeld:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen vanuit Azure PowerShell beheren, volgt hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
