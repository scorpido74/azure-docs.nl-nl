---
title: Connectiviteit van apparaten bewaken met behulp van Azure IoT Central Explorer
description: Controleer de apparaatgegevens en Bekijk de dubbele wijzigingen van het apparaat via de IoT Central Explorer-CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756667"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Apparaatconnectiviteit bewaken met Azure CLI

*Dit onderwerp is van toepassing op ontwikkel aars van apparaten en oplossingen bouwers.*

Gebruik de Azure CLI IoT-extensie om berichten te bekijken die uw apparaten naar IoT Central verzenden en wijzigingen in het apparaat te observeren. U kunt dit hulp programma gebruiken om de connectiviteit van apparaten op te sporen en te controleren op problemen met de toegang tot de Cloud of apparaten die niet reageren op twee wijzigingen.

[Bezoek de naslag informatie voor Azure CLI-extensies voor meer informatie](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Vereisten

+ Azure CLI is geïnstalleerd en versie 2.0.7 of hoger. Controleer de versie van uw Azure CLI door uit `az --version`te voeren. Meer informatie over het installeren en bijwerken van de [Azure cli-documenten](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Een werk-of school account in azure, toegevoegd als een gebruiker in een IoT Central-toepassing.

## <a name="install-the-iot-central-extension"></a>De IoT Central-extensie installeren

Voer de volgende opdracht uit vanaf de opdracht regel die u wilt installeren:

```azurecli
az extension add --name azure-iot
```

Controleer de versie van de uitbrei ding door het volgende uit te voeren:

```azurecli
az --version
```

U ziet dat de Azure IOT-uitbrei ding 0.8.1 of hoger is. Als dat niet het geval is, voert u de volgende handelingen uit:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>De uitbrei ding gebruiken

In de volgende secties worden veelvoorkomende opdrachten en opties beschreven die u kunt gebruiken `az iot central`wanneer u uitvoert. Als u de volledige set met opdrachten en opties wilt weer `--help` geven `az iot central` , geeft u een of meer van de bijbehorende subopdrachten door.

### <a name="login"></a>Aanmelden

Meld u aan bij de Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>De toepassings-ID van uw IoT Central-app ophalen
Kopieer de **toepassings-id**in **beheer/toepassings instellingen**. U gebruikt deze waarde in latere stappen.

### <a name="monitor-messages"></a>Berichten bewaken
Controleer de berichten die naar uw IoT Central-app worden verzonden vanaf uw apparaten. De uitvoer bevat alle kopteksten en aantekeningen.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Apparaateigenschappen weer geven
De huidige eigenschappen voor lezen en lezen/schrijven weer geven voor een bepaald apparaat.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u de volgende stap lezen over de [connectiviteit van apparaten in Azure IOT Central](./concepts-get-connected.md).
