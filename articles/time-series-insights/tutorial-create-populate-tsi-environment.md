---
title: 'Zelfstudie: Een omgeving maken - Azure Time Series Insights | Microsoft Documenten'
description: Meer informatie over het maken van een Time Series Insights-omgeving die is gevuld met gegevens van gesimuleerde apparaten.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 7bebc9e682f5156fa235b77ff020e502695a28be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76981188"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Zelfstudie: Een Azure Time Series Insights-omgeving maken

Deze zelfstudie begeleidt u bij het maken van een Azure Time Series Insights-omgeving die is gevuld met gegevens van gesimuleerde apparaten. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een Time Series Insights-omgeving.
> * Maak een apparaatsimulatieoplossing die een IoT-hub bevat.
> * Verbind de Time Series Insights-omgeving met de IoT-hub.
> * Voer een apparaatsimulatie uit om gegevens te streamen naar de Time Series Insights-omgeving.
> * Controleer de gesimuleerde telemetriegegevens.

> [!IMPORTANT]
> Meld u aan voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* Uw Azure-aanmeldingsaccount moet ook lid zijn van de **rol Eigenaar** van het abonnement. Lees [Toegang beheren met behulp van op rollen gebaseerd toegangsbeheer en de Azure-portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="review-video"></a>Video bekijken

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Meer informatie over het gebruik van een Azure IoT-oplossingsversneller om gegevens te genereren en aan de slag te gaan met Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Overzicht

De Time Series Insights-omgeving is de plaats waar apparaatgegevens worden verzameld en opgeslagen. Eenmaal opgeslagen, kunnen de [Azure Time Series Insights explorer](time-series-quickstart.md) en Time Series Insights Query [API](/rest/api/time-series-insights/ga-query-api) worden gebruikt om de gegevens op te vragen en te analyseren.

Azure IoT Hub is de gebeurtenisbron die door alle apparaten (gesimuleerd of fysiek) wordt gebruikt in de zelfstudie om veilig gegevens te verbinden en te verzenden naar uw Azure-cloud.

Deze zelfstudie maakt ook gebruik van een [IoT-oplossingsversneller](https://www.azureiotsolutions.com) om voorbeeldtelemetriegegevens te genereren en te streamen naar IoT Hub.

>[!TIP]
> [IoT-oplossingsversnellers](https://www.azureiotsolutions.com) bieden vooraf geconfigureerde oplossingen van bedrijfsniveau die u gebruiken om de ontwikkeling van aangepaste IoT-oplossingen te versnellen.

## <a name="create-a-device-simulation"></a>Een apparaatsimulatie maken

Maak eerst de apparaatsimulatieoplossing, die testgegevens genereert om uw Time Series Insights-omgeving te vullen.

1. Ga in een apart venster of tabblad naar [azureiotsolutions.com](https://www.azureiotsolutions.com). Meld u aan met hetzelfde Azure-abonnementsaccount en selecteer het **accelerator voor apparaatsimulatie.**

   [![De oplossingsverbetering Apparaatsimulatie uitvoeren](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Selecteer **Nu uitproberen**. Voer vervolgens de vereiste parameters in op de pagina **Apparaatsimulatie maken.**

   Parameter|Beschrijving
   ---|---
   **Implementatienaam** | Deze unieke waarde wordt gebruikt om een nieuwe resourcegroep te maken. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep.
   **Azure-abonnement** | Geef hetzelfde abonnement op dat in de vorige sectie is gebruikt om uw Time Series Insights-omgeving te maken.
   **Implementatieopties** | Selecteer **Nieuwe IoT-hub inrichten** om een nieuwe IoT-hub te maken die specifiek is voor deze zelfstudie.
   **Azure-locatie** | Geef hetzelfde gebied op als dat is gebruikt om uw Time Series Insights-omgeving in de vorige sectie te maken.

   Wanneer u klaar bent, selecteert u **Maken** om de Azure-resources van de oplossing in te richten. Het kan tot 20 minuten duren voordat dit proces is voltooid.

   [![De apparaatsimulatieoplossing inrichten](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Nadat de inrichting is voltooid, worden twee updates weergegeven met de melding dat de implementatiestatus is verplaatst van **Provisioning** naar **Ready.** 

   >[!IMPORTANT]
   > Voer uw oplossingsversneller nog niet in! Houd deze webpagina open omdat u er later op terugkomt.

   [![Het inrichten van de apparaatsimulatieoplossing is voltooid](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Inspecteer nu de nieuw gemaakte resources in de Azure-portal. Op de pagina **Resourcegroepen** wordt opgemerkt dat een nieuwe resourcegroep is gemaakt met de **naam Oplossing** in de laatste stap. Noteer de bronnen die zijn gemaakt voor de apparaatsimulatie.

   [![Apparaatsimulatiebronnen](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Een omgeving maken

Ten tweede maakt u een Time Series Insights-omgeving in uw Azure-abonnement.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-abonnementsaccount. 
1. Selecteer linksboven **+ Een resource maken**. 
1. Selecteer de categorie **Internet of Things** en vervolgens **Time Series Insights**. 

   [![Selecteer de resource van Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Vul op de pagina **Time Series Insights** de vereiste parameters in.

   Parameter|Beschrijving
   ---|---
   **Omgevingsnaam** | Kies een unieke naam voor de Time Series Insights-omgeving. De namen worden gebruikt door de Time Series Insights explorer en de [Query API's](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Abonnement** | Abonnementen zijn containers voor Azure-resources. Kies een abonnement om de Time Series Insights-omgeving te maken.
   **Resourcegroep** | Een resourcegroep is een container voor Azure-resources. Kies een bestaande resourcegroep of maak een nieuwe voor de resource time series Insights.
   **Locatie** | Kies een datacenterregio voor uw Time Series Insights-omgeving. Maak de Time Series Insights-omgeving in dezelfde regio als andere IoT-resources om extra latentie te voorkomen.
   **Laag** | Kies de benodigde doorvoer. Selecteer **S1**.
   **Capaciteit** | Capaciteit is de multiplier die wordt toegepast op de invallende snelheid en opslagcapaciteit die is gekoppeld aan de geselecteerde SKU. U de capaciteit na het maken wijzigen. Selecteer een capaciteit van **1**.

   Als u klaar bent, selecteert u **Volgende: gebeurtenisbron** om door te gaan naar de volgende stap.

   [![Een Time Series Insights-omgevingsresource maken](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Verbind nu de Time Series Insights-omgeving met de IoT-hub die is gemaakt door de Solution Accelerator. **Een hub** selecteren `Select existing`instellen op . Kies vervolgens de IoT-hub die door de Oplossingsversneller is gemaakt bij het instellen van **de naam van de IoT-hub.**

   [![Verbind de Time Series Insights-omgeving met de gemaakte IoT-hub](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Tot slot selecteert u **Controleren + maken**.

1. Controleer het deelvenster **Meldingen** om de voltooiing van de implementatie te controleren. 

   [![Time Series Insights omgeving implementatie is voltooid](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Apparaatsimulatie uitvoeren

Nu de implementatie en de eerste configuratie is voltooid, vult u de Time Series Insights-omgeving in met voorbeeldgegevens van [gesimuleerde apparaten die door de accelerator zijn gemaakt.](#create-a-device-simulation)

Samen met de IoT-hub is een Azure App Service-webtoepassing gegenereerd om gesimuleerde apparaattelemetrie te maken en te verzenden.

1. Ga terug naar het dashboard [Oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#dashboard). Meld u indien nodig opnieuw aan met hetzelfde Azure-account dat u in deze zelfstudie hebt gebruikt. Selecteer uw "apparaatoplossing" en **ga vervolgens naar uw oplossingsversneller om** uw geïmplementeerde oplossing te starten.

   [![Dashboard Oplossingsverbeteringen](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. De web-app voor apparaatsimulatie begint met de vraag u om de webtoepassing de aanmelding te verlenen **en uw profieltoestemming te lezen.** Met deze toestemming kan de toepassing de gebruikersprofielgegevens ophalen die nodig zijn om de werking van de toepassing te ondersteunen.

   [![Toestemming voor webtoepassing voor apparaatsimulatie](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Selecteer **+ Nieuwe simulatie**. Voer de vereiste parameters in nadat de **pagina Simulatie-instelling** is geladen.

   Parameter|Beschrijving
   ---|---
   **Doel-IoT-hub** | Selecteer **Vooraf ingerichte IoT-hub gebruiken**.
   **Apparaatmodel** | Selecteer **Koelunit**.
   **Aantal apparaten**  | Voer `10` in onder **Bedrag**.
   **Telemetriefrequentie** | Voer `10` seconden in.
   **Simulatieduur** | Selecteer **Einde in:** en voer minuten in. `5`

   Wanneer u klaar bent, selecteert u **Simulatie starten**. De simulatie duurt in totaal 5 minuten. Het genereert gegevens van 1.000 gesimuleerde apparaten elke 10 seconden. 

   [![Apparaatsimulatie instellen](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Terwijl de simulatie wordt uitgevoerd, wordt opgemerkt dat de **velden Totaal** en Berichten **per seconde** worden bijgewerkt, ongeveer elke 10 seconden. De simulatie eindigt na ongeveer 5 minuten en brengt u terug naar **Simulatie setup**.

   [![Apparaatsimulatie wordt uitgevoerd](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>De telemetriegegevens verifiëren

In dit laatste gedeelte controleert u of de telemetriegegevens zijn gegenereerd en opgeslagen in de time-serie Insights-omgeving. Voor het verifiëren van de gegevens gebruikt u de verkenner van Time Series Insights, die wordt gebruikt voor het opvragen en analyseren van telemetriegegevens.

1. Ga terug naar de **overzichtspagina** van de resourcegroep Overzicht van de resourcegroep van de Time Series Insights-omgeving. Selecteer de time series Insights-omgeving.

   [![Time Series Insights-brongroep en -omgeving](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Selecteer op de pagina Time Series Insights-omgeving **overzicht** de **URL van Time Series Insights explorer** om de Time Series Insights explorer te openen.

   [![Verkenner van Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. De Time Series Insights explorer laadt en verifieert met behulp van uw Azure-portalaccount. In eerste instantie wordt het grafiekgebied waarmee de Time Series Insights-omgeving werd gevuld, samen met de gesimuleerde telemetriegegevens weergegeven. Als u een smaller tijdsbereik wilt filteren, selecteert u de vervolgkeuzelijst in de linkerbovenhoek. Voer een tijdsbereik in dat groot genoeg is om de duur van de apparaatsimulatie te verlengen. Selecteer vervolgens het zoekvergrootglas.

   [![Time Series Insights explorer-tijdbereikfilter](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Door het tijdsbereik te verkleinen, kan de grafiek inzoomen op de verschillende uitbarstingen van gegevensoverdracht naar de IoT-hub en de Time Series Insights-omgeving. Let ook op de volledige tekst **Streaming** in de rechterbovenhoek, die het totale aantal gevonden gebeurtenissen weergeeft. U ook de schuifregelaar **Intervalgrootte** slepen om de granulariteit van de plot in de grafiek te bepalen.

   [![Gefilterde weergave Time Series Insights explorer-tijdsbereik](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Ten slotte u ook linksop een gebied klikken om een bereik te filteren. Klik vervolgens met de rechtermuisknop en gebruik **Gebeurtenissen verkennen** om gebeurtenisgegevens weer te geven in de tabelweergave **Gebeurtenissen.**

   [![Time Series Insights explorer tijdbereik gefilterde weergave en gebeurtenissen](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt verschillende azure-services voor de time-serie Insights-omgeving en apparaatsimulatieoplossing. Als u ze wilt verwijderen, navigeert u terug naar de Azure-portal.

In het menu aan de linkerkant in de Azure-portal:

1. Selecteer het pictogram **Resourcegroepen.** Selecteer vervolgens de resourcegroep die u hebt gemaakt voor de time series Insights-omgeving. Selecteer boven aan de pagina **De brongroep verwijderen,** voer de naam van de brongroep in en selecteer **Verwijderen**.

1. Selecteer het pictogram **Resourcegroepen.** Selecteer vervolgens de resourcegroep die is gemaakt door de apparaatsimulatieoplossingsversneller. Selecteer boven aan de pagina **De brongroep verwijderen,** voer de naam van de brongroep in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Maak een Time Series Insights-omgeving.
> * Maak een apparaatsimulatieoplossing die een IoT-hub bevat.
> * Verbind de Time Series Insights-omgeving met de IoT-hub.
> * Voer een apparaatsimulatie uit om gegevens te streamen naar de Time Series Insights-omgeving.
> * Controleer de gesimuleerde telemetriegegevens.

Nu u weet hoe u uw eigen Time Series Insights-omgeving maken, leert u hoe u een webtoepassing maken die gegevens verbruikt uit een Time Series Insights-omgeving:

> [!div class="nextstepaction"]
> [Voorbeelden van gehoste SDK-visualisatievan client lezen](https://tsiclientsample.azurewebsites.net/)
