---
title: Connectiviteit van apparaten bewaken met behulp van Azure IoT Central Explorer
description: Controleer de apparaatgegevens en Bekijk de dubbele wijzigingen van het apparaat via de IoT Central Explorer-CLI.
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 90cf83f86acb647b8194619bc1b572e5147cc0cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434950"
---
# <a name="monitor-device-connectivity-using-azure-cli-preview-features"></a>Connectiviteit van apparaten bewaken met behulp van Azure CLI (preview-functies)

*Dit onderwerp is van toepassing op bouwers en beheerders.*

Gebruik de Azure CLI IoT-extensie om berichten te bekijken die uw apparaten naar IoT Central verzenden en wijzigingen in het apparaat te observeren. U kunt dit hulp programma gebruiken om de connectiviteit van apparaten op te sporen en te controleren op problemen met de toegang tot de Cloud of apparaten die niet reageren op twee wijzigingen.

[Bezoek de naslag informatie voor Azure CLI-extensies voor meer informatie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Vereisten

+ Azure CLI is geïnstalleerd en versie 2.0.7 of hoger. Controleer de versie van uw Azure CLI door `az --version`uit te voeren. Meer informatie over het installeren en bijwerken van de [Azure cli-documenten](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Een werk-of school account in azure, toegevoegd als een gebruiker in een IoT Central-toepassing.

## <a name="install-the-iot-central-extension"></a>De IoT Central-extensie installeren

Voer de volgende opdracht uit vanaf de opdracht regel die u wilt installeren:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

De versie van de uitbrei ding controleren door uit te voeren 
```cmd/sh
az --version
```
U moet de Azure-cli-IOT-ext-extensie zien 0.8.1 of hoger. Als dat niet het geval is, voert u
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>De uitbrei ding gebruiken

In de volgende secties worden veelvoorkomende opdrachten en opties beschreven die u kunt gebruiken wanneer u `az iot central`uitvoert. Als u de volledige set met opdrachten en opties wilt weer geven, geeft u `--help` door aan `az iot central` of een van de bijbehorende subopdrachten.

### <a name="login"></a>Aanmelden

Meld u aan bij de Azure CLI. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>De toepassings-ID van uw IoT Central-app ophalen
Kopieer de **toepassings-id**in **beheer/toepassings instellingen**. U gaat dit in latere stappen gebruiken.

### <a name="monitor-messages"></a>Berichten bewaken
Controleer de berichten die naar uw IoT Central-app worden verzonden vanaf uw apparaten. Hiermee worden alle kopteksten en aantekeningen ingevoegd.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Apparaateigenschappen weer geven
De huidige eigenschappen voor lezen en lezen/schrijven weer geven voor een bepaald apparaat.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de IoT Central Explorer kunt gebruiken, is de voorgestelde volgende stap om het [beheren van apparaten IOT Central](howto-manage-devices.md)te verkennen.
