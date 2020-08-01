---
title: IoT-Plug en Play architectuur | Microsoft Docs
description: Als opbouw functie voor oplossingen begrijpt u belang rijke onderdelen van IoT Plug en Play.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f656de0bb2e5244e137ae21a6d7af88f3430b12c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475682"
---
# <a name="iot-plug-and-play-preview-architecture"></a>Architectuur van IoT Plug en Play (preview)

Met IoT Plug en Play Preview kunnen bouwers van oplossingen smart-apparaten integreren met hun oplossingen zonder hand matige configuratie. De kern van IoT Plug en Play is een _model_ apparaat waarmee de mogelijkheden van een apparaat worden beschreven in een IoT-Plug en Play toepassing. Dit model is gestructureerd als een reeks interfaces die het volgende definiëren:

- _Eigenschappen_ die de status alleen-lezen of beschrijfbaar van een apparaat of andere entiteit vertegenwoordigen. Een serie nummer van een apparaat kan bijvoorbeeld een alleen-lezen eigenschap zijn en een doel temperatuur op een thermo staat kan een Beschrijf bare eigenschap zijn.
- _Telemetrie_ die de gegevens die door een apparaat worden verzonden, ongeacht of de gegevens een gewone stroom van sensor leesingen, een incidentele fout of een informatie bericht zijn.
- _Opdrachten_ die een functie of bewerking beschrijven die op een apparaat kan worden uitgevoerd. Een opdracht kan bijvoorbeeld een gateway opnieuw opstarten of een foto nemen met een externe camera.

Elk model en elke interface heeft een unieke ID.

In het volgende diagram ziet u de belangrijkste elementen van een IoT Plug en Play-oplossing:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT-Plug en Play architectuur":::

## <a name="model-repository"></a>Model opslagplaats

De [model opslagplaats](./concepts-model-repository.md) is een archief voor model-en Interface definities. U definieert modellen en interfaces met behulp van de [Digital Apparaatdubbels Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl).

Met de Web-UI kunt u de modellen en interfaces beheren.

De model opslagplaats maakt gebruik van RBAC zodat u de toegang tot Interface definities kunt beperken.

## <a name="devices"></a>Apparaten

Een apparaat Builder implementeert de code om uit te voeren op een IoT-Smart Device met behulp van een van de [Azure IOT-apparaat-sdk's](./libraries-sdks.md). De Sdk's van het apparaat helpen de opbouw functie voor apparaten bij het volgende:

- Veilig verbinding maken met een IoT-hub.
- Registreer het apparaat bij uw IoT-hub en laat de model-ID aankondigen waarmee de verzameling interfaces wordt geïdentificeerd die het apparaat implementeert.
- Update de eigenschappen die zijn gedefinieerd in de DTDL-interfaces die het apparaat implementeert. Deze eigenschappen worden geïmplementeerd met Digital apparaatdubbels die de synchronisatie met uw IoT-hub beheren.
- Opdracht-handlers toevoegen voor de opdrachten die zijn gedefinieerd in de DTDL-interfaces die het apparaat implementeert.
- Telemetrie verzenden naar de IoT-hub.

## <a name="iot-hub"></a>IoT Hub

[IOT hub](../iot-hub/about-iot-hub.md) is een in de Cloud gehoste service die fungeert als een centrale Message hub voor bidirectionele communicatie tussen uw IOT-oplossing en de apparaten die worden beheerd.

Een IoT-hub:

- Hiermee wordt de model-ID geïmplementeerd door een apparaat dat beschikbaar is voor een back-end-oplossing.
- Houdt de digitale twee apparaten bij die zijn gekoppeld aan elk Plug en Play apparaat dat is verbonden met de hub.
- Stuurt telemetrie-stromen naar andere services voor verwerking of opslag.
- Hiermee worden digitale dubbele wijzigings gebeurtenissen naar andere services gerouteerd om de bewaking van apparaten mogelijk te maken.

## <a name="backend-solution"></a>Back-end-oplossing

Met een back-end-oplossing worden verbonden apparaten bewaakt en beheerd door interactie met Digital apparaatdubbels in de IoT-hub. Gebruik een van de service-Sdk's om uw back-end-oplossing te implementeren. Om inzicht te krijgen in de mogelijkheden van een verbonden apparaat, de back-end van de oplossing:

1. Hiermee wordt de model-ID opgehaald van het apparaat dat is geregistreerd bij de IoT hub.
1. Maakt gebruik van de model-ID voor het ophalen van de interface definities uit een model opslagplaats.
1. Maakt gebruik van de model-parser om informatie uit de interface definities te extra heren.

De back-end-oplossing kan de informatie van de interface definities gebruiken voor het volgende:

- Lees eigenschaps waarden die zijn gerapporteerd door apparaten.
- Beschrijf bare eigenschappen op een apparaat bijwerken.
- Aanroep opdrachten die worden geïmplementeerd door een apparaat.
- Meer informatie over de indeling van de telemetrie die door een apparaat wordt verzonden.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van de architectuur van een IoT-Plug en Play oplossing, moeten de volgende stappen worden uitgevoerd om meer te weten te komen over:

- [De model opslagplaats](./concepts-model-repository.md)
- [Digitale dubbele model integratie](./concepts-model-discovery.md)
- [Ontwikkelen voor IoT-Plug en Play](./concepts-developer-guide.md)
