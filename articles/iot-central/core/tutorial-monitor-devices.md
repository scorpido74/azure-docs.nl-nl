---
title: 'Zelf studie: uw apparaten bewaken in azure IoT Central'
description: 'Zelf studie: als operator gebruikt u uw Azure IoT Central-toepassing om uw apparaten te bewaken.'
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: db3a9a6ba51394a0473f64a2ccede97e5283bba1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106566"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Zelfstudie: Azure IoT Central gebruiken om uw apparaten te bewaken

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In deze zelfstudie leert u hoe u als operator uw Microsoft Azure IoT Central toepassing kunt gebruiken om uw apparaten te bewaken en instellingen te wijzigen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een melding ontvangen
> * Een probleem onderzoeken
> * Een probleem oplossen

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet de bouwer ten minste de eerste drie zelfstudies voor bouwers voltooien om de Azure IoT Central-toepassing te maken:

* [Een nieuw apparaattype definiëren](tutorial-define-device-type.md)
* [Regels en acties voor uw apparaat configureren](tutorial-configure-rules.md)
* [De weergaven van de operator aanpassen](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Een melding ontvangen

Azure IoT Central verzendt meldingen over apparaten als e-mailberichten. De bouwer heeft een regel toegevoegd om een ​​melding te verzenden wanneer de temperatuur in een aangesloten airconditioningapparaat een drempel overschrijdt. Controleer de e-mails die zijn verzonden naar het account dat de bouwer heeft gekozen voor het ontvangen van meldingen.

Open het e-mailbericht dat u aan het einde van de zelfstudie [Regels en acties voor uw apparaat configureren](tutorial-configure-rules.md) hebt ontvangen. Selecteer in het e-mail bericht de koppeling naar het apparaat naast **apparaatnaam** in het gedeelte **Details** :

![E-mailwaarschuwing](media/tutorial-monitor-devices/email.png)

De **Apparaat**-pagina voor het gesimuleerde apparaat **Aangesloten airconditioner-1** dat u in de vorige zelfstudies hebt gemaakt, wordt in uw browser geopend:

![Apparaat dat het e-mailbericht met de melding heeft geactiveerd](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Een probleem onderzoeken

Als operator kunt u informatie over het apparaat bekijken op de pagina’s **Metingen**, **Instellingen**, **Eigenschappen**, **Regels** en  **Dashboard**. De builder heeft het **Dashboard** aangepast om belangrijke informatie weer te geven over een aangesloten airconditioningapparaat.

Kies de weergave **Dashboard** om informatie over het apparaat te bekijken.

![Apparaatdashboard](media/tutorial-monitor-devices/initial_screen.png)

De grafiek op het dashboard toont een grafische voorstelling van de temperatuur van het apparaat. U kunt ook de huidige doel temperatuur voor het apparaat zien op de tegel eigenschappen van het **apparaat** . U besluit dat de doeltemperatuur te hoog is.

## <a name="remediate-an-issue"></a>Een probleem oplossen

Gebruik de pagina **Instellingen** om de doeltemperatuur van het apparaat te wijzigen:

1. Kies **Instellingen**. Wijzig **Temperatuur instellen** in 75. Kies **Update** om de nieuwe doeltemperatuur naar het apparaat te verzenden. Wanneer het apparaat de instellingswijziging bevestigt, verandert de status van de instellingswaarde in **Gesynchroniseerd**:

    ![Instellingen bijwerken](media/tutorial-monitor-devices/change_settings.png)

2. Kies **Dashboard** en controleer de waarde van de nieuwe instelling:

    ![Bijgewerkt apparaatdashboard](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="nextstepaction"]
> * Een melding ontvangen
> * Een probleem onderzoeken
> * Een probleem oplossen

Nu u weet hoe u uw apparaat controleert, is de voorgestelde volgende stap [Een apparaat toevoegen](tutorial-add-device.md).