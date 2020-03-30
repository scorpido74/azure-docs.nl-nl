---
title: Algemene interfaces - IoT Plug and Play Preview | Microsoft Documenten
description: Beschrijving van de gemeenschappelijke interfaces voor IoT Plug and Play-ontwikkelaars
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c6ac90f917b9afc37b3a39d8da679fbcad091778
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234709"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Algemene interfaces voor IoT Plug and Play Preview

Van alle IoT Plug and Play-apparaten wordt verwacht dat ze een aantal algemene interfaces implementeren. Gemeenschappelijke interfaces profiteren van IoT-oplossingen omdat ze consistente functionaliteit bieden. [Certificering](tutorial-build-device-certification.md) vereist dat uw apparaat verschillende algemene interfaces implementeert. U algemene interfacedefinities ophalen uit de openbare modelopslagplaats.

## <a name="summary-of-common-interfaces"></a>Samenvatting van gemeenschappelijke interfaces

| Name | Id | Beschrijving | Geïmplementeerd door Azure IoT SDK | Moet worden gedeclareerd in het vermogen model |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Modelgegevens | urn:azureiot:ModelDiscovery:ModelInformation:1 | Voor apparaten om de mogelijkheid model-ID en interfaces te verklaren. Vereist voor alle IoT Plug and Play-apparaten. | Ja | Nee |
| SDK-informatie voor digitale tweelingklanten | urn:azureiot:Client:SDKInformation:1 | Client SDK voor het verbinden van het apparaat met Azure. Vereist voor [certificering](tutorial-build-device-certification.md) | Ja | Nee |
| Apparaatgegevens | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informatie over hardware en besturingssysteem over het apparaat. Vereist voor [certificering](tutorial-build-device-certification.md) | Nee | Ja |
| Modeldefinitie | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Voor apparaten om de volledige definitie voor zijn capaciteitsmodel en interfaces te verklaren. Moet worden geïmplementeerd wanneer modeldefinities niet worden gehost in een modelopslagplaats. | Nee | Ja |
| Digital Twin | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Voor ontwikkelaars van oplossingen om de mogelijkheid model ID en interface-ID's voor een digitale tweeling op te halen. Deze interface wordt niet gedeclareerd of geïmplementeerd door een IoT Plug and Play-apparaat. | Nee | Nee |

- Geïmplementeerd door Azure IoT SDK - Of de Azure IoT SDK de mogelijkheden implementeert die in de interfaces zijn gedeclareerd. IoT Plug and Play-apparaten die de Azure IoT SDK gebruiken, hoeven deze interface niet te implementeren.
- Moet worden gedeclareerd in het vermogen model - Als `"implements":` 'ja', deze interface moet worden aangegeven binnen het gedeelte van het apparaat mogelijkheid model voor dit IoT Plug and Play apparaat.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Interfacedefinities ophalen uit de openbare opslagplaats

### <a name="cli"></a>CLI

U de Azure IoT-extensie voor Azure CLI gebruiken om de algemene interfaces op te halen uit de openbare modelopslagplaats.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS-code

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play in** en selecteer vervolgens de opdracht Plug and Play van **IoT Plug and Play: Open Model Repository.** Kies **Openbare opslagplaats**. De openbare modelopslagplaats wordt geopend in VS-code.

1. Voer in de openbare modelopslagplaats de naam van de interface in het zoekveld in.

1. Als u een lokale kopie van de interface wilt maken, selecteert u deze in de zoekresultaten en selecteert u **Downloaden**.

## <a name="next-steps"></a>Volgende stappen

Nu u meer hebt geleerd over algemene interfaces, volgen hier enkele aanvullende bronnen:

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [Apparaat-SDK voor C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST-API](https://docs.microsoft.com/rest/api/iothub/device)
