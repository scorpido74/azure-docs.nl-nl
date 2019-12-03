---
title: 'Quick Start: uw apparaten bewaken in azure IoT Central'
description: Als operator kunt u uw Azure IoT Central-toepassing gebruiken om uw apparaten in deze Quick Start te controleren.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a6bc4b76dc00330e39526aec8bc5651b9abb590b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706742"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Snelstartgids: Azure IoT Central gebruiken om uw apparaten te bewaken (preview-functies)

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze Quick start ziet u, als operator, hoe u uw Microsoft Azure IoT Central toepassing kunt gebruiken om uw apparaten te bewaken en instellingen te wijzigen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de drie vorige Quick starts volt ooien [een Azure IOT Central-toepassing maken](./quick-deploy-iot-central.md), [een gesimuleerd apparaat toevoegen aan uw IOT Central toepassing](./quick-create-pnp-device.md) en [regels en acties voor uw apparaat configureren](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Een melding ontvangen

Azure IoT Central verzendt meldingen over apparaten als e-mailberichten. De opbouw functie heeft een regel toegevoegd voor het verzenden van een melding wanneer de Tempe ratuur in een verbonden omgevings sensor een drempel waarde overschrijdt. Controleer de e-mails die zijn verzonden naar het account dat de bouwer heeft gekozen voor het ontvangen van meldingen.

Open het e-mail bericht dat u hebt ontvangen aan het einde van de [regels en acties voor de Snelstartgids van uw apparaat](quick-configure-rules.md) . Selecteer in het e-mail bericht de koppeling naar het apparaat:

![E-mailwaarschuwing](media/quick-monitor-devices/email.png)

De **Dashboard** weergave voor het door de omgevings sensor gesimuleerde apparaat dat u hebt gemaakt in de vorige Quick starts, wordt geopend in uw browser:

![Apparaat dat het e-mailbericht met de melding heeft geactiveerd](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Een probleem onderzoeken

Als operator kunt u informatie weer geven over het apparaat op de pagina's **overzicht**, **Eigenschappen van omgevings sensor**en **opdrachten** . De opbouw functie heeft de pagina's **dash board** en **Eigenschappen van omgevings sensor** aangepast om belang rijke informatie weer te geven over een verbonden omgevings sensor apparaat.

Kies de weer gave **overzicht** om informatie over het apparaat weer te geven.

De grafiek op het dashboard toont een grafische voorstelling van de temperatuur van het apparaat. U besluit dat de Tempe ratuur van het apparaat te hoog is.

## <a name="remediate-an-issue"></a>Een probleem oplossen

Als u een wijziging wilt aanbrengen in het apparaat, gebruikt u de pagina **Eigenschappen van omgevings sensor** .

Kies **Eigenschappen van de omgevings sensor**. **Helderheids niveau** wijzigen in 10. Kies **Opslaan** om het apparaat bij te werken. Wanneer het apparaat de gewijzigde instellingen bevestigt, wordt de status van de eigenschap gewijzigd in **gesynchroniseerd**:

![Instellingen bijwerken](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

* Een melding ontvangen
* Een probleem onderzoeken
* Een probleem oplossen

Nu u weet dat u het apparaat nu kunt controleren, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een sjabloon voor een apparaat maken en beheren](howto-set-up-template.md).
