---
title: Uw apparaten bewaken in Azure IoT Central | Microsoft Docs
description: Gebruik als operator uw Azure IoT Central-toepassing om uw apparaten te bewaken.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878115"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Zelfstudie: Azure IoT Central gebruiken om uw apparaten te bewaken (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In deze zelfstudie leert u hoe u als operator uw Microsoft Azure IoT Central toepassing kunt gebruiken om uw apparaten te bewaken en instellingen te wijzigen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een melding ontvangen
> * Een probleem onderzoeken
> * Een probleem oplossen

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet de bouwer ten minste de eerste drie zelfstudies voor bouwers voltooien om de Azure IoT Central-toepassing te maken:

* [Een nieuw apparaattype definiëren](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Een apparaat toevoegen](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Regels en acties voor uw apparaat configureren](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Een melding ontvangen

Azure IoT Central verzendt meldingen over apparaten als e-mailberichten. De opbouw functie heeft een regel toegevoegd voor het verzenden van een melding wanneer de Tempe ratuur in een verbonden omgevings sensor een drempel waarde overschrijdt. Controleer de e-mails die zijn verzonden naar het account dat de bouwer heeft gekozen voor het ontvangen van meldingen.

Open het e-mailbericht dat u aan het einde van de zelfstudie [Regels en acties voor uw apparaat configureren](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) hebt ontvangen. Selecteer in het e-mail bericht de koppeling naar het apparaat:

![E-mailwaarschuwing](media/tutorial-monitor-devices-pnp/email.png)

De **Dashboard** weergave voor het door de omgevings sensor gesimuleerde apparaat dat u hebt gemaakt in de vorige zelf studies, wordt geopend in uw browser:

![Apparaat dat het e-mailbericht met de melding heeft geactiveerd](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Een probleem onderzoeken

Als operator kunt u informatie weer geven over het apparaat op het **dash board**, de eigenschappen van de **omgevings sensor**en de pagina **opdrachten** . De opbouw functie heeft de pagina's **dash board** en **Eigenschappen van omgevings sensor** aangepast om belang rijke informatie weer te geven over een verbonden omgevings sensor apparaat.

Kies de weergave **Dashboard** om informatie over het apparaat te bekijken.

De grafiek op het dashboard toont een grafische voorstelling van de temperatuur van het apparaat. U kunt ook de huidige doel temperatuur voor het apparaat zien op de tegel eigenschappen van het **apparaat** . U besluit dat de doeltemperatuur te hoog is.

## <a name="remediate-an-issue"></a>Een probleem oplossen

Als u een wijziging wilt aanbrengen in het apparaat, gebruikt u de pagina **Eigenschappen van omgevings sensor** :

1. Kies **Eigenschappen**van de omgevings sensor. **Helderheids niveau** wijzigen in 10. Kies **Opslaan** om het apparaat bij te werken. Wanneer het apparaat de gewijzigde instellingen bevestigt, wordt de status van de eigenschap gewijzigd in **gesynchroniseerd**:

    ![Update-instellingen](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Kies **dash board** en controleer de nieuwe eigenschaps waarde:

    ![Bijgewerkt apparaatdashboard](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een melding ontvangen
* Een probleem onderzoeken
* Een probleem oplossen

Nu u weet dat u het apparaat nu kunt controleren, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Regels en acties voor uw apparaat configureren](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).