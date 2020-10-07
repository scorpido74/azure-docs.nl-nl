---
title: 'Quickstart: Beveiligingsaanbevelingen onderzoeken'
description: Onderzoek beveiligingsaanbevelingen met de Defender for IoT-beveiligingsservice.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 859f1c4a1ed1b3d9139307c52f44a14e3089e31f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943784"
---
# <a name="quickstart-investigate-security-recommendations"></a>Quickstart: Beveiligingsaanbevelingen onderzoeken


Tijdige analyse en beperking van aanbevelingen door Defender for IoT is de beste manier om de beveiligingsstatus te verbeteren en het aanvalsoppervlak in uw IoT-oplossing te verkleinen.

In deze quickstart verkennen we de informatie die beschikbaar is in elke IoT-beveiligingsaanbeveling, en wordt uitgelegd hoe u kunt inzoomen en de details van elke aanbeveling en het bijbehorende apparaat kunt gebruiken om risico’s te voorkomen.

Aan de slag.

## <a name="investigate-new-recommendations"></a>Nieuwe aanbevelingen onderzoeken

In de lijst met IoT Hub-aanbevelingen worden alle geaggregeerde beveiligingsaanbevelingen voor uw IoT Hub weergegeven.

1.  Open in Azure Portal de  **IoT Hub**  die u wilt onderzoeken op nieuwe aanbevelingen.

1.  Selecteer in het  **Beveiliging**-menu    **Aanbevelingen**. Alle beveiligingsaanbevelingen voor de IoT Hub worden weergegeven en de aanbevelingen met een markering  **Nieuw**   markeren uw aanbevelingen van de afgelopen 24 uur. 

    [ ![Beveiligingsaanbevelingen met ASC voor IoT onderzoeken](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  Selecteer en open een aanbeveling in de lijst om de details van de aanbeveling te openen en in te zoomen op de specificaties van de waarschuwing.

## <a name="security-recommendation-details"></a>Details van de beveiligingsaanbeveling

Open elke geaggregeerde aanbeveling om de gedetailleerde beschrijving van de aanbeveling, herstelstappen en apparaat-id weer te geven voor elk apparaat dat een aanbeveling heeft geactiveerd. Ook wordt de ernst van de aanbeveling en directe toegang tot onderzoek weergegeven met behulp van Log Analytics.

1.  Selecteer en open een beveiligingsaanbeveling in de lijst  **IoT Hub** \> **Beveiliging** \> **Aanbevelingen** .

1.  Bekijk de **beschrijving** en  **ernst** van de aanbeveling en de  **apparaatgegevens**  van alle apparaten die deze aanbeveling tijdens de aggregatie hebben uitgegeven. 

1.  Nadat u de details van de aanbeveling hebt bekeken, gebruikt u de instructies uit de  **stap handmatig herstel**  om het probleem dat de aanbeveling heeft veroorzaakt, te herstellen en op te lossen. 

    [ ![Beveiligingsaanbevelingen met ASC voor IoT opvolgen](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  Bekijk de aanbevelingsdetails voor een specifiek apparaat door het gewenste apparaat te selecteren op de inzoompagina.

    [![Onderzoek specifieke beveiligingsaanbevelingen voor een apparaat met ASC voor IoT](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  Als verder onderzoek is vereist,  **onderzoek dan de aanbevelingen in Log Analytics**  met behulp van de koppeling. 


## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel om te lezen hoe u aangepaste waarschuwingen maakt.

> [!div class="nextstepaction"]
> [Aangepaste waarschuwingen maken](quickstart-create-custom-alerts.md)
