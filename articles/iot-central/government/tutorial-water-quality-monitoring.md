---
title: 'Zelf studie: een app voor het controleren van water kwaliteit maken met Azure IoT Central'
description: 'Zelf studie: meer informatie over het maken van een toepassing voor het controleren van water kwaliteit met behulp van Azure IoT Central-toepassings sjablonen.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d738868e0e4ca7599f4aaf8d6e09d22f26a8db92
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016371"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Zelf studie: een toepassing voor het controleren van water kwaliteit maken in azure IoT Central



In deze zelf studie wordt u begeleid bij het maken van een toepassing voor het controleren van water kwaliteit in azure IoT Central. U maakt de toepassing vanuit de sjabloon voor het controleren van de Azure IoT Central **water kwaliteit** -toepassing.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik de **bewakings sjabloon water kwaliteit** om een toepassing voor het controleren van water kwaliteit te maken.
> * Een operator dashboard verkennen en aanpassen.
> * Een sjabloon voor bewaking van water kwaliteit verkennen.
> * Verken gesimuleerde apparaten.
> * Regels verkennen en configureren.
> * Taken configureren.
> * De huis stijl van een toepassing aanpassen met behulp van wit labelen.

## <a name="prerequisites"></a>Vereisten

We raden u aan een Azure-abonnement te hebben om deze zelf studie te volt ooien. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Een toepassing voor het controleren van water kwaliteit maken in azure IoT Central

In deze sectie gebruikt u de bewakings sjabloon Azure IoT Central **water kwaliteit** om een toepassing voor water kwaliteit te maken.

