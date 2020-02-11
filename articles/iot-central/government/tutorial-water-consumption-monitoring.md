---
title: 'Zelf studie: een water-bewakings-app maken met behulp van Azure IoT Central'
description: 'Zelf studie: meer informatie over het maken van een toepassing voor het controleren van water verbruik met behulp van Azure IoT Central-toepassings sjablonen.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7f00947504e5c6355379ce1e400fd2325016e05a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122060"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Zelf studie: een toepassing voor het controleren van water verbruik maken met Azure IoT Central

In deze zelf studie wordt uitgelegd hoe u een Azure IoT Central-bewakings toepassing voor water verbruik maakt met behulp van de toepassings sjabloon Azure IoT Central water verbruik-bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik de Azure IoT Central-bewakings sjabloon water verbruik om uw toepassing voor water verbruik-bewaking te maken.
> * Verken het dash board van de operator en pas het aan.
> * Sjablonen voor apparaten verkennen.
> * Verken gesimuleerde apparaten.
> * Regels verkennen en configureren.
> * Taken configureren.
> * Pas de huis stijl van uw toepassing aan met behulp van wit labelen.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Een app voor het bijhouden van water verbruik maken met Azure IoT Central

In deze sectie gebruikt u de sjabloon voor de bewaking van Azure IoT Central water verbruik om uw toepassing voor water gebruik in azure IoT Central te maken.

Voor het maken van een nieuwe Azure IoT Central-bewakings toepassing voor water verbruik:

