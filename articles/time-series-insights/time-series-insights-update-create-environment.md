---
title: 'Zelf studie: een voorbeeld omgeving instellen-Azure Time Series Insights | Microsoft Docs'
description: 'Zelf studie: meer informatie over het instellen van een omgeving in Azure Time Series Insights preview.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6adb48b9d6c490b60302f93101506ec53679ae4f
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530234"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Zelf studie: een Azure Time Series Insights-voorbeeld omgeving instellen

Deze zelf studie leidt u door het proces van het maken van een Azure Time Series Insights preview-omgeving ( *betalen per gebruik* ) (PAYG).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Time Series Insights Preview-omgeving maken.
> * Verbind de Azure Time Series Insights preview-omgeving met een IoT Hub.
> * Een voorbeeldoplossingsverbetering uitvoeren om gegevens te streamen naar de Azure Time Series Insights Preview-omgeving.
> * Voer een eenvoudige analyse van de gegevens uit.
> * Een type Time Series Model en een hiërarchie definiëren en deze koppelen aan uw exemplaren.
> * Gebruik de Power BI-connector om gegevens te visualiseren in Power BI.

>[!TIP]
> [IOT-oplossings Accelerators](https://www.azureiotsolutions.com/Accelerators) bieden vooraf geconfigureerde oplossingen op ondernemings niveau die u kunt gebruiken om de ontwikkeling van aangepaste IOT-oplossingen te versnellen.

Meld u aan voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* U moet mini maal de rol **Inzender** hebben voor het Azure-abonnement. Zie [toegang beheren met op rollen gebaseerd toegangs beheer en de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="create-a-device-simulation"></a>Een apparaatsimulatie maken

In deze sectie maakt u drie gesimuleerde apparaten die gegevens verzenden naar een Azure IoT Hub-exemplaar.

1. Ga naar [de pagina Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators). Op de pagina worden enkele vooraf gedefinieerde voorbeelden weergegeven. Meld u aan met uw Azure-account. Selecteer vervolgens **Apparaatsimulatie**.

   [![pagina met accelerators voor Azure IoT-oplossing.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Selecteer **nu proberen**op de volgende pagina. Voer vervolgens de vereiste para meters in op de pagina oplossing voor het maken van een **apparaat simulatie** .

   Parameter|Beschrijving
   ---|---
   **Implementatie naam** | Deze unieke waarde wordt gebruikt om een nieuwe resource groep te maken. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep.
   **Azure-abonnement** | Geef hetzelfde abonnement op dat is gebruikt voor het maken van uw Time Series Insights-omgeving in de vorige sectie.
   **Implementatie opties** | Selecteer **nieuwe IOT hub inrichten** als u een nieuwe IOT-hub wilt maken die specifiek is voor deze zelf studie.
   **Azure-locatie** | Geef dezelfde regio op die is gebruikt voor het maken van uw Time Series Insights-omgeving in de vorige sectie.

   Wanneer u klaar bent, selecteert u **maken** om de Azure-resources van de oplossing in te richten. Het kan Maxi maal 20 minuten duren voordat dit proces is voltooid.

   [![de simulatie oplossing voor apparaten in te richten.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Nadat het inrichten is voltooid, worden er twee updates weer gegeven die aangeven dat de implementatie status is verplaatst van **inrichting** naar **gereed**. 

   >[!IMPORTANT]
   > Voer uw oplossings versneller nog niet in. Laat deze webpagina geopend omdat u deze later weer gaat gebruiken.

   [inrichting van de inrichtings oplossing voor ![Device simulatie is voltooid.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Inspecteer nu de zojuist gemaakte resources in het Azure Portal. Op de pagina **resource groepen** ziet u dat er een nieuwe resource groep is gemaakt met behulp van de **oplossings naam** die in de laatste stap is opgenomen. Noteer de resources die zijn gemaakt voor de simulatie van het apparaat.

   [![simulatie bronnen voor apparaten.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Een voor beeld-PAYG-omgeving maken

In deze sectie wordt beschreven hoe u een Azure Time Series Insights-voorbeeld omgeving maakt en deze verbindt met de IoT-hub die is gemaakt met de [Azure Portal](https://portal.azure.com/)van de IOT-oplossings versneller.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw account voor uw Azure-abonnement. 
1. Selecteer linksboven **+ Een resource maken**. 
1. Selecteer de categorie **Internet of Things** en vervolgens **Time Series Insights**. 

   [![de resource van de Time Series Insights omgeving te selecteren.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. Stel in het deel venster **Time Series Insights omgeving maken** op het tabblad **basis beginselen** de volgende para meters in:

    | Parameter | Actie |
    | --- | ---|
    | **Omgevingsnaam** | Voer een unieke naam in voor de voorbeeld omgeving van Azure Time Series Insights. |
    | **Abonnement** | Geef het abonnement op waar u de Azure Time Series Insights voorbeeld omgeving wilt maken. Een best practice is het gebruik van hetzelfde abonnement als de rest van de IoT-resources die door de Device Simulator zijn gemaakt. |
    | **Resourcegroep** | Selecteer een bestaande resource groep of maak een nieuwe resource groep voor de resource van de Azure Time Series Insights preview-omgeving. Een resourcegroep is een container voor Azure-resources. Een best practice is het gebruik van dezelfde resource groep als de andere IoT-resources die door de Device Simulator zijn gemaakt. |
    | **Locatie** | Selecteer een regio voor het Data Center voor uw Azure Time Series Insights-voorbeeld omgeving. Om extra latentie te voor komen, kunt u het beste uw Azure Time Series Insights-voorbeeld omgeving maken in dezelfde regio als uw IoT-hub die is gemaakt door de Device Simulator. |
    | **Laag** |  Selecteer **payg** (*betalen naar*gebruik). Dit is de SKU voor het Azure Time Series Insights Preview-product. |
    | **Eigenschapsnaam** | Voer een unieke waarde in voor uw time series-instantie. De waarde die u invoert in het vak **eigenschap-ID** kan later niet worden gewijzigd. Voer voor deze zelf studie ***iothub-Connection-apparaat-id***in. Zie [Aanbevolen procedures voor het kiezen van een time series-id voor](./time-series-insights-update-how-to-id.md)meer informatie over de tijd reeks-id. |
    | **Naam van opslagaccount** | Voer een globaal unieke naam in voor een nieuw opslag account.|
    |**Warme Store inschakelen**|Selecteer **Ja** om warme Store in te scha kelen. U kunt later terugkeren en deze instelling inschakelen. |
    |**Bewaren van gegevens (in dagen)**|Kies de standaard optie van 7 dagen. |

    Selecteer **volgende: gebeurtenis bron**.

   [![nieuwe Time Series Insights omgevings configuratie.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [de tijd reeks-ID voor de omgeving ![configureren.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Stel op het tabblad **gebeurtenis bron** de volgende para meters in:

   | Parameter | Actie |
   | --- | --- |
   | **Een gebeurtenisbron maken** | Selecteer **Ja**.|
   | **Naam** | Voer een unieke waarde in voor de naam van de gebeurtenis bron. |
   | **Brontype** | Selecteer **IOT hub**. |
   | **Een hub selecteren** | Kies **bestaande selecteren**. |
   | **Abonnement** | Selecteer het abonnement dat u voor de Device Simulator hebt gebruikt. |
   | **Naam van de IoT Hub** | Selecteer de naam van de IoT-hub die u hebt gemaakt voor de Device Simulator. |
   | **IoT Hub-toegangsbeleid** | Selecteer **iothubowner**. |
   | **IoT Hub consumenten groep** | Selecteer **Nieuw**, voer een unieke naam in en selecteer **+ toevoegen**. De Consumer groep moet een unieke waarde zijn in Azure Time Series Insights preview. |
   | **Timestamp-eigenschap** | Deze waarde wordt gebruikt om de eigenschap **Time Stamp** te identificeren in de inkomende telemetriegegevens. Voor deze zelf studie laat u dit vak leeg. In deze simulator wordt de binnenkomende timestamp van IoT Hub gebruikt waarop Time Series Insights standaard is ingesteld. |

   Selecteer **Controleren + maken**.

   [![de gemaakte IoT-hub als een gebeurtenis bron configureren.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selecteer **Maken**.

    [![bekijken + pagina maken met de knop maken.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    U kunt de status van uw implementatie bekijken:

    [![melding dat de implementatie is voltooid.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. U hebt standaard toegang tot uw Azure Time Series Insights-voorbeeld omgeving als u eigenaar bent van het Azure-abonnement. Controleer of u toegang hebt:

   1. Zoek naar de resource groep en selecteer vervolgens de zojuist gemaakte Azure Time Series Insights voorbeeld omgeving. 

      [![uw omgeving selecteren en weer geven.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Selecteer op de pagina Azure Time Series Insights preview **Data Access policies**:

      [![het verifiëren van het beleid voor gegevens toegang.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Controleer of uw referenties worden weer gegeven:

      Als uw referenties niet worden weer gegeven, moet u uzelf toestemming geven voor toegang tot de omgeving door toe te voegen en te zoeken naar uw referenties. Raadpleeg [Gegevenstoegang verlenen](./time-series-insights-data-access.md) voor meer informatie over het instellen van machtigingen.

## <a name="stream-data"></a>Gegevens streamen

Nu u uw Time Series Insights-omgeving hebt geïmplementeerd, begint u gegevens te streamen voor analyse.

1. Ga terug naar het dashboard [Oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#dashboard). Meld u indien nodig opnieuw aan met behulp van hetzelfde Azure-account dat u in deze zelf studie hebt gebruikt. Selecteer uw ' oplossing voor apparaten ' en **Ga vervolgens naar uw oplossings versneller** om uw geïmplementeerde oplossing te starten.

   [dash board voor ![oplossings versnellers.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. De web-app voor Device simulatie begint met de vraag of u de webtoepassing wilt verlenen voor het **Aanmelden en de machtiging voor uw profiel te lezen** . Met deze machtiging kan de toepassing de gebruikers profiel gegevens ophalen die nodig zijn om de werking van de toepassing te ondersteunen.

   [toestemming voor webtoepassingen voor simulatie van ![apparaat.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selecteer **+ nieuwe simulatie**. Nadat de pagina **simulatie installatie** is geladen, voert u de vereiste para meters in.

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer een unieke naam voor een simulator in. |
    | **Beschrijving** | Voer een definitie in. |
    | **Simulatieduur** | Stel de simulatieduur in op **Voor onbepaalde tijd uitvoeren**. |
    | **Apparaatmodel** | Klik op + **een apparaattype toevoegen** <br />**Naam**: Geef **Lift**op. <br />**Bedrag**: Voer **3**in. <br /> De overige standaard waarden behouden |
    | **Doel-IoT-hub** | Stel **Vooraf ingerichte IoT-hub gebruiken** in. |

    [![para meters configureren en starten.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Selecteer **simulatie starten**.

    In het dash board voor Device simulatie ziet u **actieve apparaten** en **Totaal aantal berichten**.

    [![Azure IoT-simulatie dashboard.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analyseer data

In deze sectie voert u een eenvoudige analyse uit op uw tijdreeksgegevens met de [verkenner van Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Ga naar de verkenner van Azure Time Series Insights Preview door de URL te selecteren op de resourcepagina in [Azure Portal](https://portal.azure.com/).

    [![de Time Series Insights preview Explorer-URL.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. In de Time Series Insights Explorer ziet u een balk die de bovenkant van het scherm beslaat. Dit is de beschikbaarheids kiezer. Zorg ervoor dat er ten minste twee m is geselecteerd en vouw, indien nodig, het tijds bestek uit door de selectie grepen naar links en rechts te slepen.

1. U ziet **nu de time series-exemplaren** aan de linkerkant.

    [Lijst met niet-bovenliggende instanties ![.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selecteer de eerste-time series-instantie. Selecteer vervolgens **weer geven temperatuur**.

    [![geselecteerde time series-instantie met menu opdracht om de gemiddelde Tempe ratuur weer te geven.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Er wordt een tijd reeks diagram weer gegeven. Wijzig het **interval** in **30s**.

1. Herhaal de vorige stap met de andere twee exemplaren van de tijd reeks, zodat u alle drie hebt, zoals wordt weer gegeven in dit diagram:

    [![grafiek voor alle tijd reeksen.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Selecteer de tijdsinterval kiezer in de rechter bovenhoek. Hier kunt u specifieke begin-en eind tijden voor de milliseconde selecteren of kiezen uit vooraf geconfigureerde opties, zoals de **laatste 30 minuten**. U kunt ook de standaardtijd zone wijzigen.

    [![het tijds bereik in te stellen op de laatste 30 minuten.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    De oplossings versneller in de **laatste 30 minuten** wordt nu weer gegeven in de time series Insights Explorer.

## <a name="define-and-apply-a-model"></a>Een model definiëren en toepassen

In dit gedeelte past u een model toe om uw gegevens te structureren. U definieert typen, hiërarchieën en exemplaren om het model te voltooien. Zie [Time Series model](./time-series-insights-update-tsm.md)voor meer informatie over gegevens modellering.

1. Selecteer het tabblad **Model** in de verkenner:

   [![het tabblad model in de Explorer te bekijken.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Op het tabblad **typen** selecteert u **+ toevoegen**.

1. Voer de volgende para meters in:

    | Parameter | Actie |
    | --- | ---|
    | **Naam** | **Lift** invoeren |
    | **Beschrijving** | Geef **een type definitie op voor lift** |

1. Selecteer vervolgens het tabblad **variabelen** . 

   Selecteer **+ variabele toevoegen** en vul de volgende waarden in voor de eerste variabele van het type lift. U maakt drie variabelen in totaal.

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **Gemiddelde temperatuur** in. |
    | **Type** | **Numeriek** selecteren |
    | **Waarde** | Selecteren uit vooraf ingesteld: Selecteer **Tempe ratuur (dubbel)** . <br /> Opmerking: het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt.|
    | **Aggregatiebewerking** | Vouw **Geavanceerde opties**uit. <br /> Selecteer **GEMIDDELDE**. |

    Selecteer **Toepassen**. Daarna moet u vervolgens de **variabele opnieuw toevoegen** en de volgende waarden instellen:

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **AVG trillingen**in. |
    | **Type** | **Numeriek** selecteren |
    | **Waarde** | Selecteer een van de voor instellingen: **trillen (dubbel)** selecteren. <br /> Opmerking: het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt.|
    | **Aggregatiebewerking** | Vouw **Geavanceerde opties**uit. <br /> Selecteer **GEMIDDELDE**. |

    Selecteer **Toepassen**. Daarna moet u vervolgens opnieuw een **variabele toevoegen** en de volgende waarden instellen voor de derde en laatste variabele:

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **vloer**in. |
    | **Type** | **Categorische** selecteren |
    | **Waarde** | Selecteren uit vooraf ingesteld: Selecteer **Floor (dubbel)** . <br /> Opmerking: het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt.|
    | **Verschillende** | <span style="text-decoration: underline">Label</span>  - <span style="text-decoration: underline">waarden</span> <br /> Lager: 1, 2, 3, 4 <br /> Midden: 5, 6, 7, 8, 9 <br /> Upper: 10, 11, 12, 13, 14, 15 |
    | **Standaard categorie** | **Onbekend** invoeren |

    [![type variabelen toevoegen.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Selecteer **Toepassen**.

1. Selecteer **Opslaan**. U ziet nu drie variabelen die zijn gemaakt.

    [Zie ![na het toevoegen van het type in de model weergave.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selecteer het tabblad **hiërarchieën** . Selecteer vervolgens **+ toevoegen**.
   
   Stel in het deel venster **hiërarchie bewerken** de volgende para meters in:

   | Parameter | Actie |
   | --- | ---|
   | **Naam** | Voer **Locatiehiërarchie** in. |
   |**Residu**| **Land** invoeren als de naam van het eerste niveau <br> Selecteer **+ niveau toevoegen** <br> Voer **plaats** in voor het tweede niveau en selecteer **+ niveau toevoegen** <br> **Gebouw** invoeren als de naam van het derde en laatste niveau |

   Selecteer **Opslaan**.

   [![uw nieuwe hiërarchie weer geven in de model weergave.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navigeer naar **instanties**. Klik onder **acties** uiterst rechts op het potlood pictogram om de eerste instantie te bewerken met de volgende waarden:

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Geef **lift 1** op|
    | **Beschrijving** | **Instantie voor lift 1** invoeren |

    Navigeer naar **exemplaar velden** en voer de volgende waarden in:

    | Parameter | Actie |
    | --- | --- |
    | **Hiërarchieën** | **Locatie hiërarchie** selecteren |
    | **Land** | Voer **USA** in |
    | **Plaats** | **Seattle** invoeren |
    | **Bouwen** | **Spatie naald** invoeren |

    Selecteer **Opslaan**.

1. Herhaal de vorige stap met de andere twee exemplaren terwijl u de volgende waarden gebruikt:

    **Voor Lift 2:**

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Geef **Lift 2** op|
    | **Beschrijving** | **Instantie voor Lift 2** invoeren |
    | **Hiërarchieën** | **Locatie hiërarchie** selecteren |
    | **Land** | Voer **USA** in |
    | **Plaats** | **Seattle** invoeren |
    | **Bouwen** | Voer het **Pacific Science Center** in |

    **Voor lift 3:**

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Geef **Lift 3** op|
    | **Beschrijving** | **Instantie voor lift 3** invoeren |
    | **Hiërarchieën** | **Locatie hiërarchie** selecteren |
    | **Land** | Voer **USA** in |
    | **Plaats** | Voer **New York** in |
    | **Bouwen** | **Empire-status gebouw** invoeren |

    [![de bijgewerkte exemplaren weer te geven.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Ga terug naar het tabblad **analyseren** om het deel venster voor grafieken weer te geven. Vouw onder **locatie hiërarchie**alle hiërarchie niveaus uit om de time series-exemplaren weer te geven:

    [![alle hiërarchieën weer geven in de grafiek weergave.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Selecteer onder **Pacific Science Center**de time series instance **Lift 2**en selecteer vervolgens **gemiddelde Tempe ratuur weer geven**.

1. Voor hetzelfde exemplaar, **Lift 2**, selecteert u **basis weer geven**.

    Met uw categorische-variabele kunt u bepalen hoeveel tijd de lift heeft besteed aan de bovenste, onderste en middelste vloer.

    [![Lift 2 visualiseren met hiërarchie en gegevens.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelf studie hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **alle resources**, zoek de Azure time series Insights resource groep.
1. Verwijder de hele resource groep (en alle resources erin) door elke resource afzonderlijk **verwijderen** of verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:  

* Een verbetering voor apparaatsimulatie maken en gebruiken.
* Een Time Series Insights Preview-omgeving op basis van betalen per gebruik maken.
* Verbind de Azure Time Series Insights preview-omgeving met een IOT-hub.
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
