---
title: Een echte apparaat toevoegen aan een Azure IoT Central-toepassing | Microsoft Docs
description: Als operator kunt u een nieuw apparaat toevoegen aan uw Azure IoT Central-toepassing.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878171"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Zelfstudie: Een gesimuleerd apparaat toevoegen aan uw Azure IoT Central-toepassing (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Deze zelf studie laat zien hoe u een gesimuleerd apparaat kunt toevoegen en configureren voor uw Microsoft Azure IoT Central toepassing.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuw gesimuleerd apparaat toevoegen
> * Een gesimuleerd apparaat gebruiken in de bouw ervaring

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet de opbouw functie de eerste zelf studie voor de opbouw functie volt ooien om de Azure IoT Central-toepassing te maken:

* [Een nieuw apparaattype definiëren](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (verplicht)

## <a name="add-a-simulated-device"></a>Een gesimuleerd apparaat toevoegen

Als u een echt apparaat aan uw toepassing wilt toevoegen, gebruikt u de sjabloon voor de **omgevings sensor** van het apparaat dat u hebt gemaakt in de zelf studie [een nieuw apparaattype definiëren](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

1. Als u een nieuw apparaat wilt toevoegen als een operator, kiest u **apparaten** in het navigatie menu links. Op het tabblad **apparaten** worden **alle apparaten** en de sjabloon voor de **omgevings sensor** van het apparaat weer gegeven.

1. Selecteer **+ Nieuw**om een gesimuleerd omgevings sensor apparaat toe te voegen. Gebruik de voorgestelde **apparaat-id** of geef uw eigen kleine **apparaat-id**op. U kunt ook een naam voor het nieuwe apparaat opgeven. Schakel de gesimuleerde wissel knop in **op aan** en selecteer vervolgens **maken**.

    ![Gesimuleerd apparaat](./media/tutorial-add-device-pnp/simulated-device.png)

Nu kunt u communiceren met de weer gaven die zijn gemaakt door de opbouw functie voor de apparaatprofiel met gesimuleerde gegevens.

## <a name="use-a-simulated-device-to-improve-views"></a>Een gesimuleerd apparaat gebruiken om weer gaven te verbeteren

Nadat u een nieuw gesimuleerd apparaat hebt gemaakt, kan de Builder dit apparaat gebruiken om de weer gaven voor de apparaatprofiel te verbeteren en te bouwen.

1. In de weer gave van uw apparaat kopieert u de **apparaat-id** van het gesimuleerde apparaat dat u hebt gemaakt.

1. Kies het tabblad **Apparaatinstellingen** in het navigatie menu aan de linkerkant en selecteer de sjabloon **omgevings sensor** .

1. Selecteer een van de weer gaven die u wilt bewerken of maak een nieuwe weer gave. Klik op **Preview-apparaat configureren**. Hier kunt u kiezen tussen het gebruik van geen preview-apparaat en een echt apparaat dat u kunt configureren voor het testen of van een bestaand apparaat dat u hebt toegevoegd in IoT Central.

1. Kies **selecteren in een actief apparaat** en voer de **apparaat-id** in van het gesimuleerde apparaat dat u hebt gekopieerd.

1. Kies **Toep assen**. U ziet nu hetzelfde gesimuleerde apparaat in de sjabloon weergaven voor het maken van een apparaat. Deze weer gave is vooral nuttig voor grafieken en andere visualisaties.

    ![Preview-apparaat configureren](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een nieuw gesimuleerd apparaat toevoegen
* Een gesimuleerd apparaat gebruiken in de bouw ervaring

Nu u een gesimuleerd apparaat hebt verbonden met uw Azure IoT Central-toepassing, zijn hier enkele suggesties voor de volgende stappen.

Als operator leert u het volgende:

> [!div class="nextstepaction"]
> [Regels configureren](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

Als apparaatontwikkelaar, leert u het volgende:

> [!div class="nextstepaction"]
> [Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



