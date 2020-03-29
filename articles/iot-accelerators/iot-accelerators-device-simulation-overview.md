---
title: Overzicht van apparaatsimulatie - Azure | Microsoft Documenten
description: Een beschrijving van de device simulation oplossingsversneller en de mogelijkheden ervan.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65464868"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Overzicht van de oplossingsversneller voor apparaatsimulatie

In een Cloud-gebaseerde IoT-oplossing maken uw apparaten verbinding met een eindpunt in de cloud om telemetrie te verzenden, zoals temperatuur, locatie en status. Uw oplossing verbruikt deze telemetrie, zodat u acties ondernemen of er inzichten uit halen.

Wanneer u een IoT-oplossing ontwikkelt, zijn experimenteren en testen essentiële onderdelen van dat proces. Simulatie is een belangrijk hulpmiddel tijdens dit proces. Met apparaatsimulatie u:

* Snel een prototype aan de praat krijgen en vervolgens herhalen door gesimuleerd apparaatgedrag on the fly aan te passen. Met dit proces u het idee bewijzen voordat u investeert in dure hardware. U aangepaste apparaten maken via de webgebruikersinterface om binnen enkele seconden een prototype-apparaat te genereren.
* Validerde de oplossing zoals verwacht van apparaat tot oplossing door het simuleren van real-world apparaatgedrag. U complexe apparaatgedragingen scripten met JavaScript om realistische gesimuleerde telemetrie te genereren.
* Schaal test uw oplossing door normale, piek- en buitenpiekbelastingsomstandigheden te simuleren. Schaaltests helpen u ook om de Azure-resources die nodig zijn om uw oplossing uit te voeren, op de juiste grootte te krijgen.

![Voorbeeld drone simulatie](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Met Apparaatsimulatie u apparaatmodellen definiëren om uw echte apparaten te simuleren. Dit model bevat berichtindelingen, dubbele eigenschappen en methoden. U ook complexe apparaatgedragen simuleren met JavaScript.

U simulaties uitvoeren voor één tot duizenden apparaten die verbinding maken met een IoT-hub. Om u te helpen bij het testen, u optioneel een IoT-hub implementeren, samen met apparaatsimulatie voor een zelfstandige omgeving.

Apparaatsimulatie is gratis. Apparaatsimulatie wordt echter geïmplementeerd in uw Azure-abonnement in de cloud en verbruikt azure-resources. Als apparaatsimulatie niet aan uw vereisten voldoet, is de [broncode ook beschikbaar op GitHub](https://github.com/Azure/device-simulation-dotnet) om te kopiëren en te wijzigen.

## <a name="sample-simulations"></a>Voorbeeldsimulaties

Wanneer u apparaatsimulatie implementeert, krijgt u enkele voorbeeldsimulaties en voorbeeldapparaten. U deze voorbeelden gebruiken om te leren hoe u apparaatsimulatie gebruiken. Voer om te beginnen een voorbeeldsimulatie uit [die 10 vrachtwagens simuleert.](quickstart-device-simulation-deploy.md) U ook [uw eigen simulatie maken met behulp van een van de vele voorbeeldapparaten die worden geleverd.](iot-accelerators-device-simulation-create-simulation.md)

![Simulatieconfiguratie](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Aangepaste gesimuleerde apparaten

U de apparaatsimulatie gebruiken om [aangepaste apparaatmodellen](iot-accelerators-device-simulation-create-custom-device.md) te maken die u in uw simulaties gebruiken. U bijvoorbeeld een nieuw koelkastapparaatmodel definiëren dat telemetrie over temperatuur en vochtigheid verzendt. Aangepaste gesimuleerde apparaten zijn ideaal voor eenvoudig apparaatgedrag met willekeurige, toenemende of afzwaaiende telemetriewaarden.

![Apparaatmodel maken](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Geavanceerde gesimuleerde apparaten

Wanneer u meer controle nodig hebt over de telemetriewaarden die een apparaat verzendt, u een geavanceerd apparaatmodel gebruiken. Geavanceerde apparaatmodellen maken JavaScript-ondersteuning mogelijk om de verzonden telemetriewaarden te manipuleren. U bijvoorbeeld de binnentemperatuur van een geparkeerde auto simuleren op een warme zonnige dag - naarmate de buitentemperatuur stijgt, neemt de binnentemperatuur exponentieel toe.

Met geavanceerde apparaatmodellen u [uw eigen apparaatmodellen maken en uploaden](iot-accelerators-device-simulation-advanced-device.md) die bestaan uit een JSON-apparaatdefinitiebestand en bijbehorende JavaScript-bestanden.

Met geavanceerde apparaatmodellen u:

* Geef de berichtindeling op die vanaf het apparaat wordt verzonden, samen met de telemetrietypen.
* Gebruik aangepaste scripting om telemetriewaarden te genereren die de status van het apparaat in de loop van de tijd behouden.
* Gebruik aangepaste scripting om op te geven hoe het gesimuleerde apparaat reageert op methoden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leerde u over de apparaatsimulatieoplossingsversneller en de mogelijkheden ervan. Ga aan de slag met de oplossingsversneller:

> [!div class="nextstepaction"]
> [een IoT-apparaatsimulatie in Azure implementeren en uitvoeren](quickstart-device-simulation-deploy.md)