1. Ga naar de [Start pagina van Azure IOT Central](https://aka.ms/iotcentral).

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt voor toegang tot de app. Als dat niet het geval is, meldt u zich aan met een Microsoft-account:

    ![Aanmelden bij uw organisatie account](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Selecteer **Build** in het meest linkse deel venster van Azure IOT Central en selecteer het tabblad **Government** . Het deel venster Government bevat verschillende sjablonen voor overheids toepassingen.

    ![Overheids toepassings sjablonen](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selecteer de toepassings sjabloon **water kwaliteit bewaken** . Deze toepassings sjabloon bevat een sjabloon voor water kwaliteit, gesimuleerde apparaten, een operator dashboard en vooraf geconfigureerde bewakings regels.

1. Selecteer **app maken**. Het deel venster **nieuwe toepassing** wordt geopend en toont de volgende elementen:

    * **Toepassings naam**: de naam van de toepassing is standaard ingesteld op **water kwaliteit** , gevolgd door een unieke id-reeks die door Azure IOT Central wordt gegenereerd. Als u wilt, kunt u een weergave naam invoeren of de naam van de toepassing later wijzigen.
    * **URL**: u kunt elke gewenste URL invoeren of de URL-waarde later wijzigen.
    * Als u een Azure-abonnement hebt, voert u waarden in voor de **Directory**, het **Azure-abonnement**en de **regio**. Als u geen abonnement hebt, kunt u een **gratis proef versie van 7 dagen** inschakelen en de vereiste contact gegevens volt ooien.

    Zie de Snelstartgids [een toepassing maken](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor meer informatie over directory's en abonnementen.

1. Selecteer de knop **maken** in het linkerbenedenhoek van de pagina.

    ![De Azure IoT Central-pagina Nieuw-toepassing](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![De Azure IoT Central-facturerings gegevens voor nieuwe toepassingen](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

U hebt nu een toepassing voor water kwaliteit gemaakt met behulp van de sjabloon voor het controleren van de IoT Central **water kwaliteit** van Azure.

De nieuwe toepassing wordt geleverd met de volgende vooraf geconfigureerde onderdelen:

* Operator dashboards
* Bewakings sjablonen voor water kwaliteit
* Bewakings apparaten voor gesimuleerde water kwaliteit
* Regels en taken
* Huis stijl waarin gebruik wordt gemaakt van witte labels

U kunt uw toepassing op elk gewenst moment wijzigen.

Daarna kunt u de toepassing verkennen en enkele aanpassingen aanbrengen.

## <a name="explore-and-customize-the-operator-dashboard"></a>Het operator dashboard verkennen en aanpassen

Nadat u de toepassing hebt gemaakt, wordt het deel venster **dash board voor hoogwaardige water kwaliteit** geopend.

   ![Het dash board controle van water kwaliteit](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Als ontwerper kunt u weer gaven op het dash board maken en aanpassen voor gebruik door Opera tors. Maar voordat u wilt aanpassen, moet u eerst het dash board verkennen.

Alle gegevens die in het dash board worden weer gegeven, zijn gebaseerd op gesimuleerde apparaatgegevens, die in de volgende sectie worden besproken.

Het dash board bevat de volgende soorten tegels:

* **Tegel**: de eerste tegel in de linkerbovenhoek van het dash board is een afbeelding waarin het fictieve hulp programma met de naam Wide World wordt weer gegeven. U kunt de tegel aanpassen om uw eigen installatie kopie te gebruiken, of u kunt de tegel verwijderen.

* **Gemiddelde pH KPI-tegels**: KPI-tegels zoals **gemiddeld pH in de afgelopen 30 minuten** bevinden zich bovenaan het deel venster dash board. U kunt KPI-tegels aanpassen en deze instellen op een ander type en tijds bereik.

* **Zone kaart water bewaking**: Azure IoT Central gebruikt Azure Maps, die u rechtstreeks in uw toepassing kunt instellen om de locatie van het apparaat weer te geven. U kunt ook locatie gegevens van uw toepassing toewijzen aan uw apparaat en vervolgens Azure Maps gebruiken om de gegevens op een kaart weer te geven. Beweeg de muis aanwijzer over de kaart en probeer de besturings elementen.

* **Gemiddeld heatmap voor de pH-verdeling**: u kunt verschillende visualisatie diagrammen selecteren om de telemetrie van apparaten weer te geven op de manier die het meest geschikt is voor uw toepassing.

* **Lijn diagram van kritieke kwaliteits indicatoren**: u kunt de telemetrie van apparaten visualiseren als een lijn diagram gedurende een tijds bereik.  

* **Staaf diagram voor de concentratie van chemische agents**: u kunt de telemetrie van apparaten visualiseren in een staaf diagram.

* **Actie knop**: het dash board bevat een tegel voor acties die een operator rechtstreeks vanuit het bewakings dashboard kan initiëren. Het opnieuw instellen van de eigenschappen van een apparaat is een voor beeld van dergelijke acties.

* **Tegels van eigenschappen lijst**: het dash board heeft meerdere eigenschappen tegels die drempel gegevens, informatie over Apparaatstatus en onderhouds informatie vertegenwoordigen.

### <a name="customize-the-dashboard"></a>Het dash board aanpassen

Als bouwer kunt u weer gaven op het dash board aanpassen voor gebruik door Opera tors.

1. Selecteer **bewerken** om het deel venster **dash board voor grote water kwaliteit** aan te passen. U kunt het dash board aanpassen door opdrachten te selecteren in het menu **bewerken** . Wanneer het dash board zich in de bewerkings modus bevindt, kunt u nieuwe tegels toevoegen of kunt u de bestaande bestanden configureren.

    ![Uw dash board bewerken](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Selecteer **+ Nieuw** om een nieuw dash board te maken dat u kunt configureren. U kunt meerdere Dash boards hebben en hiertussen scha kelen vanuit het menu dash board.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Een sjabloon voor het bewakings apparaat voor water kwaliteit verkennen

Met een apparaatprofiel in azure IoT Central worden de mogelijkheden van een apparaat gedefinieerd. Beschik bare mogelijkheden zijn telemetrie, eigenschappen en opdrachten. Als ontwerper kunt u Device-sjablonen definiëren in azure IoT Central die de mogelijkheden van de verbonden apparaten vertegenwoordigen. U kunt ook gesimuleerde apparaten maken om de sjabloon en toepassing van uw apparaat te testen.

De toepassing voor het controleren van water kwaliteit die u hebt gemaakt, wordt geleverd met een sjabloon voor bewaking van water kwaliteit.

De sjabloon voor het apparaat weer geven:

1. Selecteer **Apparaatbeheer** in het linkerdeel venster van uw toepassing in azure IOT Central.
1. Selecteer in de lijst met Apparaatinstellingen **water kwaliteit monitor**. De Device-sjabloon wordt geopend.

    ![De sjabloon voor het apparaat](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>De sjabloon voor het apparaat aanpassen

De volgende instellingen voor de apparaatinstellingen aanpassen:

1. Selecteer in het menu Apparaatbeheer de optie **aanpassen**.
1. Ga naar het type **temperatuur** telemetrie.
1. Wijzig de **weergave naam** waarde in **gemelde Tempe ratuur**.
1. Wijzig de maat eenheid of stel de **minimum waarde** en de **maximum waarde**in.
1. Selecteer **Opslaan**.

#### <a name="add-a-cloud-property"></a>Een Cloud eigenschap toevoegen

1. Selecteer in het menu Apparaatbeheer de optie **Cloud eigenschap**.
1. Selecteer **+ eigenschap Cloud toevoegen**om een nieuwe Cloud eigenschap toe te voegen. In azure IoT Central kunt u een eigenschap toevoegen die relevant is voor een apparaat, maar die niet naar verwachting door het apparaat wordt verzonden. Een voor beeld van een dergelijke eigenschap is een waarschuwings drempel die specifiek is voor een installatie gebied, activa-informatie of onderhouds informatie.
1. Selecteer **Opslaan**.

### <a name="explore-views"></a>Weer gaven verkennen

De sjabloon voor bewaking van water kwaliteit wordt geleverd met vooraf gedefinieerde weer gaven. De weer gaven bepalen hoe Opera tors de gegevens van het apparaat zien en Cloud eigenschappen instellen. Bekijk de weer gaven en voer wijzigingen door te voeren.

  ![Sjabloon weergaven voor apparaten](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>De sjabloon voor het apparaat publiceren

Als u wijzigingen aanbrengt, selecteert u **publiceren** om de sjabloon te publiceren.

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken

1. Selecteer **+ Nieuw** om een nieuwe sjabloon voor het apparaat te maken en volg het aanmaak proces.
1. Maak een aangepaste apparaatprofiel of kies een sjabloon in de Azure IoT-apparaatinstantie.

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

De toepassing voor het controleren van water kwaliteit die u hebt gemaakt op basis van de toepassings sjabloon heeft twee gesimuleerde apparaten. Deze apparaten zijn gekoppeld aan de sjabloon voor bewaking van water kwaliteit.

### <a name="view-the-devices"></a>De apparaten weer geven

1. Selecteer **apparaten** in het meest linkse deel venster van uw toepassing.

   ![Apparaten](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selecteer één gesimuleerd apparaat.

    ![Apparaat 1 selecteren](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Wijzig op het tabblad **Cloud eigenschappen** de drempel waarde voor **zuren (pH)** van **8** in **9**.
1. Verken het tabblad **Apparaateigenschappen** en het tabblad **dash board** voor het apparaat.

> [!NOTE]
> Alle tabbladen zijn geconfigureerd in de **weer gaven**van de apparaatinstellingen.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

Selecteer op het tabblad **apparaten** **+ Nieuw** om een nieuw apparaat toe te voegen.

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In azure IoT Central kunt u regels maken die de telemetrie van apparaten automatisch bewaken. Deze regels activeren een actie wanneer aan een van de voor waarden wordt voldaan. Een mogelijke actie is het verzenden van e-mail meldingen. Andere mogelijkheden zijn onder andere een Microsoft Flow actie of een webhook-actie voor het verzenden van gegevens naar andere services.

De toepassing voor het controleren van water kwaliteit die u hebt gemaakt heeft twee vooraf geconfigureerde regels.

### <a name="view-rules"></a>Regels weer geven

1. Selecteer **regels** in het meest linkse deel venster van uw toepassing.

   ![Regels](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selecteer **hoge pH-waarschuwing**. Dit is een van de vooraf geconfigureerde regels in de toepassing.

   ![De hoge pH-waarschuwings regel](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   De **hoge pH-waarschuwings** regel is geconfigureerd om te controleren of de zuur voorwaarde (pH) groter is dan 8.

Voeg vervolgens een e-mail actie toe aan de regel:

1. Selecteer **+ e-mail adres**.
1. Voer in het vak **weergave naam** **hoge pH-waarschuwing**in.
1. Voer in het vak **aan** het e-mail adres in dat is gekoppeld aan uw Azure IOT Central-account.
1. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mail bericht.
1. Selecteer **gereed** om de actie te volt ooien.
1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren.

Binnen een paar minuten ontvangt u een e-mail wanneer aan de geconfigureerde voor waarde is voldaan.

> [!NOTE]
> De toepassing verzendt een e-mail bericht telkens wanneer aan een voor waarde wordt voldaan. Selecteer **uitschakelen** voor een regel om te stoppen met het ontvangen van automatische e-mail van deze regel.
  
Als u een nieuwe regel wilt maken, selecteert u **regels** in het meest linkse deel venster van uw toepassing en selecteert u **+ Nieuw**.

## <a name="configure-jobs"></a>Taken configureren

Met Azure IoT Central Jobs kunt u updates voor apparaat-of Cloud eigenschappen op meerdere apparaten activeren. U kunt ook taken gebruiken om opdrachten op meerdere apparaten te activeren. De werk stroom wordt door Azure IoT Central geautomatiseerd.

1. Selecteer **taken** in het meest linkse deel venster van uw toepassing.
1. Selecteer **+ Nieuw** en configureer een of meer taken.

## <a name="customize-your-application"></a>Uw toepassing aanpassen

Als ontwerper kunt u verschillende instellingen wijzigen om de gebruikers ervaring in uw toepassing aan te passen.

1. Selecteer **beheer** > **uw toepassing**aan te passen.
1. Onder **toepassings logo**selecteert u **wijzigen** om de afbeelding te kiezen die u als logo wilt uploaden.
1. Selecteer onder **browser pictogram** **wijzigen** om de afbeelding te kiezen die wordt weer gegeven op de tabbladen van de browser.
1. Onder **browser kleuren**kunt u de standaard waarden vervangen door hexadecimale HTML-kleur codes.
1. Selecteer **instellingen** om de waarde van het **thema**te wijzigen.

   ![Uw toepassing aanpassen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>De installatie kopie van de toepassing bijwerken

1. Selecteer **beheer** > **Toepassings instellingen**.

1. Gebruik de knop **afbeelding selecteren** om een afbeelding te kiezen die u als de installatie kopie van de toepassing wilt uploaden.

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw toepassing niet wilt blijven gebruiken, verwijdert u de toepassing met de volgende stappen:

1. Open het tabblad **beheer** in het meest linkse deel venster van uw toepassing.
1. Selecteer **Toepassings instellingen** en selecteer de knop **verwijderen** .

    ![Uw toepassing verwijderen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [concepten voor water kwaliteit](./concepts-waterqualitymonitoring-architecture.md).
