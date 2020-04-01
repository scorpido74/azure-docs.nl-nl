---
title: Waarschuwingen gebruiken in de oplossing voor externe bewaking - Azure | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u waarschuwingen kunt gebruiken om problemen te identificeren en op te lossen met apparaten die zijn verbonden met de verbetering voor de externe bewakingsoplossing.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 853fa2b80e04dd8d9225d023db8030fed044ed7f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890928"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Zelfstudie: Problemen met apparaten opsporen en oplossen

In deze zelfstudie gebruikt u de verbetering voor de externe bewakingsoplossing om problemen met verbonden IoT-apparaten te identificeren en te herstellen. U gebruikt waarschuwingen op het dashboard van de oplossingsverbetering om problemen te identificeren. Vervolgens voert u externe taken uit om deze problemen op te lossen.

Contoso test momenteel in de praktijk een nieuw **Prototypeapparaat**. Als Contoso-operator merkt u, tijdens het testen, dat voor het **Prototypeapparaat** onverwacht een temperatuurwaarschuwing wordt geactiveerd op het dashboard. U moet nu het gedrag onderzoeken van het **Prototypeapparaat** waarvoor de waarschuwing wordt weergegeven, en het probleem oplossen.

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * Een waarschuwing voor een apparaat onderzocht
> * Het probleem met het apparaat opgelost

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Een waarschuwing onderzoeken

Op de **Dashboardpagina** ziet u dat onverwachte temperatuurwaarschuwingen zijn geactiveerd op basis van de regel die is gekoppeld aan de **Prototypeapparaten**:

[![Waarschuwingen op het dashboard](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Als u het probleem nader wilt onderzoeken, kiest u de optie **Waarschuwing verkennen** naast de waarschuwing:

[![Waarschuwing van het dashboard verkennen](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

In de detailweergave van de waarschuwing wordt het volgende weergegeven:

* Wanneer de waarschuwing is geactiveerd
* Statusinformatie over de apparaten waarvoor de waarschuwing is afgegeven
* Telemetrie van de apparaten waarvoor de waarschuwing is afgegeven

[![Waarschuwingsgegevens](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Als u de waarschuwing wilt accepteren, selecteert u alle **Voorvallen van de waarschuwing** en kiest u **Accepteren**. Door deze actie weten andere operators dat u de waarschuwing hebt gezien en ermee bezig bent:

[![De waarschuwingen erkennen](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Als u de waarschuwing hebt geaccepteerd, wordt de status van het voorval gewijzigd in **Geaccepteerd**.

In de lijst met gewaarschuwde apparaten ziet u welk **Prototypeapparaat** verantwoordelijk is voor het activeren van de temperatuurwaarschuwing:

[![De apparaten weergeven die de waarschuwing veroorzaken](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Het probleem oplossen

Als u het probleem met het **Prototypeapparaat** wilt oplossen, moet u de methode **DecreaseTemperature** aanroepen op het apparaat.

Als u actie wilt ondernemen voor een apparaat, selecteert u het apparaat in de lijst met gewaarschuwde apparaten en kiest u vervolgens **Taken**. Het model **Prototypeapparaat** biedt ondersteuning voor zes methoden:

[![De methoden weergeven die het apparaat ondersteunt](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Kies **DecreaseTemperature** en stel de taaknaam in op **DecreaseTemperature**. Klik vervolgens op **Toepassen:**

[![De taak maken om de temperatuur te verlagen](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Als u de status van de taak wilt bijhouden, klikt u op **Taakstatus bekijken**. Gebruik de weergave **Taken** om alle aanroepen voor taken en methoden in de oplossing bij te houden:

[![Controleer de taak om de temperatuur te verlagen](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

U kunt controleren of de temperatuur van het apparaat is afgenomen door de telemetrie te bekijken op de **Dashboardpagina**:

[![Bekijk de daling van de temperatuur](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u waarschuwingen kunt gebruiken om problemen met apparaten te identificeren en welke acties u kunt ondernemen om de problemen op deze apparaten op te lossen. Ga verder met de praktische artikelen voor informatie over het verbinden van een echt apparaat met de oplossingsverbetering.

Nu u hebt geleerd hoe u problemen met apparaten aanpakt, is de volgende stap die we willen voorstellen het [verbinden van uw apparaat met de verbetering voor de externe bewakingsoplossing](iot-accelerators-connecting-devices.md).
