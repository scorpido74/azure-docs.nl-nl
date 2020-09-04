---
title: 'Quickstart: Beveiligingsaanbevelingen onderzoeken'
description: Onderzoek beveiligingsaanbevelingen met de Azure Security Center for IoT-beveiligingsservice.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: mlottner
ms.openlocfilehash: aa241a9108be32f88357065732c6f283d312be6c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88144000"
---
# <a name="quickstart-investigate-security-recommendations"></a>Quickstart: Beveiligingsaanbevelingen onderzoeken


Tijdige analyse en beperking van aanbevelingen door Azure Security Center for IoT is de beste manier om de beveiligingsstatus te verbeteren en het aanvalsoppervlak in uw IoT-oplossing te verminderen.

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
