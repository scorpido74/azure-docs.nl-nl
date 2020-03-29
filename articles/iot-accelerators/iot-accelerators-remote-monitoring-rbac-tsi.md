---
title: Beheer van gegevenstoegangscontrole op afstand - Azure | Microsoft Documenten
description: In dit artikel vindt u informatie over hoe u toegangsbesturingselementen voor de telemetrieverkenner Time Series Insights configureren in de accelerator voor oplossingen voor externe bewaking
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65827240"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Toegangsbesturingselementen configureren voor de telemetrieverkenner Time Series Insights

In dit artikel vindt u informatie over het configureren van toegangscontroles voor de Time Series Insights explorer in de accelerator voor externe monitoringoplossingen. Als u gebruikers van de oplossingsversneller toegang wilt geven tot de Time Series Insights-verkenner, moet u elke gebruiker toegang verlenen tot gegevens.

Beleid voor gegevenstoegang verleent machtigingen voor het uitvoeren van gegevensquery's, het bewerken van referentiegegevens in de omgeving en het delen van opgeslagen query's en perspectieven die aan de omgeving zijn gekoppeld.

## <a name="grant-data-access"></a>Gegevenstoegang verlenen

Volg de volgende stappen om gegevenstoegang te verlenen aan een gebruikersprincipal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek uw Time Series Insights-omgeving. Typ **Tijdreeks** in het **zoekvak.** Selecteer **Tijdreeksomgeving** in de zoekresultaten. 

3. Selecteer uw Time Series Insights-omgeving in de lijst.

4. Selecteer **Beleid voor gegevenstoegang**en selecteer **+ Toevoegen**.
    ![Beheren van de Time Series Insights-bron - omgeving](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Selecteer **Gebruiker selecteren**.  Zoek naar de gebruikersnaam of het e-mailadres om de gebruiker te vinden die u wilt toevoegen. Klik **op Selecteren** om de selectie te bevestigen. 

    ![Beheren van de Time Series Insights-bron - toevoegen](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Selecteer **Rol selecteren**. Kies de juiste toegangsrol voor de gebruiker:
   - Selecteer **Bijdrager** als u de gebruiker wilt toestaan referentiegegevens te wijzigen en opgeslagen query's en perspectieven te delen met andere gebruikers van de omgeving. 
   - Selecteer anders **Reader** om gebruikersquerygegevens in de omgeving toe te staan en persoonlijke (niet gedeelde) query's op te slaan in de omgeving.

     Selecteer **Ok** om de rolkeuze te bevestigen.

     ![Beheren van de Time Series Insights-bron - gebruiker selecteren](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Selecteer **Ok** op de pagina **Gebruikersrol selecteren.**

    ![Beheren van de Time Series Insights-bron - rol selecteren](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Op de pagina **Beleid voor gegevenstoegang** worden de gebruikers en de rol(en) voor elke gebruiker weergegeven.

    ![Beheren van de Time Series Insights-bron - resultaten](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe toegangsbesturingselementen worden verleend voor de Time Series Insights-verkenner in de accelerator voor externe monitoringoplossingen.

Zie [Remote Monitoring-architectuur](iot-accelerators-remote-monitoring-sample-walkthrough.md) voor meer conceptuele informatie over de remote monitoring-oplossingsversneller

Zie [Een microservice aanpassen en opnieuw implementeren](iot-accelerators-microservices-example.md) voor meer informatie over het aanpassen van de oplossing voor externe bewaking
<!-- Next tutorials in the sequence -->