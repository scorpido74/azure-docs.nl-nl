---
title: Apparaatconnectiviteit bewaken met de Azure IoT Central Explorer
description: Monitor apparaatberichten en observeer apparaatwijzigingen via de IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 0fa1b58547e0260ec5486003cf4cb85d855de33a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310219"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Apparaatconnectiviteit bewaken met Azure CLI

*Dit onderwerp is van toepassing op bouwers en beheerders.*

Gebruik de Azure CLI IoT-extensie om berichten te zien die uw apparaten naar IoT Central verzenden en wijzigingen in de apparaattweeling te observeren. U deze tool gebruiken om de apparaatconnectiviteit te debuggen en te observeren en problemen te diagnosticeren van apparaatberichten die de cloud niet bereiken of apparaten die niet reageren op dubbele wijzigingen.

[Ga naar de referentie voor Azure CLI-extensies voor meer informatie](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Vereisten

+ Azure CLI geïnstalleerd en is versie 2.0.7 of hoger. Controleer de versie van uw `az --version`Azure CLI door het uitvoeren van . Meer informatie over het installeren en bijwerken van de [Azure CLI-documenten](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Een werk- of schoolaccount in Azure, toegevoegd als gebruiker in een IoT Central-toepassing.

## <a name="install-the-iot-central-extension"></a>De IoT Central-extensie installeren

Voer de volgende opdracht uit vanaf de opdrachtregel om te installeren:

```azurecli
az extension add --name azure-iot
```

Controleer de versie van de extensie door het uitvoeren van:

```azurecli
az --version
```

U moet zien dat de azure-iot-extensie 0,8.1 of hoger is. Als dit niet het is, voert u het als:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>De extensie gebruiken

In de volgende secties worden veelvoorkomende opdrachten en `az iot central`opties beschreven die u gebruiken wanneer u. Als u de volledige set opdrachten `--help` en `az iot central` opties wilt bekijken, geeft u de opdracht of een van de subopdrachten door.

### <a name="login"></a>Aanmelden

Begin met aanmelden bij de Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>De applicatie-id van uw IoT Central-app downloaden
Kopieer **in administratie-/toepassingsinstellingen**de **toepassings-id**. U gebruikt deze waarde in latere stappen.

### <a name="monitor-messages"></a>Berichten controleren
Controleer de berichten die vanaf uw apparaten naar uw IoT Central-app worden verzonden. De uitvoer omvat alle kop- en annotaties.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Apparaateigenschappen weergeven
Bekijk de huidige eigenschappen van het lees- en lees-/schrijfapparaat voor een bepaald apparaat.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de IoT Central Explorer gebruiken, is de voorgestelde volgende stap het [verkennen van het beheer van apparaten IoT Central.](howto-manage-devices.md)