1. Ga naar de website van de [Azure IOT Central-start pagina](https://aka.ms/iotcentral) .

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt voor toegang tot de app. Meld u anders aan met behulp van een Microsoft-account.

    ![Voer uw organisatieaccount in](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Selecteer **Build** in het linkerdeel venster en selecteer het tabblad **Government** . Op de pagina **Government** worden verschillende Government-toepassings sjablonen weer gegeven.

   ![Sjablonen voor Government-apps bouwen](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecteer de toepassings sjabloon **water verbruik bewaken** .
Deze sjabloon bevat een voor beeld van een sjabloon voor water verbruik, een gesimuleerd apparaat, een operator dashboard en vooraf geconfigureerde bewakings regels.

1. Selecteer **app maken** om het formulier **nieuwe toepassing** maken te openen met de volgende velden:
    * **Toepassings naam**: standaard gebruikt de toepassing *water verbruik bewaking* , gevolgd door een unieke id-reeks die door Azure IOT Central wordt gegenereerd. U kunt desgewenst een beschrijvende toepassings naam kiezen. U kunt de naam van de toepassing ook later wijzigen.
    * **URL**: Azure IOT Central genereert automatisch een URL op basis van de naam van de toepassing. U kunt ervoor kiezen om de URL naar uw eigen smaak bij te werken. U kunt de URL later ook wijzigen.
    * Als u een Azure-abonnement hebt, voert u uw **adres lijst**, **Azure-abonnement**en **locatie** -informatie in. Als u geen abonnement hebt, kunt u de optie **gratis proef versie van 7 dagen** selecteren en de vereiste contact gegevens volt ooien.

    Zie [Snelstartgids voor het maken van toepassingen](../core/quick-deploy-iot-central.md)voor meer informatie over directory's en abonnementen.

1. Selecteer **Maken** onderaan de pagina.

    ![Pagina nieuwe toepassing Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Pagina met facturerings gegevens voor Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

U hebt nu een water bewakings-app gemaakt met behulp van de Azure IoT Central water bewakings sjabloon.

De toepassing water consumptie bewaking wordt geleverd met vooraf geconfigureerd:

* Voorbeeld dashboards voor Opera tors.
* Voor beeld van vooraf gedefinieerde water stroom-en klep-Apparaatinstellingen sjablonen.
* Gesimuleerde water stroom-en slimme kleppen apparaten.
* Regels en taken.
* Voor beeld van een huis stijl met behulp van wit labelen.

Het is uw toepassing en u kunt deze op elk gewenst moment wijzigen. Nu gaan we de toepassing verkennen en enkele aanpassingen aanbrengen.

## <a name="explore-and-customize-the-operator-dashboard"></a>Het operator dashboard verkennen en aanpassen

Nadat u de toepassing hebt gemaakt, wordt het voor beeld- **dash board wereld wijd water verbruik** geopend.

   ![Bewakings dashboard water verbruik](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Als ontwerper kunt u weer gaven maken en aanpassen in het dash board voor Opera tors. We gaan het dash board verkennen voordat u het gaat aanpassen.

> [!NOTE]
> Alle gegevens die op het dash board worden weer gegeven, zijn gebaseerd op gesimuleerde apparaatgegevens, die we in de volgende sectie verkennen.
  
Het dash board bestaat uit verschillende soorten tegels:

* **Tegel**: de eerste tegel in het dash board is een afbeeldings tegel van het fictieve water-hulp programma Wide World water. U kunt de tegel aanpassen door uw eigen afbeelding in te voegen of te verwijderen.
* **Gemiddelde water stroom-KPI-tegel**: de KPI-tegel is geconfigureerd om te worden weer gegeven als een voor beeld *van het gemiddelde van de afgelopen 30 minuten*. U kunt de KPI-tegel aanpassen en instellen op een ander type en tijds bereik.
* **Tegels**van de opdracht: deze tegels zijn onder andere de **Sluit klep**, **Open klep**en **instellen klep positie** tegels. Als u de opdrachten selecteert, gaat u naar de opdracht pagina gesimuleerd apparaat. In azure IoT Central is een *opdracht* een type *apparaat* . We verkennen dit concept later in het gedeelte ' Device Temp late ' van deze zelf studie.
* **Kaart voor water distributiegebied**: de kaart maakt gebruik van Azure Maps, die u rechtstreeks in azure IOT Central kunt configureren. Op de kaart tegel wordt de locatie van het apparaat weer gegeven. Beweeg de muis aanwijzer over de kaart en probeer de besturings elementen op de kaart uit, zoals *Inzoomen*, *uitzoomen*of *uitvouwen*.

    ![Dashboard kaart voor bewaking van water verbruik](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Lijn diagram voor **Gemiddeld water diagram** en **omgevings voorwaarde**: u kunt een of meer apparaat-teleacties die zijn getekend als een lijn diagram over een gewenst tijds bereik visualiseren.
* De **gemiddelde heatmap-grafiek**van de klep: u kunt het visualisatie type van het apparaat selecteren van de telemetrie-gegevens van apparaten die u wilt weer geven over een tijds bereik met een kleur index.
* **Inhouds tegel voor waarschuwings drempels opnieuw instellen**: u kunt oproep-naar-actie-inhouds tegels opnemen en een koppeling insluiten naar een actie pagina. In dit geval gaat u met de drempel waarde voor opnieuw instellen naar de toepassings **taken**, waar u updates kunt uitvoeren op Apparaateigenschappen. We verkennen deze optie later in de sectie taken configureren van deze zelf studie.
* **Eigenschappen tegels**: in het dash board worden de voortgangs **gegevens**weer gegeven, de **waarschuwings drempels**voor de stroom en de tegels met **onderhouds informatie** .

### <a name="customize-the-dashboard"></a>Het dash board aanpassen

Als bouwer kunt u weer gaven aanpassen in het dash board voor Opera tors.

1. Selecteer **bewerken** om het **dash board voor het grote water verbruik**aan te passen. U kunt het dash board aanpassen door het menu **bewerken** te selecteren. Nadat het dash board zich in de **bewerkings** modus bevindt, kunt u nieuwe tegels toevoegen of kunt u deze configureren.

     ![Dash board bewerken](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Selecteer **+ Nieuw** om een nieuw dash board te maken en dit helemaal opnieuw te configureren. U kunt meerdere Dash boards hebben en u kunt scha kelen tussen uw Dash boards in het menu van het dash board.

## <a name="explore-the-device-template"></a>De sjabloon voor het apparaat verkennen

Een sjabloon voor een apparaat in azure IoT Central definieert de mogelijkheid van een apparaat, dat telemetrie, eigenschap of opdracht kan zijn. Als opbouw functie kunt u een of meer apparaatprofielen definiëren in azure IoT Central die de mogelijkheid vertegenwoordigen van de apparaten die u wilt verbinden.

De toepassing voor het bewaken van water verbruik bevat twee sjablonen voor referentie apparaten die een *stroom meter* en een *Slim klep* apparaat vertegenwoordigen.

De sjabloon voor het apparaat weer geven:

1. Selecteer **Apparaatbeheer** in het linkerdeel venster van uw toepassing in azure IOT Central. In de lijst met **Apparaatinstellingen** ziet u twee Device-sjablonen, een **slimme klep** -en **stroom meter**.

   ![Apparaatprofiel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Selecteer de sjabloon **stroom meter** apparaat en leer hoe u de mogelijkheden van het apparaat kunt verkennen.

     ![Stroom meter van Device-sjabloon](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>De sjabloon voor het apparaat aanpassen

De sjabloon voor het apparaat aanpassen:

1. Ga naar **aanpassen** in het menu met **device-sjablonen** .
1. Zoek het `Temperature` type telemetrie.
1. Werk de **weergave naam** van `Temperature` bij naar `Reported temperature`.
1. Werk de maat eenheid bij of stel de **minimum waarde** en de **maximum waarde**in.
1. Selecteer **Opslaan** om de wijzigingen op te slaan.

### <a name="add-a-cloud-property"></a>Een Cloud eigenschap toevoegen

1. Ga naar de eigenschappen van de **Cloud** in het menu met **device-sjablonen** .
1. Voeg een nieuwe Cloud eigenschap toe door **+ eigenschap Cloud toe**te voegen.
    In azure IoT Central kunt u een eigenschap toevoegen die relevant is voor het apparaat. Een Cloud eigenschap kan bijvoorbeeld een drempel waarde voor waarschuwingen zijn die specifiek is voor een installatie gebied, activa-informatie of andere onderhouds informatie.
1. Selecteer **Opslaan** om de wijzigingen op te slaan.

### <a name="views"></a>Weergaven

De sjabloon voor het monitor apparaat water verbruik bevat vooraf gedefinieerde weer gaven. Bekijk de weer gaven en u kunt updates maken. De weer gaven bepalen hoe Opera tors de gegevens van het apparaat zien, maar ook de eigenschappen van de Cloud.

  ![Sjabloon weergaven voor apparaten](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publiceren

Als u wijzigingen hebt aangebracht, moet u ervoor zorgen dat u de sjabloon voor het apparaat **publiceert** .

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken

Selecteer **+ Nieuw** om een nieuwe sjabloon voor het apparaat te maken en volg het aanmaak proces.
U kunt een volledig nieuwe sjabloon voor een apparaat maken of u kunt een sjabloon voor een apparaat uit de Azure-Apparaatbeheer kiezen.

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

In azure IoT Central kunt u gesimuleerde apparaten maken om de sjabloon en toepassing van uw apparaat te testen. De toepassing voor het controleren van water verbruik heeft twee gesimuleerde apparaten die zijn toegewezen aan de **stroom meter** en sjablonen voor **Smart-klep** apparaten.

### <a name="view-the-devices"></a>De apparaten weer geven

1. Selecteer **apparaten** > **alle apparaten** in het linkerdeel venster.

   ![Deel venster alle apparaten](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Selecteer **Smart klep 1**.

    ![Slimme klep 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Op het tabblad **opdrachten** ziet u de drie opdrachten voor apparaten (**Sluit klep**, **Open klep**en **klep positie instellen**) die zijn gedefinieerd in de sjabloon **Slim klep** apparaat.

1. Verken het tabblad **Apparaateigenschappen** en het tabblad **dash board** voor het apparaat.

> [!NOTE]
> Houd er rekening mee dat alle tabbladen worden geconfigureerd op basis van de weer gaven van de apparaatinstellingen.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

Voeg nieuwe apparaten toe door **+ Nieuw** te selecteren op het tabblad **apparaten** .

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In azure IoT Central kunt u regels maken voor het automatisch controleren van de telemetrie van apparaten en het activeren van acties wanneer aan een of meer voor waarden wordt voldaan. De acties omvatten mogelijk het verzenden van e-mail meldingen of het activeren van een actie voor het automatiseren van micro soft Powers of een webhook-actie voor het verzenden van gegevens naar andere services.

De toepassing voor het controleren van water verbruik die u hebt gemaakt heeft drie vooraf geconfigureerde regels.

### <a name="view-rules"></a>Regels weer geven

1. Selecteer **regels** in het linkerdeel venster.

   ![Deel venster regels](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Selecteer **hoge pH-waarschuwing**. Dit is een van de vooraf geconfigureerde regels in de toepassing.

     ![Hoge pH-waarschuwing](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    De `High flow alert` regel is geconfigureerd om te controleren op basis van de voor waarde `Acidity (pH)` `greater than` de `Max flow threshold`. De maximale stroom drempel is een Cloud eigenschap die is gedefinieerd in de sjabloon voor het **slimme klep** apparaat. De waarde van `Max flow threshold` wordt ingesteld per apparaatinstantie.

Nu gaan we een e-mail actie maken.

Een actie toevoegen aan de regel:

1. Selecteer **+ e-mail adres**.
1. Voer een **hoge pH-waarschuwing** in als de beschrijvende **weergave naam** voor de actie.
1. Voer het e-mail adres in dat is gekoppeld aan uw Azure IoT Central-account in **tot**.
1. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mail bericht.
1. Selecteer **gereed** om de actie te volt ooien.
1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren.

Binnen een paar minuten ontvangt u een e-mail bericht nadat aan de geconfigureerde voor waarde is voldaan.

> [!NOTE]
> De toepassing stuurt een e-mail telkens wanneer aan een voor waarde wordt voldaan. Selecteer **uitschakelen** om de regel te uitschakelen voor het ontvangen van e-mail van de automatische regel.
  
Een nieuwe regel maken:

* Selecteer **+ Nieuw** op het tabblad **regels** in het linkerdeel venster.

## <a name="configure-jobs"></a>Taken configureren

In azure IoT Central kunt u met taken apparaat-of Cloud eigenschaps updates op meerdere apparaten activeren. Naast eigenschappen kunt u ook taken gebruiken om opdrachten op meerdere apparaten te activeren. De werk stroom wordt door Azure IoT Central geautomatiseerd.

1. Selecteer **taken** in het linkerdeel venster.
1. Selecteer **+ Nieuw**en configureer een of meer taken.

## <a name="customize-your-application"></a>Uw toepassing aanpassen

Als ontwerper kunt u verschillende instellingen wijzigen om de gebruikers ervaring in uw toepassing aan te passen.

1. Selecteer **beheer** > **uw toepassing**aan te passen.
1. Selecteer de knop **wijzigen** om een afbeelding te kiezen die u als het **toepassings logo**wilt uploaden.
1. Selecteer de knop **wijzigen** om een **browser pictogram** afbeelding te kiezen die wordt weer gegeven op de browser tabbladen.
1. U kunt ook de standaard **browser kleuren** vervangen door HTML-hexadecimale kleur codes toe te voegen.

   ![Selecties voor het toepassings logo, browser pictogram en browser kleuren](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. U kunt ook installatie kopieën van toepassingen wijzigen door **beheer** > **Toepassings instellingen**te selecteren. Selecteer de knop **afbeelding selecteren** als u een afbeelding wilt kiezen die u als de installatie kopie van de toepassing wilt uploaden.
1. Ten slotte kunt u het **thema** ook wijzigen door het pictogram **instellingen** in de rechter bovenhoek van de toepassing te selecteren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u deze.

1. Selecteer **beheer** in het linkerdeel venster van uw Azure IOT Central-toepassing.
1. Selecteer **Toepassings instellingen**en selecteer vervolgens **verwijderen** onder aan de pagina.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [concepten van water verbruik-bewaking](./concepts-waterconsumptionmonitoring-architecture.md).
