---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880460"
---
## <a name="iot-plug-and-play-device"></a>IoT Plug en Play-apparaat

Een IoT-Plug en Play apparaat is doorgaans een klein, zelfstandig computer apparaat dat gegevens kan verzamelen of andere apparaten kan beheren, en waarmee software of firmware wordt uitgevoerd die de mogelijkheden implementeert die zijn gedeclareerd in een [mogelijkheidsprofiel](#device-capability-model).  Een IoT-Plug en Play apparaat kan bijvoorbeeld een milieubewakings apparaat zijn of een controller voor een systeem voor agrigulture irrigatie. In de Cloud gehoste IoT-oplossingen kunnen worden geschreven naar de opdracht, het beheer en de ontvangst van gegevens van IoT Plug en Play-apparaten. IoT Plug en Play-apparaten kunt u vinden in het [Azure Certified for IOT-apparaat Catalog](https://catalog.azureiotsolutions.com/). Elk IoT Plug en Play-apparaat in de catalogus is gevalideerd en heeft een [functionaliteits model voor apparaten](#device-capability-model).

## <a name="digital-twin"></a>Digitale dubbele

Digital apparaatdubbels zijn modellen van IoT Plug en Play-apparaten.  Digitale apparaatdubbels zijn gemodelleerd met behulp van de [Digital-taal voor dubbele definitie](https://aka.ms/DTDL).  U kunt de Azure IoT API gebruiken om te communiceren met Digital apparaatdubbels. 

## <a name="digital-twin-definition-language"></a>Digital-dubbele definitie taal

Een taal voor het beschrijven van modellen en interfaces voor [IoT Plug en Play-apparaten](#iot-plug-and-play-device).  Gebruik de [Digital-dubbele definitie taal](https://aka.ms/DTDL) om een [digitale twee](#digital-twin) mogelijkheden te beschrijven en het IoT-platform en IOT-oplossingen in te scha kelen om de semantiek van de entiteit te benutten.

## <a name="device-capability-model"></a>Mogelijkheidsprofiel

Een mogelijkheidsprofiel beschrijft een apparaat en definieert de set interfaces die door het apparaat worden geïmplementeerd. Een mogelijkheidsprofiel maken dat overeenkomt met een fysiek apparaat, product of SKU.

## <a name="interface"></a>Interface

Een interface beschrijft gerelateerde mogelijkheden die worden geïmplementeerd door een apparaat of digitale twee. Interfaces kunnen opnieuw worden gebruikt voor verschillende mogelijkhedens modellen.

## <a name="property"></a>Eigenschap

Eigenschappen zijn gegevens velden die zijn gedefinieerd in een [Interface](#interface) die een status van een digitale dubbele waarde vertegenwoordigt. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar. Alleen-lezen eigenschappen worden ingesteld door code die wordt uitgevoerd in de context van de IoT-Plug en Play apparaat zelf, zoals serie nummer.  Beschrijf bare eigenschappen worden ingesteld door externe entiteiten, zoals alarm drempels.

## <a name="telemetry"></a>Telemetrie

Telemetrie-velden die in een [Interface](#interface) zijn gedefinieerd, vertegenwoordigen metingen. Deze metingen zijn doorgaans waarden zoals sensor metingen.

## <a name="command"></a>Opdracht

Opdrachten die in een [Interface](#interface) zijn gedefinieerd, vertegenwoordigen methoden die kunnen worden uitgevoerd op het digitale dubbele. Een voor beeld: een reset opdracht om een apparaat opnieuw in te stellen.
