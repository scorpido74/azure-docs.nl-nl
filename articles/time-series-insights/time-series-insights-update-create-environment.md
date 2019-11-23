---
title: 'Zelf studie: een Azure Time Series Insights-voorbeeld omgeving instellen'
description: 'Zelf studie: meer informatie over het instellen van uw omgeving in Azure Time Series Insights preview.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49960ae91688b97a9d38a65e1b17fd277508b996
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114585"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Zelf studie: een Azure Time Series Insights-voorbeeld omgeving instellen

In deze zelfstudie wordt u door het creatieproces van een Azure Time Series Insights Preview-omgeving op basis van betalen per gebruik geleid.

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

   [pagina ![Azure IoT-oplossings versnellers](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Selecteer **nu proberen**op de volgende pagina.

   [pagina simulatie van ![apparaat](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Stel op de pagina oplossing voor het maken van een **apparaat simulatie** de volgende para meters in:

    | Parameter | Actie |
    | --- | --- |
    | **Implementatie naam** | Voer een unieke waarde in voor een nieuwe resource groep. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep. |
    | **Azure-abonnement** | Selecteer het abonnement waar u uw Time Series Insights-omgeving gaat maken. |
    | **Azure-locatie** | Selecteer de regio waar u uw Time Series Insights omgeving wilt opslaan. Houd er rekening mee dat de Device Simulator alleen wordt aangeboden in een beperkt aantal regio's. Als u de gewenste regio niet ziet, kunt u ervoor kiezen om alleen een locatie voor de zelf studie te selecteren en vervolgens een nieuwe omgeving voor de TSI te maken wanneer u klaar bent om over te stappen op uw volgende fase van op-b oarding.  |
    | **Implementatie opties** | Selecteer **nieuwe IOT hub inrichten**. |

    1. Selecteer **Maken**.
    [pagina simulatie van ![apparaat](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Na ongeveer 20 minuten is uw oplossings versneller klaar.

    [pagina simulatie van ![apparaat](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Een voor beeld-PAYG-omgeving maken

In deze sectie wordt beschreven hoe u een Azure Time Series Insights-voorbeeld omgeving maakt en deze verbindt met de IoT-hub die is gemaakt met de [Azure Portal](https://portal.azure.com/)van de IOT-oplossings versneller.

1. Meld u aan bij de Azure-portal met uw Azure-abonnementsaccount.

1. Selecteer **een resource maken** > **Internet of Things** > **Time Series Insights**.

   [![Selecteer Internet of Things en selecteer vervolgens Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Stel in het deel venster **Time Series Insights omgeving maken** op het tabblad **basis beginselen** de volgende para meters in:

    | Parameter | Actie |
    | --- | ---|
    | **Omgevingsnaam** | Voer een unieke naam in voor de voorbeeld omgeving van Azure Time Series Insights. |
    | **Abonnement** | Geef het abonnement op waar u de Azure Time Series Insights voorbeeld omgeving wilt maken. Een best practice is het gebruik van hetzelfde abonnement als de rest van de IoT-resources die door de Device Simulator zijn gemaakt. |
    | **Resourcegroep** | Selecteer een bestaande resource groep of maak een nieuwe resource groep voor de resource van de Azure Time Series Insights preview-omgeving. Een resourcegroep is een container voor Azure-resources. Een best practice is het gebruik van dezelfde resource groep als de andere IoT-resources die door de Device Simulator zijn gemaakt. |
    | **Locatie** | Selecteer een regio voor het Data Center voor uw Azure Time Series Insights-voorbeeld omgeving. Om extra latentie te voor komen, kunt u het beste uw Azure Time Series Insights-voorbeeld omgeving maken in dezelfde regio als uw IoT-hub die is gemaakt door de Device Simulator. |
    | **Laag** |  Selecteer **payg** (*betalen naar*gebruik). Dit is de SKU voor het Azure Time Series Insights Preview-product. |
    | **Eigenschaps-ID** | Voer een unieke waarde in voor uw time series-instantie. De waarde die u invoert in het vak **eigenschap-ID** kan later niet worden gewijzigd. Voer voor deze zelf studie **iothub-Connection-apparaat-id**in. Zie [Aanbevolen procedures voor het kiezen van een time series-id voor](./time-series-insights-update-how-to-id.md)meer informatie over de tijd reeks-id. |
    | **Naam van opslagaccount** | Voer een globaal unieke naam in voor een nieuw opslag account.|
    |**Warme Store inschakelen**|Selecteer **Ja** om warme Store in te scha kelen.|
    |**Bewaren van gegevens (in dagen)**|Kies de standaard optie van 7 dagen. |

    Selecteer **volgende: gebeurtenis bron**.

   [![deel venster voor het maken van een time series Insights omgeving](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![deel venster voor het maken van een time series Insights omgeving](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

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
   | **IoT Hub consumenten groep** | Selecteer **Nieuw**, voer een unieke naam in en selecteer vervolgens **Toevoegen**. De Consumer groep moet een unieke waarde zijn in Azure Time Series Insights preview. |
   | **Timestamp-eigenschap** | Deze waarde wordt gebruikt om de eigenschap **Time Stamp** te identificeren in de inkomende telemetriegegevens. Voor deze zelf studie laat u dit vak leeg. In deze simulator wordt de binnenkomende timestamp van IoT Hub gebruikt waarop Time Series Insights standaard is ingesteld. |

   Selecteer **Controleren + maken**.

   [![een gebeurtenis bron configureren](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Selecteer **Maken**.

    [pagina ![bekijken en maken, met knop maken](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    U kunt de status van uw implementatie bekijken:

    [![melding dat de implementatie is voltooid](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. U hebt standaard toegang tot uw Azure Time Series Insights-voorbeeld omgeving als u eigenaar bent van het Azure-abonnement. Controleer of u toegang hebt:

   1. Zoek naar de resource groep en selecteer vervolgens de zojuist gemaakte Azure Time Series Insights voorbeeld omgeving. 
      [Geselecteerde omgeving ![](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Selecteer op de pagina Azure Time Series Insights preview **Data Access policies**: [![Data Access policies (beleid voor gegevens toegang](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox) )

   1. Controleer of uw referenties worden weer gegeven:

      [referenties van ![lijst](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Als uw referenties niet worden weer gegeven, moet u uzelf toestemming geven voor toegang tot de omgeving door toe te voegen en te zoeken naar uw referenties. Raadpleeg [Gegevenstoegang verlenen](./time-series-insights-data-access.md) voor meer informatie over het instellen van machtigingen.

## <a name="stream-data"></a>Gegevens streamen

Nu u uw Time Series Insights-omgeving hebt geïmplementeerd, begint u gegevens te streamen voor analyse.

1. Ga terug naar de [pagina met accelerators voor Azure IOT-oplossingen](https://www.azureiotsolutions.com/Accelerators). Zoek uw oplossing in het dash board van de oplossings versneller en selecteer vervolgens **starten**:

    [de oplossing voor het simuleren van apparaten ![starten](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Selecteer **Ga naar uw oplossings versneller**.

    [de oplossing voor het simuleren van apparaten ![starten](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. U wordt omgeleid naar de pagina **Microsoft Azure IoT-apparaatsimulatie**. Selecteer in de rechter bovenhoek van de pagina **nieuwe simulatie**.

    [![pagina Azure IoT-simulatie](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Stel in het deel venster **simulatie instellingen** de volgende para meters in:

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer een unieke naam voor een simulator in. |
    | **Beschrijving** | Voer een definitie in. |
    | **Simulatieduur** | Stel de simulatieduur in op **Voor onbepaalde tijd uitvoeren**. |
    | **Apparaatmodel** | Klik op + **een apparaattype toevoegen** <br />**Naam**: Geef **Lift**op. <br />**Bedrag**: Voer **3**in. <br /> De overige standaard waarden behouden |
    | **Doel-IoT-hub** | Stel **Vooraf ingerichte IoT-hub gebruiken** in. |

    [![para meters die moeten worden ingesteld](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selecteer **simulatie starten**.

    In het dash board voor Device simulatie ziet u **actieve apparaten** en **Totaal aantal berichten**.

    [![Azure IoT-simulatie dashboard](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Gegevens analyseren

In deze sectie voert u een eenvoudige analyse uit op uw tijdreeksgegevens met de [verkenner van Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Ga naar de verkenner van Azure Time Series Insights Preview door de URL te selecteren op de resourcepagina in [Azure Portal](https://portal.azure.com/).

    [De URL van de Time Series Insights preview Explorer ![](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. In de Time Series Insights Explorer ziet u een balk die de bovenkant van het scherm beslaat. Dit is de beschikbaarheids kiezer. Zorg ervoor dat er ten minste twee 2 min. zijn geselecteerd en vouw, indien nodig, het tijds bestek uit door de selectie grepen naar links en rechts te slepen.

1. U ziet **nu de time series-exemplaren** aan de linkerkant.


    [Lijst met niet-bovenliggende instanties ![](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selecteer de eerste keer dat de reeks instantie is. Selecteer vervolgens **Druk tonen**.

    [![geselecteerde time series-instantie met menu opdracht om de gemiddelde druk weer te geven](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Er wordt een tijd reeks diagram weer gegeven. Wijzig het **interval** in **30s**.

    [diagram van ![tijd reeks](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Herhaal stap 3 met de andere twee reeksen instanties zodat u alle drie hebt, zoals wordt weer gegeven in dit diagram:

    [![grafiek voor alle tijd reeksen](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Selecteer de tijdsinterval kiezer in de rechter bovenhoek. Hier kunt u specifieke begin-en eind tijden voor de milliseconde selecteren of kiezen uit vooraf geconfigureerde opties zoals vorig uur. U kunt ook de standaardtijd zone wijzigen.

    [![het tijds bereik instellen op een uur](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Hieronder ziet u een scherm opname van het deel venster grafiek nadat de simulatie voor een uur is uitgevoerd:

    [deel venster voor grafiek ![](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Een model definiëren en toepassen

In dit gedeelte past u een model toe om uw gegevens te structureren. U definieert typen, hiërarchieën en exemplaren om het model te voltooien. Zie [Time Series model](./time-series-insights-update-tsm.md)voor meer informatie over gegevens modellering.

1. Selecteer het tabblad **Model** in de verkenner:

   [![tabblad model in de Explorer](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selecteer **toevoegen**op het tabblad **typen** .

   [De knop toevoegen ![voor typen](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Voer de volgende para meters in:

    | Parameter | Actie |
    | --- | ---|
    | **Naam** | **Lift** invoeren |
    | **Beschrijving** | Geef **een type definitie op voor lift** |

    [De knop toevoegen ![voor typen](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Selecteer vervolgens het tabblad **variabelen** . [![tabblad variabelen selecteren](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Selecteer **+ variabele toevoegen** en vul de volgende waarden in voor de eerste variabele van het type lift. U maakt drie variabelen in totaal.

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **Gemiddelde temperatuur** in. |
    | **Type** | **Numeriek** selecteren |
    | **Waarde** | Selecteren uit vooraf ingesteld: Selecteer **Tempe ratuur (dubbel)** . <br /> Opmerking: het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt.|
    | **Aggregatiebewerking** | Vouw **Geavanceerde opties**uit. <br /> Selecteer **GEMIDDELDE**. |

    [![selecties voor het definiëren van de Tempe ratuur](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Selecteer **Toepassen**.

    Selecteer **+ variabele opnieuw toevoegen** en stel de volgende waarden in:

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **AVG trillingen**in. |
    | **Type** | **Numeriek** selecteren |
    | **Waarde** | Selecteer een van de voor instellingen: **trillen (dubbel)** selecteren. <br /> Opmerking: het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt.|
    | **Aggregatiebewerking** | Vouw **Geavanceerde opties**uit. <br /> Selecteer **GEMIDDELDE**. |

    [![selecties voor het definiëren van trillingen](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Selecteer **Toepassen**.

    Selecteer **+ variabele opnieuw toevoegen** en stel de volgende waarden in voor de derde en laatste variabele:

    | Parameter | Actie |
    | --- | --- |
    | **Naam** | Voer **vloer**in. |
    | **Type** | **Categorische** selecteren |
    | **Waarde** | Selecteren uit vooraf ingesteld: Selecteer **Floor (dubbel)** . <br /> Opmerking: het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt.|
    | **Verschillende** | <span style="text-decoration: underline">Label</span>  - <span style="text-decoration: underline">waarden</span> <br /> Lager: 1, 2, 3, 4 <br /> Midden: 5, 6, 7, 8, 9 <br /> Upper: 10, 11, 12, 13, 14, 15 |
    | **Standaard categorie** | **Onbekend** invoeren |

    [![selecties voor het definiëren van trillingen](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Selecteer **Toepassen**. U ziet nu drie variabelen die zijn gemaakt:

    [![selecties voor het definiëren van trillingen](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Selecteer **Opslaan**. U ziet het **type** dat is gemaakt:

    [![selecties voor het definiëren van trillingen](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Selecteer het tabblad **hiërarchieën** . Selecteer **+ toevoegen**.

    [tabblad ![hiërarchieën met de knop toevoegen](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Stel in het deel venster **hiërarchie bewerken** de volgende para meters in:

   | Parameter | Actie |
   | --- | ---|
   | **Naam** | Voer **Locatiehiërarchie** in. |
   |**Residu**| **Land** invoeren als de naam van het eerste niveau <br> Selecteer **+ niveau toevoegen** <br> Voer **plaats** in voor het tweede niveau en selecteer **+ niveau toevoegen** <br> **Gebouw** invoeren als de naam van het derde en laatste niveau |

   Selecteer **Opslaan**.

    [![hiërarchie velden met de knop maken](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   U kunt de gemaakte hiërarchie bekijken:

    [Informatie over de hiërarchie ![](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Navigeer naar **instanties**. Klik onder **acties** uiterst rechts op het potlood pictogram om de eerste instantie te bewerken met de volgende waarden:

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Geef **lift 1** op|
    | **Beschrijving** | **Instantie voor lift 1** invoeren |

    [![de knop bewerken voor een exemplaar selecteren](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Navigeer naar **exemplaar velden** en voer het volgende in:

    | Parameter | Actie |
    | --- | --- |
    | **Hiërarchieën** | **Locatie hiërarchie** selecteren |
    | **Land** | Voer **USA** in |
    | **Plaats** | **Seattle** invoeren |
    | **Bouwen** | **Spatie naald** invoeren |

    [![de knop bewerken voor een exemplaar selecteren](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Selecteer **Opslaan**.

1. Herhaal stap 8 met de andere twee exemplaren met de volgende waarden:

    <span style="text-decoration: underline;">Voor Lift 2</span>:

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Geef **Lift 2** op|
    | **Beschrijving** | **Instantie voor Lift 2** invoeren |
    | **Hiërarchieën** | **Locatie hiërarchie** selecteren |
    | **Land** | Voer **USA** in |
    | **Plaats** | **Seattle** invoeren |
    | **Bouwen** | Voer het **Pacific Science Center** in |

    <span style="text-decoration: underline;">Voor lift 3</span>:

    | Parameter | Actie |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Geef **Lift 3** op|
    | **Beschrijving** | **Instantie voor lift 3** invoeren |
    | **Hiërarchieën** | **Locatie hiërarchie** selecteren |
    | **Land** | Voer **USA** in |
    | **Plaats** | Voer **New York** in |
    | **Bouwen** | **Empire-status gebouw** invoeren |

1. Ga terug naar het tabblad **analyseren** om het deel venster voor grafieken weer te geven. Vouw onder **locatie hiërarchie**alle hiërarchie niveaus uit om de time series-exemplaren weer te geven:

   [Het tabblad analyseren ![](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Selecteer onder **Pacific Science Center**de time series instance **Lift 2**en selecteer vervolgens **gemiddelde Tempe ratuur weer geven**.

    [![time series-instantie gemiddelde Tempe ratuur weer geven](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Voor hetzelfde exemplaar, **Lift 2**, selecteert u **basis weer geven**.

    [![time series-instantie Floor weer geven](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Met uw categorische-variabele kunt u bepalen hoeveel tijd de lift heeft besteed aan de bovenste, onderste en middelste vloer.

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelf studie hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **alle resources**, zoek de Azure time series Insights resource groep.
1. Verwijder de hele resource groep (en alle resources erin) door elke resource afzonderlijk **verwijderen** of verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:  

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
