---
title: Snelstart - Uw apparaten controleren in Azure IoT Central
description: Als operator leest u hoe u uw Azure IoT Central-toepassing gebruiken om uw apparaten te controleren.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1dec52bbf1435cd7e363edf111f769d3e2cffb6a
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998924"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Snelstart: Azure IoT Central gebruiken om uw apparaten te controleren

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Met deze snelstart ziet u als operator hoe u uw Microsoft Azure IoT Central-toepassing gebruiken om uw apparaten te controleren en instellingen te wijzigen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de drie vorige quickstarts voltooien [Een Azure IoT Central-toepassing](./quick-deploy-iot-central.md) [maken, een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md) en [Regels en acties configureren voor uw apparaat](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Een melding ontvangen

Azure IoT Central verzendt meldingen over apparaten als e-mailberichten. De bouwer heeft een regel toegevoegd om een melding te verzenden wanneer de temperatuur in een aangesloten apparaatsensor een drempelwaarde overschrijdt. Controleer de e-mails die zijn verzonden naar het account dat de bouwer heeft gekozen voor het ontvangen van meldingen.

Open het e-mailbericht dat u hebt ontvangen aan het einde van de [snelstart regels en acties configureren voor uw apparaat.](quick-configure-rules.md) Selecteer in de e-mail de koppeling naar het apparaat:

![E-mailwaarschuwing](media/quick-monitor-devices/email.png)

De **overzichtsweergave** voor het gesimuleerde apparaat dat u in de vorige quickstarts hebt gemaakt, wordt geopend in uw browser:

![Apparaat dat het e-mailbericht met de melding heeft geactiveerd](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Een probleem onderzoeken

Als operator u informatie over het apparaat bekijken in de weergaven **Overzicht**, **Over**en **Opdrachten.** De bouwer heeft een **apparaatweergave beheren** gemaakt om apparaatgegevens te bewerken en apparaateigenschappen in te stellen.

De grafiek op het dashboard toont een grafische voorstelling van de temperatuur van het apparaat. U besluit dat de temperatuur van het apparaat te hoog is.

## <a name="remediate-an-issue"></a>Een probleem oplossen

Als u een wijziging in het apparaat wilt aanbrengen, gebruikt u de pagina **Apparaat beheren.**

Wijzig **de ventilatorsnelheid** in 500 om het apparaat te koelen. Kies **Opslaan** om het apparaat bij te werken. Wanneer het apparaat bevestigt dat de instellingen worden gewijzigd, wordt de status van de eigenschap gewijzigd in **gesynchroniseerd:**

![Instellingen bijwerken](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

* Een melding ontvangen
* Een probleem onderzoeken
* Een probleem oplossen

Nu u weet dat u uw apparaat moet controleren, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een apparaatsjabloon maken en beheren.](howto-set-up-template.md)
