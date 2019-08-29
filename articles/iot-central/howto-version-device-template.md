---
title: Informatie over het versie beheer van de app voor uw Azure IoT Central-apps | Microsoft Docs
description: Herhaal uw Apparaatinstellingen door nieuwe versies te maken en zonder uw live verbonden apparaten te beïnvloeden
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 155f392410c5722a28ba09acafc1480e72586773
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100909"
---
# <a name="create-a-new-device-template-version"></a>Een nieuwe sjabloon versie voor een apparaat maken

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Met Azure IoT Central kunt u IoT-toepassingen snel ontwikkelen. U kunt de ontwerpen van uw device-sjablonen snel herhalen door metingen, instellingen of eigenschappen toe te voegen, te bewerken of te verwijderen. Sommige van deze wijzigingen kunnen zich voor de momenteel verbonden apparaten voordoen. Azure IoT Central identificeert deze laatste wijzigingen en biedt een manier om deze updates veilig te implementeren op de apparaten.

Een sjabloon voor een apparaat heeft een versie nummer wanneer u deze maakt. Het versie nummer is standaard 1.0.0. Als u een sjabloon voor een apparaat bewerkt en als die wijziging invloed kan hebben op Live Connected-apparaten, wordt u door Azure IoT Central gevraagd om een nieuwe versie van de sjabloon voor het apparaat te maken.

> [!NOTE]
> Zie [een sjabloon instellen](howto-set-up-template.md) voor meer informatie over het maken van een sjabloon voor een apparaat

## <a name="changes-that-prompt-a-version-change"></a>Wijzigingen die een versie wijziging vragen

In algemene wijzigingen in instellingen of eigenschappen van uw apparaatprofiel wordt een versie wijziging gevraagd.

> [!NOTE]
> Wijzigingen die zijn aangebracht in de sjabloon voor het apparaat, wordt niet gevraagd om een nieuwe versie te maken wanneer er geen apparaat of het meeste apparaat is verbonden.

In de volgende lijst worden de gebruikers acties beschreven waarvoor een nieuwe versie kan zijn vereist:

* Eigenschappen (vereist)
    * Toevoegen of verwijderen van een vereiste eigenschap
    * Het wijzigen van de veld naam van een eigenschap, veld naam die door uw apparaten wordt gebruikt om berichten te verzenden.
*  Eigenschappen (optioneel)
    * Een optionele eigenschap verwijderen
    * Het wijzigen van de veld naam van een eigenschap, veld naam die door uw apparaten wordt gebruikt om berichten te verzenden.
    * Een optionele eigenschap wijzigen in een vereiste eigenschap
*  Instellingen
    * Een instelling toevoegen of verwijderen
    * Het wijzigen van de veld naam van een instelling, veld naam die door uw apparaten wordt gebruikt voor het verzenden en ontvangen van berichten.

## <a name="what-happens-on-version-change"></a>Wat gebeurt er bij de versie wijziging?

Wat gebeurt er met regels en dash boards van het apparaat als er een versie wijziging is?

**Regels** in de vorige versie van de sjabloon voor het apparaat blijven ongewijzigd. Regels worden niet automatisch gemigreerd naar de nieuwe sjabloon versie van het apparaat. U kunt op de gebruikelijke wijze regels maken voor de nieuwe sjabloon versie. Zie voor meer informatie de [regel een telemetrie maken en meldingen instellen in uw Azure IOT Central-artikel voor het Toep assen van toepassingen](howto-create-telemetry-rules.md) .

**Dash boards van apparaten** kunnen verschillende typen tegels bevatten. Sommige tegels kunnen instellingen en eigenschappen bevatten. Wanneer een eigenschap of instelling die in een tegel wordt gebruikt, wordt verwijderd, is de tegel volledig of gedeeltelijk verbroken. U kunt naar de tegel gaan om het probleem op te lossen door de tegel te verwijderen of de inhoud van de tegel bij te werken.

## <a name="migrate-a-device-across-device-template-versions"></a>Een apparaat migreren in de sjabloon versies van een apparaat

U kunt meerdere versies van de sjabloon voor het apparaat maken. In de loop van de tijd hebt u meerdere apparaten die zijn verbonden met behulp van deze Apparaatinstellingen. U kunt apparaten van de ene versie van uw apparaatprofiel naar de andere migreren. In de volgende stappen wordt beschreven hoe u een apparaat migreert:

1. Ga naar de pagina **device Explorer** .
1. Selecteer het apparaat dat u naar een andere versie wilt migreren.
1. Kies **apparaat migreren**.
1. Selecteer het versie nummer waarnaar u het apparaat wilt migreren en kies **migreren**.

![Een apparaat migreren](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u versie van Apparaatbeheer kunt gebruiken in uw Azure IoT Central-toepassing, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Telemetrie-regels maken](howto-create-telemetry-rules.md)