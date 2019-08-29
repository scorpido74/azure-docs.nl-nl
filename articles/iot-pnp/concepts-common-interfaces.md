---
title: Algemene interfaces-IoT Plug en Play preview | Microsoft Docs
description: Beschrijving van algemene interfaces voor IoT Plug en Play-ontwikkel aars
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/16/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5a5aeac06ccf31e88a8ec670e57b22e753553ff5
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114254"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Algemene interfaces Plug en Play de preview-versie van IoT

Alle IoT Plug en Play-apparaten worden verwacht een aantal algemene interfaces te implementeren. Algemene interfaces profiteren van IoT-oplossingen omdat ze consistente functionaliteit bieden. Voor [certificering](tutorial-build-device-certification.md) is het apparaat vereist dat er verschillende algemene interfaces worden geïmplementeerd. U kunt algemene interface definities ophalen uit de open bare model opslagplaats.

## <a name="summary-of-common-interfaces"></a>Samen vatting van algemene interfaces

| Name | id | Description | Geïmplementeerd door de Azure IoT SDK | Moet worden gedeclareerd in het mogelijkheidsprofiel |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Model gegevens | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Voor apparaten om de ID en interfaces van het mogelijkheidsprofiel te declareren. Vereist voor alle IoT Plug en Play-apparaten. | Ja | Nee |
| Digital-client-SDK-informatie | urn: azureiot: client: SDKInformation: 1 | Client-SDK voor het verbinden van het apparaat met Azure. Vereist voor [certificering](tutorial-build-device-certification.md) | Ja | Nee |
| Apparaatgegevens | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Informatie over de hardware en het besturings systeem van het apparaat. Vereist voor [certificering](tutorial-build-device-certification.md) | Nee | Ja |
| Model definitie | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Voor apparaten voor het declareren van de volledige definitie voor het mogelijkheidsprofiel en de interfaces van het model. Moet worden geïmplementeerd wanneer model definities niet worden gehost in een model opslagplaats. | Nee | Ja |
| Digitale dubbele | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | Ontwikkel aars van oplossingen moeten de ID en interface-Id's van het mogelijkheidsprofiel ophalen voor een digitale dubbele. Deze interface is niet gedeclareerd of geïmplementeerd door een IoT Plug en Play-apparaat. | Nee | Nee |

- Geïmplementeerd door de Azure IoT SDK: of de Azure IoT SDK de mogelijkheden implementeert die in de interfaces zijn gedeclareerd. IoT Plug en Play-apparaten die gebruikmaken van de Azure IoT SDK, hoeven deze interface niet te implementeren.
- Moet worden gedeclareerd in het mogelijkheidsprofiel: als Ja is opgegeven, moet deze interface worden gedeclareerd binnen de `"implements":` sectie van het mogelijkheidsprofiel voor deze IOT-Plug en Play apparaat.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Interface definities ophalen uit de open bare opslag plaats

### <a name="cli"></a>CLI

U kunt de Azure IoT-extensie voor Azure CLI gebruiken om de gemeen schappelijke interfaces uit de open bare model opslagplaats op te halen.

```cmd/sh
az iot pnp interface show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

```cmd/sh
az iot pnp model show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

### <a name="vs-code"></a>VS Code

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer vervolgens **de IOT-Plug en Play: Open de opdracht** model opslagplaats. Kies een **open bare opslag plaats**. De open bare model opslagplaats wordt geopend in VS code.

1. Voer in de open bare model opslagplaats de naam van de interface in het zoek veld in.

1. Als u een lokale kopie van de interface wilt maken, selecteert u deze in de zoek resultaten en selecteert u **downloaden**.

## <a name="next-steps"></a>Volgende stappen

Nu u over algemene interfaces hebt geleerd, zijn hier enkele aanvullende bronnen:

- [Digital-dubbele-definitie taal (DTDL)](https://aka.ms/DTDL)
- [Apparaat-SDK voor C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
