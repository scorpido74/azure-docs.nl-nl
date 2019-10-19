---
title: 'Zelf studie: een Azure Time Series Insights omgeving maken | Microsoft Docs'
description: Zelf studie om te leren hoe u een Time Series Insights omgeving kunt maken die wordt gevuld met gegevens van gesimuleerde apparaten.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 518847db727c9d8c527d272f9122ef9850ca9135
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553042"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Zelfstudie: Een Azure Time Series Insights-omgeving maken

Deze zelf studie leidt u door het proces van het maken van een Azure Time Series Insights-omgeving die is gevuld met gegevens van gesimuleerde apparaten. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Time Series Insights omgeving maken.
> * Maak een simulatie van een apparaat met een IoT-hub.
> * Verbind de Time Series Insights-omgeving met de IoT-hub.
> * Voer een apparaat simulatie uit om gegevens in de Time Series Insights omgeving te streamen.
> * Controleer de gesimuleerde telemetrie-gegevens.

> [!IMPORTANT]
> Meld u aan voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* Uw Azure-aanmeldings account moet ook lid zijn van de rol **eigenaar** van het abonnement. Zie [toegang beheren met op rollen gebaseerd toegangs beheer en de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="review-video"></a>Video bekijken

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Meer informatie over het gebruik van een Azure IoT-oplossings versneller om gegevens te genereren en aan de slag te gaan met Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Overzicht

In de Time Series Insights omgeving worden apparaatgegevens verzameld en opgeslagen. Zodra de gegevens zijn opgeslagen, kunnen de [Azure time series Insights Explorer](time-series-quickstart.md) -en [Time Series INSIGHTS query-API](/rest/api/time-series-insights/ga-query-api) worden gebruikt voor het uitvoeren van query's en het analyseren van de informatie.

Azure IoT Hub is de gebeurtenis bron die door alle apparaten (gesimuleerd of fysiek) in de zelf studie wordt gebruikt om veilig verbinding te maken met gegevens en deze te verzenden naar uw Azure-Cloud.

In deze zelf studie wordt ook gebruikgemaakt van een [IOT-oplossings versneller](https://www.azureiotsolutions.com) voor het genereren en streamen van voor beelden van telemetriegegevens voor IOT hub.

>[!TIP]
> [IOT-oplossings Accelerators](https://www.azureiotsolutions.com) bieden vooraf geconfigureerde oplossingen op ondernemings niveau die u kunt gebruiken om de ontwikkeling van aangepaste IOT-oplossingen te versnellen.

## <a name="create-a-device-simulation"></a>Een apparaatsimulatie maken

Maak eerst de simulatie oplossing voor apparaten, waarmee test gegevens worden gegenereerd om uw Time Series Insights omgeving in te vullen.

1. Ga in een apart venster of tabblad naar [azureiotsolutions.com](https://www.azureiotsolutions.com). Meld u aan met hetzelfde account voor het Azure-abonnement en selecteer de **apparaat simulatie** Accelerator. Selecteer **Nu uitproberen**.

   [de apparaat simulatie Accelerator ![Run](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Voer de vereiste para meters in op de pagina oplossing voor het maken van een **apparaat simulatie** .

   Parameter|Beschrijving
   ---|---
   **Implementatie naam** | Deze unieke waarde wordt gebruikt om een nieuwe resource groep te maken. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep.
   **Azure-abonnement** | Geef hetzelfde abonnement op dat is gebruikt voor het maken van uw Time Series Insights-omgeving in de vorige sectie.
   **Implementatie opties** | Selecteer **nieuwe IOT hub inrichten** als u een nieuwe IOT-hub wilt maken die specifiek is voor deze zelf studie.
   **Azure-locatie** | Geef dezelfde regio op die is gebruikt voor het maken van uw Time Series Insights-omgeving in de vorige sectie.

   Wanneer u klaar bent, selecteert u **maken** om de Azure-resources van de oplossing in te richten. Het kan Maxi maal 20 minuten duren voordat dit proces is voltooid.

   [de oplossing voor simulatie van apparaten ![Provision](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Nadat het inrichten is voltooid, verandert de tekst boven uw nieuwe oplossing van **inrichting** in **gereed**.

   >[!IMPORTANT]
   > Selecteer nog niet **starten** . Laat deze webpagina geopend omdat u deze later weer gaat gebruiken.

   [inrichting van ![Device simulatie oplossing voltooid](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Inspecteer nu de zojuist gemaakte resources in het Azure Portal. Op de pagina **resource groepen** ziet u dat er een nieuwe resource groep is gemaakt met behulp van de **oplossings naam** die in de laatste stap is opgenomen. Noteer de resources die zijn gemaakt voor de simulatie van het apparaat.

   [simulatie resources ![Device](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Een omgeving maken

Maak vervolgens een Time Series Insights omgeving in uw Azure-abonnement.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw account voor uw Azure-abonnement. 
1. Selecteer linksboven **+ Een resource maken**. 
1. Selecteer de categorie **Internet of Things** en vervolgens **Time Series Insights**. 

   [![Selecteer de Time Series Insights-omgevingsresource](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Vul op de pagina **Time Series Insights omgeving** de vereiste para meters in.

   Parameter|Beschrijving
   ---|---
   **Omgevingsnaam** | Kies een unieke naam voor de Time Series Insights omgeving. De namen worden gebruikt door de Time Series Insights Explorer en de [query-api's](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Abonnement** | Abonnementen zijn containers voor Azure-resources. Kies een abonnement om de Time Series Insights omgeving te maken.
   **Resourcegroep** | Een resourcegroep is een container voor Azure-resources. Kies een bestaande resource groep of maak een nieuwe voor de resource van de Time Series Insights omgeving.
   **Locatie** | Kies een regio in het Data Center voor uw Time Series Insights omgeving. Als u extra latentie wilt voor komen, maakt u de Time Series Insights omgeving in dezelfde regio als andere IoT-resources.
   **Laag** | Kies de benodigde doorvoer. Selecteer **S1**.
   **Capaciteit** | Capaciteit is de vermenigvuldiger die wordt toegepast op de ingangs frequentie en de opslag capaciteit die is gekoppeld aan de geselecteerde SKU. U kunt de capaciteit wijzigen nadat deze is gemaakt. Selecteer een capaciteit van **1**.

   Wanneer u klaar bent, selecteert u **volgende: gebeurtenis bron** om door te gaan naar de volgende stap.

   [![Create een Time Series Insights omgevings resource](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Verbind de Time Series Insights-omgeving nu met de IoT-hub die is gemaakt met de oplossings versneller. Stel **Selecteer een hub** in op `Select existing`. Kies vervolgens de IoT-hub die is gemaakt door de oplossings versneller bij het instellen van **IOT hub naam**.

   [de Time Series Insights omgeving ![Connect naar de gemaakte IoT-hub](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

   Selecteer tot slot de optie **controleren + maken**.

1. Controleer het deel venster **meldingen** om de voltooiing van de implementatie te bewaken. 

   [implementatie van ![Time Series Insights-omgeving is voltooid](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Simulatie van apparaten uitvoeren

Nu de implementatie en de initiële configuratie zijn voltooid, vult u de Time Series Insights omgeving in met voorbeeld gegevens van [gesimuleerde apparaten die door de Accelerator zijn gemaakt](#create-a-device-simulation).

Samen met de IoT-hub is een Azure App Service-webtoepassing gegenereerd voor het maken en verzenden van gesimuleerde telemetrie van apparaten.

1. Ga terug naar het dashboard [Oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#dashboard). Meld u indien nodig opnieuw aan met behulp van hetzelfde Azure-account dat u in deze zelf studie hebt gebruikt. Selecteer uw ' oplossing voor apparaten ' en **Ga vervolgens naar uw oplossings versneller** om uw geïmplementeerde oplossing te starten.

     [dash board voor ![Solution Accelerators](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. De web-app voor Device simulatie begint met de vraag of u de webtoepassing de machtiging ' aanmelden en uw profiel lezen ' wilt verlenen. Met deze machtiging kan de toepassing de gebruikers profiel gegevens ophalen die nodig zijn om de werking van de toepassing te ondersteunen.

     [toestemming voor ![Device simulatie van webtoepassingen](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Nadat de pagina **simulatie installatie** is geladen, voert u de vereiste para meters in.

   Parameter|Beschrijving
   ---|---
   **Doel-IoT-hub** | Selecteer **vooraf ingerichte IOT hub gebruiken**.
   **Apparaatmodel** | Selecteer **Koelunit**.
   **Aantal apparaten**  | Geef `1000` op onder **bedrag**.
   **Telemetriefrequentie** | Voer `10` seconden in.
   **Simulatieduur** | Selecteer **beëindigen in:** en voer `5` minuten in.

   Wanneer u klaar bent, selecteert u **simulatie starten**. De simulatie wordt in totaal 5 minuten uitgevoerd. Er worden om de 10 seconden gegevens van 1.000 gesimuleerde apparaten gegenereerd. 

   [simulatie instelling ![Device](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Terwijl de simulatie wordt uitgevoerd, ziet u dat de velden **Totaal aantal berichten** en **berichten per seconde** worden bijgewerkt, ongeveer elke tien seconden. De simulatie eindigt na ongeveer 5 minuten en keert u terug naar het **instellen van simulaties**.

   [![Device simulatie wordt uitgevoerd](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>De telemetriegegevens verifiëren

In dit laatste gedeelte controleert u of de telemetriegegevens zijn gegenereerd en opgeslagen in de Time Series Insights omgeving. Voor het verifiëren van de gegevens gebruikt u de verkenner van Time Series Insights, die wordt gebruikt voor het opvragen en analyseren van telemetriegegevens.

1. Ga terug naar de **overzichts** pagina van de resource groep van de time series Insights omgeving. Selecteer de Time Series Insights omgeving.

   [resource groep en omgeving van de ![Time Series Insights-omgeving](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Selecteer op de pagina **overzicht** van Time Series Insights-omgeving de **Time Series INSIGHTS Explorer-URL** om time series Insights Explorer te openen.

   [![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. De Time Series Insights Explorer wordt geladen en geverifieerd met behulp van uw Azure Portal-account. U kunt bij de eerste weer gave in het grafiek gebied zien dat de Time Series Insights omgeving is gevuld met gesimuleerde telemetriegegevens. Als u een smallere periode wilt filteren, selecteert u de vervolg keuzelijst in de linkerbovenhoek. Voer een tijds bereik in dat groot genoeg is om de duur van de simulatie van het apparaat te beslaan. Selecteer vervolgens het vergroot glas voor zoeken.

   [tijds bereik filter voor ![Time Series Insights-Verkenner](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Als u het tijds bereik beperkt, kan de grafiek in-of uitzoomen op de afzonderlijke bursts van gegevens overdracht naar de IoT-hub en de Time Series Insights omgeving. U ziet ook de tekst voor het **afhandelen** van gegevens in de rechter bovenhoek, waarin het totale aantal gevonden gebeurtenissen wordt weer gegeven. U kunt ook de schuif regelaar voor de **interval grootte** slepen om de nauw keurigheid van het tekening diagram te bepalen.

   [gefilterde weer gave tijd bereik ![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Ten slotte kunt u ook met de rechter muisknop op een regio klikken om een bereik te filteren. Klik vervolgens met de rechter muisknop en gebruik **verkennen** om gebeurtenis details weer te geven in de weer gave **gebeurtenissen** in tabel vorm.

   [gefilterde weer gave en gebeurtenissen ![Time Series Insights Explorer-tijd bereik](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelf studie maakt u verschillende Azure-Services ter ondersteuning van de oplossing voor de Time Series Insights omgeving en de simulatie van apparaten. Ga terug naar de Azure Portal om ze te verwijderen.

Vanuit het menu aan de linkerkant in het Azure Portal:

1. Selecteer het pictogram **resource groepen** . Selecteer vervolgens de resource groep die u hebt gemaakt voor de Time Series Insights omgeving. Selecteer aan de bovenkant van de pagina **resource groep verwijderen**, voer de naam van de resource groep in en selecteer **verwijderen**.

1. Selecteer het pictogram **resource groepen** . Selecteer vervolgens de resource groep die is gemaakt door de apparaat simulatie oplossings versneller. Selecteer aan de bovenkant van de pagina **resource groep verwijderen**, voer de naam van de resource groep in en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Time Series Insights omgeving maken.
> * Maak een simulatie van een apparaat met een IoT-hub.
> * Verbind de Time Series Insights-omgeving met de IoT-hub.
> * Voer een apparaat simulatie uit om gegevens in de Time Series Insights omgeving te streamen.
> * Controleer de gesimuleerde telemetrie-gegevens.

Nu u weet hoe u uw eigen Time Series Insights-omgeving kunt maken, leert u hoe u een webtoepassing bouwt die gegevens uit een Time Series Insights omgeving gebruikt:

> [!div class="nextstepaction"]
> [Zie voor beelden van gehoste client-SDK-visualisaties](https://tsiclientsample.azurewebsites.net/)
