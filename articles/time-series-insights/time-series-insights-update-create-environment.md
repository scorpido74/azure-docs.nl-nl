---
title: 'Zelfstudie: Een voorbeeldomgeving instellen - Azure Time Series Insights | Microsoft Documenten'
description: 'Zelfstudie: Meer informatie over het instellen van een omgeving in Azure Time Series Insights Preview.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 856410f2166ae442e8beadf36eac312748407b13
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529735"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Zelfstudie: Een Azure Time Series Insights Preview-omgeving instellen

Deze zelfstudie begeleidt u bij het maken van een Azure Time Series Insights Preview *pay-as-you-go* (PAYG) omgeving.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Time Series Insights Preview-omgeving maken.
> * Verbind de Azure Time Series Insights Preview-omgeving met een IoT-hub.
> * Een voorbeeldoplossingsverbetering uitvoeren om gegevens te streamen naar de Azure Time Series Insights Preview-omgeving.
> * Voer een eenvoudige analyse van de gegevens uit.
> * Een type Time Series Model en een hiërarchie definiëren en deze koppelen aan uw exemplaren.

>[!TIP]
> [IoT-oplossingsversnellers](https://www.azureiotsolutions.com/Accelerators) bieden vooraf geconfigureerde oplossingen van bedrijfsniveau die u gebruiken om de ontwikkeling van aangepaste IoT-oplossingen te versnellen.

Meld u aan voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* U moet minimaal de **rol inzender** hebben voor het Azure-abonnement. Lees [Toegang beheren met behulp van op rollen gebaseerd toegangsbeheer en de Azure-portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="create-a-device-simulation"></a>Een apparaatsimulatie maken

In deze sectie maakt u drie gesimuleerde apparaten die gegevens verzenden naar een Azure IoT Hub-instantie.

1. Ga naar [de pagina Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators). Op de pagina worden enkele vooraf gedefinieerde voorbeelden weergegeven. Meld u aan met uw Azure-account. Selecteer vervolgens **Apparaatsimulatie**.

   [![Azure IoT-oplossing versnellers pagina.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Selecteer **Nu proberen**op de volgende pagina . Voer vervolgens de vereiste parameters in op de pagina **Apparaatsimulatie maken.**

   Parameter|Beschrijving
   ---|---
   **Implementatienaam** | Deze unieke waarde wordt gebruikt om een nieuwe resourcegroep te maken. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep.
   **Azure-abonnement** | Geef hetzelfde abonnement op dat in de vorige sectie is gebruikt om uw Time Series Insights-omgeving te maken.
   **Implementatieopties** | Selecteer **Nieuwe IoT-hub inrichten** om een nieuwe IoT-hub te maken die specifiek is voor deze zelfstudie.
   **Azure-locatie** | Geef hetzelfde gebied op als dat is gebruikt om uw Time Series Insights-omgeving in de vorige sectie te maken.

   Wanneer u klaar bent, selecteert u **Maken** om de Azure-resources van de oplossing in te richten. Het kan tot 20 minuten duren voordat dit proces is voltooid.

   [![Inrichten van de apparaatsimulatieoplossing.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Nadat de inrichting is voltooid, worden twee meldingen weergegeven waarin wordt aangekondigd dat de implementatiestatus is verplaatst van **Provisioning** naar **Ready**. 

   >[!IMPORTANT]
   > Voer uw oplossingsversneller nog niet in! Houd deze webpagina open omdat u er later op terugkomt.

   [![Inrichting van apparaatsimulatieoplossingen voltooid.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Inspecteer nu de nieuw gemaakte resources in de Azure-portal. Op de pagina **Resourcegroepen** wordt opgemerkt dat een nieuwe resourcegroep is gemaakt met de **naam Oplossing** in de laatste stap. Noteer de bronnen die zijn gemaakt voor de apparaatsimulatie.

   [![Apparaatsimulatiebronnen.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Een PREVIEW-PAYG-omgeving maken

In deze sectie wordt beschreven hoe u een Azure Time Series Insights Preview-omgeving maakt en deze aansluit op de IoT-hub die is gemaakt door de IoT Solution Accelerator met behulp van de [Azure-portal.](https://portal.azure.com/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-abonnementsaccount. 
1. Selecteer linksboven **+ Een resource maken**. 
1. Selecteer de categorie **Internet of Things** en vervolgens **Time Series Insights**. 

   [![Selecteer de resource van Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. Stel in het **deelvenster Inzichten in de tijdreeks maken** op het tabblad **Basisbeginselen** de volgende parameters in:

    | Parameter | Actie |
    | --- | ---|
    | **Omgevingsnaam** | Voer een unieke naam in voor de Azure Time Series Insights Preview-omgeving. |
    | **Abonnement** | Voer het abonnement in waar u de Azure Time Series Insights Preview-omgeving wilt maken. Een aanbevolen manier is om hetzelfde abonnement te gebruiken als de rest van de IoT-resources die zijn gemaakt door de apparaatsimulator. |
    | **Resourcegroep** | Selecteer een bestaande resourcegroep of maak een nieuwe resourcegroep voor de azure time series Insights Preview-omgevingbron. Een resourcegroep is een container voor Azure-resources. Een aanbevolen manier is om dezelfde resourcegroep te gebruiken als de andere IoT-resources die door de apparaatsimulator zijn gemaakt. |
    | **Locatie** | Selecteer een datacentergebied voor uw Azure Time Series Insights Preview-omgeving. Om extra latentie te voorkomen, u het beste uw Azure Time Series Insights Preview-omgeving maken in dezelfde regio als uw IoT-hub die is gemaakt door de apparaatsimulator. |
    | **Laag** |  Selecteer **PAYG** (*pay-as-you-go).* Dit is de SKU voor het Azure Time Series Insights Preview-product. |
    | **Naam van eigenschap** | Voer een waarde in die uw tijdreeksinstantie op unieke wijze identificeert. De waarde die u invoert in het vak **Eigenschap-ID** kan later niet worden gewijzigd. Voer voor deze zelfstudie ***iothub-connection-device-id in.*** Lees Aanbevolen procedures voor het kiezen van een Time Series ID voor meer informatie over time series [ID.](./time-series-insights-update-how-to-id.md) |
    | **Naam van het opslagaccount** | Voer een wereldwijd unieke naam in voor een nieuw opslagaccount.|
    |**Warme winkel inschakelen**|Selecteer **Ja** om warme winkel in te schakelen. U later terugkomen en deze instelling inschakelen. |
    |**Gegevensbewaring (in dagen)**|Kies de standaardoptie van 7 dagen. |

    Selecteer **Volgende: Gebeurtenisbron**.

   [![Nieuwe Time Series Insights-omgevingsconfiguratie.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![Configureren Tijdreeks-ID voor de omgeving.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Stel op het tabblad **Gebeurtenisbron** de volgende parameters in:

   | Parameter | Actie |
   | --- | --- |
   | **Een gebeurtenisbron maken?** | Selecteer **Ja**.|
   | **Naam** | Voer een unieke waarde in voor de naam van de gebeurtenisbron. |
   | **Brontype** | Selecteer **IoT-hub**. |
   | **Een hub selecteren** | Kies **Bestaande selecteren**. |
   | **Abonnement** | Selecteer het abonnement dat u hebt gebruikt voor de apparaatsimulator. |
   | **Naam IoT-hub** | Selecteer de Naam van de IoT-hub die u hebt gemaakt voor de apparaatsimulator. |
   | **IoT Hub-toegangsbeleid** | Selecteer **iothubowner**. |
   | **IoT Hub-consumentengroep** | Selecteer **Nieuw,** voer een unieke naam in en selecteer **+ Toevoegen**. De consumentengroep moet een unieke waarde hebben in Azure Time Series Insights Preview. |
   | **Timestamp-eigenschap** | Deze waarde wordt gebruikt om de eigenschap **Timestamp** in uw binnenkomende telemetriegegevens te identificeren. Laat deze lesvoor deze lesstudie leeg. In deze simulator wordt de binnenkomende timestamp van IoT Hub gebruikt waarop Time Series Insights standaard is ingesteld. |

   Selecteer **Controleren + maken**.

   [![Configureer de gemaakte IoT-hub als gebeurtenisbron.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selecteer **Maken**.

    [![Review + Pagina maken, met knop Maken.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    U de status van uw implementatie bekijken:

    [![Melding dat de implementatie is voltooid.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. U hebt standaard toegang tot uw Azure Time Series Insights Preview-omgeving als u eigenaar bent van het Azure-abonnement. Controleer of je toegang hebt:

   1. Zoek naar uw resourcegroep en selecteer vervolgens de nieuw gemaakte Azure Time Series Insights Preview-omgeving. 

      [![Selecteer en bekijk uw omgeving.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Selecteer op de pagina Voorvertoning gegevenstoegang van Azure Time Series de optie **Beleid voor gegevenstoegang:**

      [![Beleid voor gegevenstoegang verifiëren.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Controleer of uw referenties worden vermeld:

      Als uw referenties niet worden vermeld, moet u uzelf toestemming geven om toegang te krijgen tot de omgeving door Toevoegen en zoeken naar uw referenties te selecteren. Raadpleeg [Gegevenstoegang verlenen](./time-series-insights-data-access.md) voor meer informatie over het instellen van machtigingen.

## <a name="stream-data"></a>Gegevens streamen

Nu u uw Time Series Insights-omgeving hebt geïmplementeerd, begint u met het streamen van gegevens voor analyse.

1. Ga terug naar het dashboard [Oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#dashboard). Meld u indien nodig opnieuw aan met hetzelfde Azure-account dat u in deze zelfstudie hebt gebruikt. Selecteer uw "apparaatoplossing" en **ga vervolgens naar uw oplossingsversneller om** uw geïmplementeerde oplossing te starten.

   [![Oplossing versnellers dashboard.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. De web-app voor apparaatsimulatie begint met de vraag u om de webtoepassing de aanmelding te verlenen **en uw profieltoestemming te lezen.** Met deze toestemming kan de toepassing de gebruikersprofielgegevens ophalen die nodig zijn om de werking van de toepassing te ondersteunen.

   [![Toestemming voor apparaatsimulatiewebtoepassingen.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selecteer **+ Nieuwe simulatie**. Voer de vereiste parameters in nadat de **pagina Simulatie-instelling** is geladen.

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer een unieke naam voor een simulator in. |
    | **Beschrijving** | Voer een definitie in. |
    | **Simulatieduur** | Stel de simulatieduur in op **Voor onbepaalde tijd uitvoeren**. |
    | **Apparaatmodel** | Klik op + **Een apparaattype toevoegen** <br />**Naam**: Enter **Elevator**. <br />**Bedrag**: Voer **3**in . <br /> De resterende standaardwaarden verlaten |
    | **Doel-IoT-hub** | Stel **Vooraf ingerichte IoT-hub gebruiken** in. |

    [![Parameters configureren en starten.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Selecteer **Simulatie starten**.

    In het dashboard voor apparaatsimulatie worden **actieve apparaten** en **Totaal-berichten** weergegeven.

    [![Azure IoT-simulatiedashboard.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Gegevens analyseren

In deze sectie voert u een eenvoudige analyse uit op uw tijdreeksgegevens met de [verkenner van Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Ga naar de verkenner van Azure Time Series Insights Preview door de URL te selecteren op de resourcepagina in [Azure Portal](https://portal.azure.com/).

    [![De URL van de Time Series Insights Preview explorer.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. In de Time Series Insights-verkenner verschijnt een balk boven aan het scherm. Dit is uw beschikbaarheidskiezer. Zorg ervoor dat u ten minste twee 2 m hebt geselecteerd en vouw indien nodig het tijdsbestek uit door de kiezergrepen naar links en rechts te selecteren en te slepen.

1. **Time Series Instances** worden aan de linkerkant weergegeven.

    [![Lijst met niet-bovenliggende exemplaren.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selecteer de eerste serie-instantie. Selecteer vervolgens **Temperatuur weergeven**.

    [![Geselecteerde tijdreeksinstantie met menuopdracht om de gemiddelde temperatuur weer te geven.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Er wordt een tijdreeksdiagram weergegeven. Wijzig het **interval** in **30s**.

1. Herhaal de vorige stap met de andere twee tijdreeksexemplaren, zodat u alle drie bekijkt, zoals in deze grafiek wordt weergegeven:

    [![Grafiek voor alle tijdreeksen.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Selecteer de tijdspannekiezer in de rechterbovenhoek. Hier u specifieke begin- en eindtijden tot op de milliseconde selecteren of kiezen uit vooraf geconfigureerde opties zoals **Laatste 30 minuten.** U ook de standaardtijdzone wijzigen.

    [![Stel het tijdsbereik in op de laatste 30 minuten.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    De voortgang van de oplossingsversneller in de **afgelopen 30 minuten** wordt nu weergegeven in de Time Series Insights-verkenner.

## <a name="define-and-apply-a-model"></a>Een model definiëren en toepassen

In dit gedeelte past u een model toe om uw gegevens te structureren. U definieert typen, hiërarchieën en exemplaren om het model te voltooien. Lees [Time Series Model](./time-series-insights-update-tsm.md)voor meer informatie over gegevensmodellering.

1. Selecteer het tabblad **Model** in de verkenner:

   [![Het tabblad Model weergeven in de verkenner.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Selecteer **op** het tabblad Typen de optie **+ Toevoegen**.

1. Voer de volgende parameters in:

    | Parameter | Actie |
    | --- | ---|
    | **Naam** | Voer **Lift** |
    | **Beschrijving** | Enter This is a type definition for Elevator Enter **This is a type definition for Elevator Enter This is a type definition for Elevator Enter This** |

1. Selecteer vervolgens het tabblad **Variabelen.** 

   Selecteer **+ Voeg variabele toe** en vul de volgende waarden in voor de eerste variabele van het type Lift. Je maakt in totaal drie variabelen.

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **Gemiddelde temperatuur** in. |
    | **Soort** | **Numeriek selecteren** |
    | **Waarde** | Selecteer uit voorinstelling: Selecteer **temperatuur (Dubbel)**. <br /> Opmerking: het kan enkele minuten duren voordat **Waarde** automatisch wordt ingevuld nadat Azure Time Series Insights Preview gebeurtenissen begint te ontvangen.|
    | **Aggregatiebewerking** | **Geavanceerde opties**uitbreiden . <br /> Selecteer **GEMIDDELDE**. |

    Selecteer **Toepassen**. Vervolgens **+ Variabele** opnieuw toevoegen en de volgende waarden instellen:

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **Avg Vibration in.** |
    | **Soort** | **Numeriek selecteren** |
    | **Waarde** | Selecteer uit de voorinstelling: Selecteer **trillingen (Dubbel)**. <br /> Opmerking: het kan enkele minuten duren voordat **Waarde** automatisch wordt ingevuld nadat Azure Time Series Insights Preview gebeurtenissen begint te ontvangen.|
    | **Aggregatiebewerking** | **Geavanceerde opties**uitbreiden . <br /> Selecteer **GEMIDDELDE**. |

    Selecteer **Toepassen**. Vervolgens **+ Variabele** opnieuw toevoegen en de volgende waarden instellen voor de derde en laatste variabele:

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **de vloer**in. |
    | **Soort** | Categorisch **selecteren** |
    | **Waarde** | Selecteer uit voorinstelling: Selecteer **Vloer (Dubbel)**. <br /> Opmerking: het kan enkele minuten duren voordat **Waarde** automatisch wordt ingevuld nadat Azure Time Series Insights Preview gebeurtenissen begint te ontvangen.|
    | **Categorieën** | <span style="text-decoration: underline">Labelwaarden </span>   -  <span style="text-decoration: underline">Values</span> <br /> Lager: 1,2,3,4 <br /> Midden: 5,6,7,8,9 <br /> Boven: 10,11,12,13,14,15 |
    | **Standaardrubriek** | Voer **Onbekend** in |

    [![Tekstvariabelen toevoegen.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Selecteer **Toepassen**.

1. Selecteer **Opslaan**. Er worden drie variabelen gemaakt en weergegeven.

    [![Bekijk het na het toevoegen van het type in de modelweergave.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selecteer het tabblad **Hiërarchieën.** Selecteer vervolgens **+ Toevoegen**.
   
   Stel in het deelvenster **Hiërarchie bewerken** de volgende parameters in:

   | Parameter | Actie |
   | --- | ---|
   | **Naam** | Voer **Locatiehiërarchie** in. |
   |**Niveaus**| **Voer Land** in als de naam van het eerste niveau <br> Selecteren **+ Niveau toevoegen** <br> Voer **De stad** in voor het tweede niveau en selecteer + Niveau **toevoegen** <br> Voer **Gebouw in** als de naam van het derde en laatste niveau |

   Selecteer **Opslaan**.

   [![Uw nieuwe hiërarchie weergeven in de modelweergave.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navigeer naar **Instanties**. Selecteer **onder Handelingen** uiterst rechts en selecteer het potloodpictogram om de eerste instantie met de volgende waarden te bewerken:

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Voer **Lift 1**|
    | **Beschrijving** | Voer **instantie voor lift 1 in** |

    Navigeer naar **instantievelden** en voer de volgende waarden in:

    | Parameter | Actie |
    | --- | --- |
    | **Hiërarchieën** | **Locatiehiërarchie selecteren** |
    | **Land** | Voer **de VS** in |
    | **Stad** | Voer **Seattle** in |
    | **Bouwen** | **Ruimtenaald** invoeren |

    Selecteer **Opslaan**.

1. Herhaal de vorige stap met de andere twee instanties terwijl u de volgende waarden gebruikt:

    **Voor lift 2:**

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Voer **Lift 2**|
    | **Beschrijving** | Voer **Instantie voor Lift 2 in** |
    | **Hiërarchieën** | **Locatiehiërarchie selecteren** |
    | **Land** | Voer **de VS** in |
    | **Stad** | Voer **Seattle** in |
    | **Bouwen** | Voer **Pacific Science Center** |

    **Voor lift 3:**

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Voer **lift 3**|
    | **Beschrijving** | Voer **instantie voor lift 3 in** |
    | **Hiërarchieën** | **Locatiehiërarchie selecteren** |
    | **Land** | Voer **de VS** in |
    | **Stad** | Voer **New York in** |
    | **Bouwen** | Voer **Empire State Building** |

    [![Bekijk de bijgewerkte exemplaren.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Navigeer terug naar het tabblad **Analyseren** om het grafiekvenster weer te geven. Vouw **onder Locatiehiërarchie**alle hiërarchieniveaus uit om de tijdreeksinstanties weer te geven:

    [![Alle hiërarchieën weergeven in de grafiekweergave.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Selecteer **onder Pacific Science Center**de Time Series Instance Elevator **2**en selecteer Vervolgens Gemiddelde **temperatuur weergeven**.

1. In hetzelfde exemplaar, **Lift 2,** selecteer **Beursvloer**.

    Met uw categorische variabele u bepalen hoeveel tijd de lift heeft besteed aan de bovenste, onderste en middelste verdiepingen.

    [![Visualiseer Lift 2 met hiërarchie en gegevens.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelfstudie hebt voltooid, ruimt u de resources op die u hebt gemaakt:

1. Zoek in het linkermenu in de [Azure-portal](https://portal.azure.com) **Alle bronnen**, zoek uw Azure Time Series Insights-brongroep.
1. Verwijder de volledige resourcegroep (en alle bronnen daarin) door Elke resource afzonderlijk **te** verwijderen of te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:  

* Een verbetering voor apparaatsimulatie maken en gebruiken.
* Een Time Series Insights Preview-omgeving op basis van betalen per gebruik maken.
* Verbind de Azure Time Series Insights Preview-omgeving met een iot-hub.
* Een voorbeeldoplossingsverbetering uitvoeren om gegevens te streamen naar de Azure Time Series Insights Preview-omgeving.
* Een eenvoudige analyse uitvoeren van de gegevens.
* Een type Time Series Model en een hiërarchie definiëren en deze koppelen aan uw exemplaren.

Nu u weet hoe u een eigen Azure Time Series Preview-omgeving maakt, is het tijd om meer te leren over de belangrijkste concepten in Azure Time Series Insights.

Meer informatie over de Azure Time Series Insights-opslagconfiguratie:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Preview-opslag en -opname](./time-series-insights-update-storage-ingress.md)

Meer informatie over Time Series-modellen:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Preview-gegevensmodellering](./time-series-insights-update-tsm.md)

Meer informatie over Uw omgeving verbinden met Power BI

> [!div class="nextstepaction"]
> [Visualiseer gegevens uit inzichten uit de Time Series in Power BI](./how-to-connect-power-bi.md)
