---
title: 'Zelfstudie: Een Azure Time Series Insights Preview-omgeving instellen | Microsoft Docs'
description: Informatie over hoe u een omgeving instelt in Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: e680652d490d75701780e2e3618a43451bfe2819
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845102"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Zelfstudie: Een Time Series Insights Preview-omgeving instellen

In deze zelfstudie wordt u door het creatieproces van een Azure Time Series Insights Preview-omgeving op basis van betalen per gebruik geleid. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Time Series Insights Preview-omgeving maken.
> * De Azure Time Series Insights Preview-omgeving verbinden met een Event Hub in Azure Event Hubs.
> * Een voorbeeldoplossingsverbetering uitvoeren om gegevens te streamen naar de Azure Time Series Insights Preview-omgeving.
> * Voer een eenvoudige analyse van de gegevens uit.
> * Een type Time Series Model en een hiërarchie definiëren en deze koppelen aan uw exemplaren.

>[!TIP]
> [IOT-oplossings Accelerators](https://www.azureiotsolutions.com/Accelerators) bieden vooraf geconfigureerde oplossingen op ondernemings niveau die u kunt gebruiken om de ontwikkeling van aangepaste IOT-oplossingen te versnellen.

Meld u aan voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* Uw Azure-aanmeldings account moet ook lid zijn van de rol **eigenaar** van het abonnement. Zie [toegang beheren met op rollen gebaseerd toegangs beheer en de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="create-a-device-simulation"></a>Een apparaatsimulatie maken

In deze sectie maakt u drie gesimuleerde apparaten die gegevens verzenden naar een Azure IoT Hub-exemplaar.

1. Ga naar [de pagina Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators). Op de pagina worden enkele vooraf gedefinieerde voorbeelden weergegeven. Meld u aan met uw Azure-account. Selecteer vervolgens **Apparaatsimulatie**.

   [pagina met accelerators voor ![Azure IoT-oplossing](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Selecteer **Nu uitproberen**.

1. Stel op de pagina oplossing voor het maken van een **apparaat simulatie** de volgende para meters in:

    | Parameter | Action |
    | --- | --- |
    | **Implementatie naam** | Voer een unieke waarde in voor een nieuwe resource groep. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep. |
    | **Azure-abonnement** | Selecteer het abonnement dat u hebt gebruikt om uw Time Series Insights-omgeving te maken. |
    | **Azure-locatie** | Selecteer de regio die u hebt gebruikt om uw Time Series Insights omgeving te maken. |
    | **Implementatie opties** | Selecteer **nieuwe IOT hub inrichten**. |
 
    Selecteer **oplossing maken**. Het kan Maxi maal 20 minuten duren voordat de oplossing is geïmplementeerd.

    [pagina 1Create-oplossing voor het simuleren van @no__t-apparaten](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Een voor beeld-PAYG-omgeving maken

In deze sectie wordt beschreven hoe u een Azure Time Series Insights-voorbeeld omgeving maakt en deze verbindt met de IoT-hub die is gemaakt met de [Azure Portal](https://portal.azure.com/)van de IOT-oplossings versneller.

1. Meld u aan bij de Azure-portal met uw Azure-abonnementsaccount.

1. Selecteer **een resource maken** > **Internet of Things** > **Time Series Insights**.

   [![Select Internet of Things en selecteer vervolgens Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Stel in het deel venster **Time Series Insights omgeving maken** op het tabblad **basis beginselen** de volgende para meters in:

    | Parameter | Action |
    | --- | ---|
    | **Omgevingsnaam** | Voer een unieke naam in voor de voorbeeld omgeving van Azure Time Series Insights. |
    | **Abonnement** | Geef het abonnement op waar u de Azure Time Series Insights voorbeeld omgeving wilt maken. Een best practice is het gebruik van hetzelfde abonnement als de rest van de IoT-resources die door de Device Simulator zijn gemaakt. |
    | **Resourcegroep** | Selecteer een bestaande resource groep of maak een nieuwe resource groep voor de resource van de Azure Time Series Insights preview-omgeving. Een resourcegroep is een container voor Azure-resources. Een best practice is het gebruik van dezelfde resource groep als de andere IoT-resources die door de Device Simulator zijn gemaakt. |
    | **Location** | Selecteer een Data Center-regio voor uw Azure Time Series Insights-voorbeeld omgeving. Als u extra latentie wilt voor komen, kunt u het beste uw Azure Time Series Insights-voorbeeld omgeving maken in dezelfde regio als uw andere IoT-resources. |
    | **Laag** |  Selecteer **payg** (*betalen naar*gebruik). Dit is de SKU voor het Azure Time Series Insights Preview-product. |
    | **Eigenschaps-ID** | Voer een unieke waarde in voor uw time series-instantie. De waarde die u in het vak **eigenschap-ID** invoert, is onveranderbaar. U kunt deze later niet meer wijzigen. Voer voor deze zelf studie **iothub-Connection-apparaat-id**in. Zie [Aanbevolen procedures voor het kiezen van een time series-id voor](./time-series-insights-update-how-to-id.md)meer informatie over de tijd reeks-id. |
    | **Naam van opslagaccount** | Voer een globaal unieke naam in voor het nieuwe opslag account dat u wilt maken. |
   
   Selecteer **Volgende: Bron van gebeurtenis**.

   [![Pane voor het maken van een Time Series Insights omgeving](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Stel op het tabblad **gebeurtenis bron** de volgende para meters in:

   | Parameter | Action |
   | --- | --- |
   | **Een gebeurtenisbron maken** | Selecteer **Ja**.|
   | **Name** | Voer een unieke waarde in voor de naam van de gebeurtenis bron. |
   | **Brontype** | Selecteer **IOT hub**. |
   | **Een hub selecteren** | Kies **bestaande selecteren**. |
   | **Abonnement** | Selecteer het abonnement dat u voor de Device Simulator hebt gebruikt. |
   | **Naam van de IoT Hub** | Selecteer de naam van de IoT-hub die u hebt gemaakt voor de Device Simulator. |
   | **IoT Hub-toegangsbeleid** | Selecteer **iothubowner**. |
   | **IoT Hub consumenten groep** | Selecteer **Nieuw**, voer een unieke naam in en selecteer vervolgens **Toevoegen**. De Consumer groep moet een unieke waarde zijn in Azure Time Series Insights preview. |
   | **Timestamp-eigenschap** | Deze waarde wordt gebruikt om de eigenschap **Time Stamp** te identificeren in de inkomende telemetriegegevens. Voor deze zelf studie laat u dit vak leeg. In deze simulator wordt de binnenkomende timestamp van IoT Hub gebruikt waarop Time Series Insights standaard is ingesteld. |

   Selecteer **Controleren + maken**.

   [![Configure een gebeurtenis bron](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Controleer uw selecties op het tabblad **controleren en maken** en selecteer vervolgens **maken**.

    [![Review + pagina maken, met knop maken](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    U kunt de status van uw implementatie bekijken:

    [![Notification die implementatie is voltooid](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. U hebt toegang tot uw Azure Time Series Insights-voorbeeld omgeving als u eigenaar bent van de Tenant. Doe het volgende om ervoor te zorgen dat u toegang hebt:

   1. Zoek naar de resource groep en selecteer uw Azure Time Series Insights-voorbeeld omgeving:

      [![Selected omgeving](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Selecteer op de pagina Azure Time Series Insights preview **Data Access policies**:

      [![Data-toegangs beleid](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Controleer of uw referenties worden weer gegeven:

      [@no__t 1Listed-referenties](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Als uw referenties niet worden weer gegeven, moet u uzelf toestemming verlenen voor toegang tot de omgeving. Raadpleeg [Gegevenstoegang verlenen](./time-series-insights-data-access.md) voor meer informatie over het instellen van machtigingen.

## <a name="stream-data"></a>Gegevens streamen

Nu u uw Time Series Insights omgeving hebt geïmplementeerd, kunt u gegevens streamen voor analyse.

1. Ga terug naar de [pagina met accelerators voor Azure IOT-oplossingen](https://www.azureiotsolutions.com/Accelerators). Zoek uw oplossing in het dash board van de oplossings versnelling. Selecteer vervolgens **starten**:

    [![Launch de simulatie oplossing voor apparaten](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. U wordt omgeleid naar de pagina **Microsoft Azure IoT-apparaatsimulatie**. Selecteer in de rechter bovenhoek van de pagina **nieuwe simulatie**.

    [pagina 1Azure IoT-simulatie @no__t](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Stel in het deel venster **simulatie instellingen** de volgende para meters in:

    | Parameter | Action |
    | --- | --- |
    | **Name** | Voer een unieke naam voor een simulator in. |
    | **Beschrijving** | Voer een definitie in. |
    | **Simulatieduur** | Stel de simulatieduur in op **Voor onbepaalde tijd uitvoeren**. |
    | **Apparaatmodel** | **Naam**: Voer **Koelunit** in. <br />**Aantal**: Voer **3** in. |
    | **Doel-IoT-hub** | Stel **Vooraf ingerichte IoT-hub gebruiken** in. |

    [![Parameters om in te stellen](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selecteer **simulatie starten**.

    In het Device simulatie-dash board ziet u de informatie die wordt weer gegeven voor **actieve apparaten** en **berichten per seconde**.

    [![Azure IoT-simulatie dashboard](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Gegevens analyseren

In deze sectie voert u een eenvoudige analyse uit op uw tijdreeksgegevens met de [verkenner van Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Ga naar de verkenner van Azure Time Series Insights Preview door de URL te selecteren op de resourcepagina in [Azure Portal](https://portal.azure.com/).

    [![The Time Series Insights preview Explorer-URL](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Selecteer in de Verkenner het knoop punt **Time Series-exemplaren** om alle Azure time series Insights preview-exemplaren in de omgeving weer te geven.

    [![List van niet-bovenliggende instanties](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selecteer de eerste keer dat de reeks instantie is. Selecteer vervolgens **Druk tonen**.

    [![Selected time series-instantie met menu opdracht om de gemiddelde druk weer te geven](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Er wordt een tijd reeks diagram weer gegeven. Wijzig het **interval** in **15s**.

    [![Time](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Herhaal stap 3 met de andere twee time series-exemplaren. U kunt alle exemplaren van de tijd reeks weer geven, zoals wordt weer gegeven in dit diagram:

    [![Chart voor alle tijd reeksen](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Wijzig in de optie **tijds bestek** het tijds bereik om de tijd reeks trends in het afgelopen uur te bekijken:

    [@no__t 1Set het tijds bereik tot een uur](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Een model definiëren en toepassen

In dit gedeelte past u een model toe om uw gegevens te structureren. U definieert typen, hiërarchieën en exemplaren om het model te voltooien. Zie [Time Series model](./time-series-insights-update-tsm.md)voor meer informatie over gegevens modellering.

1. Selecteer het tabblad **Model** in de verkenner:

   [@no__t-tabblad 1Model in de Explorer](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selecteer **toevoegen** om een type toe te voegen:

   [![The knop toevoegen voor typen](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Vervolgens definieert u drie variabelen voor het type: *Druk*, *Tempe ratuur*en *vochtigheid*. Stel in het deel venster **een type toevoegen** de volgende para meters in:

    | Parameter | Action |
    | --- | ---|
    | **Name** | Voer **Koelunit** in. |
    | **Beschrijving** | Voer **Dit is een typedefinitie van Koelunit** in. |

   * Stel de volgende para meters in om de *Druk*onder **variabelen**te definiëren:

     | Parameter | Action |
     | --- | ---|
     | **Name** | Voer **Gemiddelde druk** in. |
     | **Waarde** | Selecteer **druk (dubbel)** . Het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt. |
     | **Aggregatiebewerking** | Selecteer **GEMIDDELDE**. |

      [![Selections voor het definiëren van druk](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Als u de volgende variabele wilt toevoegen, selecteert u **variabele toevoegen**.

   * *Tempe ratuur*definiëren:

     | Parameter | Action |
     | --- | ---|
     | **Name** | Voer **Gemiddelde temperatuur** in. |
     | **Waarde** | Selecteer **temperatuur (dubbel)** . Het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt. |
     | **Aggregatiebewerking** | Selecteer **GEMIDDELDE**.|

      [![Selections voor het definiëren van de Tempe ratuur](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Als u de volgende variabele wilt toevoegen, selecteert u **variabele toevoegen**.

   * *Vochtigheid*definiëren:

      | | |
      | --- | ---|
      | **Name** | **Maximum vochtigheid** invoeren |
      | **Waarde** | Selecteer **vochtigheid (dubbel)** . Het kan enkele minuten duren voordat de **waarde** automatisch wordt ingevuld nadat Azure time series Insights preview gebeurtenissen ontvangt. |
      | **Aggregatiebewerking** | Selecteer **MAXIMUM**.|

      [![Selections voor het definiëren van de Tempe ratuur](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Selecteer **Maken**.

    U ziet het type dat u hebt toegevoegd:

    [![Information over het toegevoegde type](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. De volgende stap is om een hiërarchie toe te voegen. Onder **hiërarchieën**selecteert u **toevoegen**:

    [@no__t-tabblad 1Hierarchies met knop toevoegen](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Stel in het deel venster **hiërarchie bewerken** de volgende para meters in:

   | Parameter | Action |
   | --- | ---|
   | **Name** | Voer **Locatiehiërarchie** in. |
   | **Niveau 1** | Voer **Land** in. |
   | **Niveau 2** | Voer **Plaats** in. |
   | **Niveau 3** | Voer **Gebouw** in. |

   Selecteer **Opslaan**.

    [![Hierarchy velden met de knop maken](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   U kunt de gemaakte hiërarchie bekijken:

    [![Information over de hiërarchie](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Selecteer **exemplaren**. Selecteer het eerste exemplaar en selecteer vervolgens **bewerken**:

    [![Selecting de knop bewerken voor een exemplaar](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Stel in het deel venster **instanties bewerken** de volgende para meters in:

    | Parameter | Action |
    | --- | --- |
    | **Type** | Selecteer **Koelunit**. |
    | **Beschrijving** | Voer **Exemplaar voor Koelunit-01.1** in. |
    | **Hiërarchieën** | Selecteer de **locatie hiërarchie**. |
    | **Land** | Voer **USA** in. |
    | **Plaats** | Voer **Seattle** in. |
    | **Bouwen** | Voer **Space Needle** in. |

    [![Instance velden met de knop Opslaan](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Selecteer **Opslaan**.

1. Herhaal de voor gaande stap voor de andere Sens oren. Werk de volgende waarden bij:

   * Voor Koelunit 01.2:

     | Parameter | Action |
     | --- | --- |
     | **Type** | Selecteer **Koelunit**. |
     | **Beschrijving** | Voer **Exemplaar voor Koelunit-01.2** in. |
     | **Hiërarchieën** | Selecteer de **locatie hiërarchie**. |
     | **Land** | Voer **USA** in. |
     | **Plaats** | Voer **Seattle** in. |
     | **Bouwen** | Voer **Pacific Science Center** in. |

   * Voor Koelunit 01.3:

     | Parameter | Action |
     | --- | --- |
     | **Type** | Selecteer **Koelunit**. |
     | **Beschrijving** | Voer **het exemplaar in voor Chiller-01,3**. |
     | **Hiërarchieën** | Selecteer de **locatie hiërarchie**. |
     | **Land** | Voer **USA** in. |
     | **Plaats** | Voer **New York** in. |
     | **Bouwen** | Voer **Empire State Building** in. |

1. Selecteer het tabblad **analyseren** en vernieuw vervolgens de pagina. Vouw onder **locatie hiërarchie**alle hiërarchie niveaus uit om de time series-exemplaren weer te geven:

   [![The-tabblad analyse](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Als u de time series-exemplaren in het afgelopen uur wilt verkennen, wijzigt u **snelle tijden** naar **vorig uur**:

    [![The-vak snelle tijden, waarbij vorig uur is geselecteerd](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Selecteer onder **Pacific Science Center**het time series-exemplaar en selecteer vervolgens **Maximum vochtigheid weer geven**.

    [![Selected time series-instantie en de selectie Maximum vochtigheid weer geven](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. De tijd reeks voor **maximale vochtigheid** met een interval van **1 minuut** wordt weer gegeven. Als u een bereik wilt filteren, selecteert u een regio. Als u gebeurtenissen in het tijds bestek wilt analyseren, klikt u met de rechter muisknop op de grafiek en selecteert u vervolgens **Inzoomen**:

   [![Selected-bereik met de opdracht zoomen in een snelmenu](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Selecteer een regio en klik met de rechter muisknop op de grafiek om de details van de gebeurtenis te bekijken:

   [![Detailed-lijst met gebeurtenissen](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelf studie hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **alle resources**, zoek de Azure time series Insights resource groep.
1. Verwijder de hele resource groep (en alle resources erin) door elke resource afzonderlijk **verwijderen** of verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:  

> [!div class="checklist"]
> * Een verbetering voor apparaatsimulatie maken en gebruiken.
> * Een Time Series Insights Preview-omgeving op basis van betalen per gebruik maken.
> * De Azure Time Series Insights Preview-omgeving verbinden met een Event Hub.
> * Een voorbeeldoplossingsverbetering uitvoeren om gegevens te streamen naar de Azure Time Series Insights Preview-omgeving.
> * Een eenvoudige analyse uitvoeren van de gegevens.
> * Een type Time Series Model en een hiërarchie definiëren en deze koppelen aan uw exemplaren.

Nu u weet hoe u een eigen Azure Time Series Preview-omgeving maakt, is het tijd om meer te leren over de belangrijkste concepten in Azure Time Series Insights.

Meer informatie over de Azure Time Series Insights-opslagconfiguratie:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Preview opslag en inkomend](./time-series-insights-update-storage-ingress.md)

Meer informatie over Time Series-modellen:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Preview-gegevensmodellering](./time-series-insights-update-tsm.md)
