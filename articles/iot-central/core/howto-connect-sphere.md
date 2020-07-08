---
title: Een Azure Sphere apparaat verbinden in azure IoT Central | Microsoft Docs
description: Informatie over het verbinden van een Azure Sphere-apparaat (DevKit) met een Azure IoT Central-toepassing.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.openlocfilehash: a42b483a5ca4b4948f14c1ccbf0b7af6e49224bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83715054"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Een Azure Sphere apparaat verbinden met uw Azure IoT Central-toepassing

*Dit artikel is bedoeld voor ontwikkelaars van apparaten.*

In dit artikel wordt beschreven hoe u een Azure Sphere apparaat (DevKit) verbindt met een Azure IoT Central-toepassing.

Azure Sphere is een beveiligd toepassings platform op hoog niveau met ingebouwde communicatie-en beveiligings functies voor apparaten met een Internet verbinding. Het bevat een beveiligde, verbonden, Crossover microcontroller-eenheid (MCU), een aangepast Linux-besturings systeem op hoog niveau (OS) en een cloud-gebaseerde beveiligings service die continue, Hernieuw bare beveiliging biedt. Zie [Wat is Azure Sphere?](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere)voor meer informatie.

[Azure Sphere Development Kits](https://azure.microsoft.com/services/azure-sphere/get-started/) bieden alles wat u nodig hebt om te beginnen met het maken van prototypen en het ontwikkelen van Azure Sphere toepassingen. Azure IoT Central met Azure Sphere biedt een end-to-end stack voor een IoT-oplossing. Azure Sphere biedt de ondersteuning van het apparaat en IoT Central als een ' Zero-code ' beheerd IoT-toepassings platform.

In dit procedure-artikel:

- Maak een Azure Sphere apparaat in IoT Central met behulp van de sjabloon Azure Sphere DevKit-apparaat uit de-bibliotheek.
- Azure Sphere DevKit-apparaat voorbereiden voor Azure IoT.
- Verbind Azure Sphere DevKit met Azure IoT Central.
- Bekijk de telemetrie van het apparaat in IoT Central.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

- Een Azure IoT Central-toepassing.
- Visual Studio 2019, versie 16,4 of hoger.
- Een [Azure Sphere MT3620 Development Kit van Seeed Studios](https://docs.microsoft.com/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Als u nog geen fysiek apparaat hebt, gaat u na de stap eerste stap naar de laatste sectie om een gesimuleerd apparaat te proberen.

## <a name="create-the-device-in-iot-central"></a>Het apparaat maken in IoT Central

Een Azure Sphere-apparaat maken in IoT Central:

1. Selecteer in uw Azure IoT Central-toepassing het tabblad **Apparaatinstellingen** en selecteer **+ Nieuw**. Selecteer **Azure Sphere voorbeeld apparaat**in de sectie **een sjabloon met een aanbevolen apparaat gebruiken**.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Device-sjabloon voor Azure Sphere DevKit":::

1. Bewerk in de sjabloon met de weer gave met de naam **overzicht** om de **Tempe ratuur** en de **knop indrukken**weer te geven.

1. Selecteer het **bewerkings type apparaat en** de weer gave Cloud gegevens om een andere weer gave toe te voegen waarin de **LED**van de eigenschap lezen/schrijven wordt weer gegeven. Sleep de eigenschap LED van de **status** naar de lege, gestippelde rechthoek aan de rechter kant van het formulier. Selecteer **Opslaan**.

## <a name="prepare-the-device"></a>Het apparaat voorbereiden

Voordat u het Azure Sphere DevKit-apparaat kunt verbinden met IoT Central, moet u [het apparaat en de ontwikkel omgeving instellen](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Het apparaat verbinden

Als u het voor beeld wilt inschakelen om verbinding te maken met IoT Central, moet u [een Azure IOT Central-toepassing configureren en vervolgens het toepassings manifest van het voor beeld wijzigen](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>De telemetrie van het apparaat weer geven

Wanneer het apparaat is verbonden met IoT Central, ziet u de telemetrie op het dash board.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Dash board voor Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken

Als u geen fysiek Azure Sphere DevKit-apparaat hebt, kunt u een gesimuleerd apparaat maken om Azure IoT Central-toepassing uit te proberen.

Een gesimuleerd apparaat maken:

- **Apparaten selecteren > Azure IOT-bol**
- Selecteer **+ Nieuw**.
- Voer een unieke **apparaat-id** en een beschrijvende naam voor het **apparaat**in.
- Schakel de **gesimuleerde** instelling in.
- Selecteer **Maken**.

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u het volgende doen:

- Meer informatie over [connectiviteit van apparaten in Azure IOT Central](./concepts-get-connected.md)
- Meer informatie over het [controleren van de connectiviteit van apparaten met behulp van Azure cli](./howto-monitor-devices-azure-cli.md)
