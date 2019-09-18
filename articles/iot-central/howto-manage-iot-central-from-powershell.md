---
title: IoT Central beheren vanuit Azure PowerShell | Microsoft Docs
description: IoT Central beheren vanuit Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: e91d2d487a8b3409c53a0a0731700c39cf56dd63
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066360"
---
# <a name="manage-iot-central-from-azure-powershell"></a>IoT Central beheren vanuit Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , kunt u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) gebruiken om uw toepassingen te beheren.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u liever Azure PowerShell op uw lokale computer wilt uitvoeren, raadpleegt u [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps). Wanneer u Azure PowerShell lokaal uitvoert, gebruikt u de cmdlet **Connect-AzAccount** om u aan te melden bij Azure voordat u de cmdlets in dit artikel probeert uit te voeren.

## <a name="install-the-iot-central-module"></a>De IoT Central-module installeren

Voer de volgende opdracht uit om te controleren of de [IOT Central module](https://docs.microsoft.com/powershell/module/az.iotcentral/) is geïnstalleerd in uw Power shell-omgeving:

```powershell
Get-InstalledModule -name Az.I*
```

Als niet de lijst met geïnstalleerde modules **AZ. IotCentral**bevat, voert u de volgende opdracht uit:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Een app maken

Gebruik de cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) om een IOT Central-toepassing te maken in uw Azure-abonnement. Bijvoorbeeld:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Het script maakt eerst een resource groep in de regio VS-Oost voor de toepassing. In de volgende tabel worden de para meters beschreven die worden gebruikt met de opdracht **New-AzIotCentralApp** :

|Parameter         |Description |
|------------------|------------|
|ResourceGroupName |De resource groep die de toepassing bevat. Deze resource groep moet al bestaan in uw abonnement. |
|Location |Deze cmdlet gebruikt standaard de locatie van de resource groep. Op dit moment kunt u een IoT Central-toepassing maken in de regio **VS-Oost**, VS- **West**, **Europa-Noord**of **Europa-West** regio's of in het **Australia** -geografie. |
|Name              |De naam van de toepassing in de Azure Portal. |
|Subdomein         |Het subdomein in de URL van de toepassing. In het voor beeld is https://mysubdomain.azureiotcentral.com de URL van de toepassing. |
|Sku               |Op dit moment is de enige waarde **S1** (Standard-laag). Zie [prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Template          | De toepassings sjabloon die moet worden gebruikt. Zie de volgende tabel voor meer informatie: |
|DisplayName       |De naam van de toepassing, zoals deze wordt weer gegeven in de gebruikers interface. |

**Toepassings sjablonen**

|Naam van sjabloon  |Description |
|---------------|------------|
|iotc-default@1.0.0 |Hiermee maakt u een lege toepassing die u kunt vullen met uw eigen apparaatsjablonen en apparaten. |
|iotc-demo@1.0.0    |Hiermee maakt u een toepassing die onder andere een vooraf gemaakte apparaatjabloon voor een gekoelde verkoopautomaat bevat. Gebruik deze sjabloon als u wilt beginnen met het verkennen van Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Hiermee maakt u een toepassing met apparaatsjablonen die u in staat stelt verbinding te maken met een MXChip- of Raspberry Pi-apparaat. Gebruik deze sjabloon als u een ontwikkelaar van een apparaat wilt experimenteren met een van deze apparaten. |

> [!NOTE]
> De **Preview-toepassings** sjabloon is momenteel alleen beschikbaar in de regio's **Europa-Noord** en **Centraal VS** .

## <a name="view-your-iot-central-applications"></a>Uw IoT Central-toepassingen weer geven

Gebruik de cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) om uw IOT Central toepassingen te vermelden en meta gegevens weer te geven.

## <a name="modify-an-application"></a>Een toepassing wijzigen

Gebruik de cmdlet [set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) om de meta gegevens van een IOT Central-toepassing bij te werken. Als u bijvoorbeeld de weergave naam van uw toepassing wilt wijzigen:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Gebruik de cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) om een IOT Central-toepassing te verwijderen. Bijvoorbeeld:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen kunt beheren vanuit Azure PowerShell, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
