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
ms.openlocfilehash: 0b100e0bebc7c5b26449e396650da5434334075d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112618"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Zelf studie: een toepassing voor het controleren van water verbruik maken in IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt u begeleid bij het maken van een Azure IoT Central-bewakings toepassing voor water gebruik vanuit de sjabloon voor de bewaking van IoT Central water verbruik. 

In deze zelf studie leert u het volgende: 

> [!div class="checklist"]
> * Gebruik de sjabloon voor het **controleren** van het water verbruik van Azure IOT Central voor het maken van uw water verbruik-bewakings toepassing
> * Het dash board van de operator verkennen en aanpassen 
> * Device-sjabloon verkennen
> * Gesimuleerde apparaten verkennen
> * Regels verkennen en configureren
> * Taken configureren
> * Uw huis stijl aanpassen met behulp van witlabels

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelf studie te volt ooien:
-  U wordt aangeraden een Azure-abonnement te nemen. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Een app voor het controleren van water verbruik maken in IoT Central

In deze sectie gebruiken we de sjabloon voor de bewaking van Azure IoT Central **water verbruik** om uw toepassing voor water gebruik in IOT Central te maken.

Voor het maken van een nieuwe Azure IoT Central-bewakings toepassing voor water verbruik:  

1. Ga naar de website van de [Azure IOT Central-start pagina](https://aka.ms/iotcentral) .

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om het te openen. u kunt zich ook aanmelden met een Microsoft-account:

    ![Voer uw organisatieaccount in](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Klik op **Build** in het linkerdeel venster en selecteer het tabblad **Government** . Op de pagina Government worden verschillende Government-toepassings sjablonen weer gegeven.

   ![Sjablonen voor Government-apps bouwen](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecteer de toepassings sjabloon **water verbruik bewaken** . Deze sjabloon bevat een voor beeld van een sjabloon voor water verbruik, gesimuleerd apparaat, operator dashboard en vooraf geconfigureerde bewakings regels.    

2. Klik op **app maken**, waarmee u een nieuw formulier voor het maken van een **toepassing** kunt openen met de volgende velden:
    * **Toepassings naam**: standaard gebruikt de toepassing *water verbruik bewaking* , gevolgd door een unieke ID-reeks die IOT Central gegenereerd. U kunt desgewenst een beschrijvende toepassings naam kiezen. U kunt de toepassings naam later ook wijzigen.
    * **URL**: IOT Central automatisch een URL genereren op basis van de naam van de toepassing. U kunt ervoor kiezen om de URL naar uw eigen smaak bij te werken. U kunt de URL later ook wijzigen. 
    * Als u een Azure-abonnement hebt, voert u uw *adres lijst, het Azure-abonnement en de regio*in. Als u geen abonnement hebt, kunt u een **gratis proef versie van 7 dagen** inschakelen en de vereiste contact gegevens volt ooien.  

    Zie de [snelstart over het maken van een toepassing](../preview/quick-deploy-iot-central.md)voor meer informatie over directory's en abonnementen.

5. Klik op de knop **maken** onder aan de pagina. 

    ![Pagina Toepassing maken van Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. U hebt nu een water bewakings-app gemaakt met behulp van de Azure IoT Central **water bewakings** sjabloon.

Gefeliciteerd. U bent klaar met het maken van de bewakings toepassing voor water kwaliteit, die wordt geleverd met vooraf geconfigureerde:
* Voor beeld-operator dashboards
* Voor beeld van vooraf gedefinieerde water stroom-en klep-Apparaatinstellingen
* Gesimuleerde water stroom-en slimme kleppen apparaten
* Vooraf geconfigureerde regels en taken
* Voor beeld van een huis stijl met wit labelen 

Het is uw toepassing en u kunt deze op elk gewenst moment wijzigen. We gaan nu de toepassing verkennen en enkele aanpassingen aanbrengen.  

## <a name="explore-and-customize-operator-dashboard"></a>Het dash board van de operator verkennen en aanpassen 
Nadat u de door u gelandde toepassing hebt gemaakt in het dash board voor de voorbeeld operator, het dash board voor het controleren van een **groot water verbruik**

   ![Bewakings dashboard water verbruik](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Als ontwerper kunt u weer gaven maken en aanpassen in het dash board voor Opera tors. Voordat u probeert te aanpassen, gaan we het dash board verkennen. 

> [!NOTE]
> Alle gegevens die in het dash board worden weer gegeven, zijn gebaseerd op gesimuleerde apparaatgegevens, die in de volgende sectie worden besproken. 
  
Het dash board bestaat uit verschillende soorten tegels:

* **Tegel**: de eerste tegel in het dash board is een afbeeldings tegel van een fictief water-hulp programma voor wereld wijd water. U kunt de tegel aanpassen en uw eigen installatie kopie plaatsen of verwijderen. 

* **Gemiddelde water stroom-KPI-tegel**: de KPI-tegel is geconfigureerd om te worden weer gegeven als een voor beeld *van het gemiddelde van de afgelopen 30 minuten*. U kunt KPI-tegels aanpassen en instellen op een ander type en tijds bereik.

* Vervolgens is het direct in de opdracht tegels van het dash board- *apparaat* om klep, **Open klep**of **instellen klep positie**te **sluiten**. Als u op de opdrachten klikt, gaat u naar de opdracht pagina apparaat gesimuleerd apparaat. In IoT Central een *opdracht* is een type *apparaat* dat u later kunt verkennen in het **gedeelte Device Temp late** van deze zelf studie.

*  **Kaart voor water distributiegebied**: de kaart maakt gebruik van Azure Maps, die u rechtstreeks in azure IOT Central kunt configureren. Op de kaart tegel wordt de locatie van het apparaat weer gegeven. Beweeg de muis aanwijzer over de kaart en probeer de besturings elementen op de kaart uit, zoals *Inzoomen*, *uitzoomen* of *uitvouwen*. 

    ![Dashboard kaart voor bewaking van water verbruik](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Lijn diagram voor **gemiddelde water stroom** en **omgevings omstandigheden**: u kunt een of meer apparaat-teleacties die zijn getekend als een lijn diagram over een gewenst tijds bereik visualiseren.  

* De **gemiddelde heatmap-grafiek**van de klep: u kunt het visualisatie type van een apparaat kiezen voor het weer geven van de distributie over een tijds bereik met een kleur index.

* De **tegel inhoud van waarschuwings drempelwaarde opnieuw instellen**: u kunt oproep naar actie-inhouds tegels opnemen in het insluiten van een koppeling naar een actie pagina. In dit geval stelt u de waarschuwings drempel opnieuw in voor de toepassings **taken** waar u updates kunt uitvoeren voor de apparaten-eigenschappen, die later worden besproken in de sectie **taken configureren** van deze zelf studie.

* **Eigenschappen tegels**: in het dash board worden de bewerkings gegevens van de **klep**weer gegeven, de **drempel waarden voor stroom waarschuwingen**en **onderhouds informatie over** Apparaateigenschappen.  


### <a name="customize-dashboard"></a>Dash board aanpassen 

Als bouwer kunt u weer gaven in het dash board aanpassen voor Opera tors. U kunt het volgende proberen:
1. Klik op **bewerken** om het **dash board voor het wereld wijd water verbruik te bewaken**. U kunt het dash board aanpassen door te klikken op het menu **bewerken** . Wanneer het dash board zich in de **bewerkings** modus bevindt, kunt u nieuwe tegels toevoegen of configureren 

     ![Dash board bewerken](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Klik op **+ Nieuw** om een nieuw dash board te maken en zelf te configureren. U kunt meerdere Dash boards hebben en u kunt scha kelen tussen uw Dash boards in het menu van het dash board. 

## <a name="explore-device-template"></a>Device-sjabloon verkennen
Een sjabloon voor een apparaat in azure IoT Central definieert de mogelijkheid van een apparaat, dat telemetrie, eigenschap of opdracht kan zijn. Als ontwerper kunt u een of meer apparaatprofielen definiëren in IoT Central die de mogelijkheid vertegenwoordigen van de apparaten die u wilt verbinden. 
 

De **bewakings toepassing water verbruik** wordt geleverd met twee referentie-apparaatnamen die een *stroom meter* en een *Slim klep* apparaat vertegenwoordigen. 

De sjabloon voor het apparaat weer geven:

1. Klik op **device-sjablonen** in het navigatie deel venster links van uw toepassing in IOT Central. 
    In de lijst met Apparaatinstellingen ziet u twee Device-sjablonen **stroom meter** en **slimme klep**

   ![Apparaatprofiel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Klik op **stroom meter** apparaat sjabloon en Raadpleeg de mogelijkheden van het apparaat 

     ![Stroom meter van Device-sjabloon](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>De sjabloon voor het apparaat aanpassen

Probeer het volgende aan te passen:
1. Ga naar **aanpassen** via het menu van de sjabloon voor het apparaat
2. Het type telemetrie van `Temperature` zoeken
3. Werk de **weergave naam** van `Temperature` bij naar `Reported temperature`
4. Maat eenheid bijwerken of *minimum waarde* en *maximum waarde* instellen
5. Wijzigingen **Opslaan** 

### <a name="add-a-cloud-property"></a>Een Cloud eigenschap toevoegen 
1. Ga naar de **eigenschap Cloud** in het menu van de sjabloon voor het apparaat
2. Voeg een nieuwe Cloud eigenschap toe door op **+ Cloud eigenschap toevoegen**te klikken. 
    In IoT Central kunt u een eigenschap toevoegen die relevant is voor het apparaat. Een voor beeld: een Cloud eigenschap kan een drempel waarde zijn die specifiek is voor de installatie, informatie over activa of onderhouds informatie, enzovoort. 
3. Wijzigingen **Opslaan** 
 
### <a name="views"></a>Weergaven 
De sjabloon voor het monitor apparaat water verbruik bevat vooraf gedefinieerde weer gaven. Bekijk de weer gaven en u kunt updates maken. De weer gaven bepalen hoe Opera tors de gegevens van het apparaat zien, maar ook Cloud eigenschappen in te voeren. 

  ![Sjabloon weergaven voor apparaten](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publiceren 
Als u wijzigingen hebt aangebracht, moet u ervoor zorgen dat u de sjabloon voor het apparaat **publiceert** . 

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken 
- Selecteer **+ Nieuw** om een nieuwe sjabloon voor het apparaat te maken en volg het aanmaak proces. U kunt zelf een volledig nieuwe sjabloon maken of een sjabloon voor een apparaat uit de Azure-Apparaatbeheer kiezen. 

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen
In IoT Central kunt u gesimuleerde apparaten maken om de sjabloon en toepassing van uw apparaat te testen. De toepassing voor het **controleren van water verbruik** heeft twee gesimuleerde apparaten die zijn toegewezen aan de *stroom meter* en sjablonen voor *Smart-klep* apparaten. 

### <a name="to-view-the-devices"></a>De apparaten weer geven:
1. Ga naar **apparaat** vanuit IOT Central navigatie deel venster links. 

   ![Apparaten](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Klik op één **Slim klep 1** 

    ![Apparaat 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  In de **opdrachten** van het apparaat ziet u de drie Apparaatinstellingen *Open klep*, *Sluit klep*en *instellen klep positie* die in de sjabloon *Slim klep* apparaat zijn gedefinieerd. 
4. Verken **het tabblad apparaateigenschappen en** het tabblad **apparaat-dash board** . 

> [!NOTE]
> Houd er rekening mee dat alle tabbladen zijn geconfigureerd in de weer gaven van de apparaatinstellingen.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen
* Voeg nieuwe apparaten toe door op het tabblad **apparaten** op **+ Nieuw** te klikken. 

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In azure IoT Central kunt u regels maken om de telemetrie van apparaten automatisch te controleren en acties activeren wanneer aan een of meer voor waarden wordt voldaan. De acties omvatten mogelijk het verzenden van e-mail meldingen of het activeren van een Microsoft Flow actie of een webhook-actie voor het verzenden van gegevens naar andere services.

De toepassing voor het **controleren van water verbruik** waarvoor u een sjabloon hebt gemaakt, heeft drie vooraf geconfigureerde regels.

### <a name="to-view-rules"></a>Regels weer geven:
1. Navigeer naar **regels** vanuit IOT Central navigatie deel venster links. 

   ![Regels](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Selecteer en klik op **hoge pH-waarschuwing** . Dit is een van de vooraf geconfigureerde regels in de toepassing.

     ![Hoge pH-waarschuwing](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    De `High flow alert` regel is geconfigureerd om te controleren op basis van de voor waarde `Acidity (pH)` `greater than` de `Max flow threshold`. De maximale stroom drempel is een Cloud eigenschap die is gedefinieerd in de Device *Smart klep* Device-sjabloon. De waarde van `Max flow threshold` wordt ingesteld per apparaatinstantie. 

Nu gaan we een e-mail actie maken.

Een actie toevoegen aan de regel:

1. Selecteer **+ e-mail adres**. 
1. Voer een *hoge pH-waarschuwing* in als de beschrijvende **weergave naam** voor de actie.
    * Voer het e-mail **adres in dat is gekoppeld aan uw**IOT Central account in. 
1. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mail bericht.
1. Selecteer **gereed** om de actie te volt ooien.
1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

Binnen een paar minuten ontvangt u een e-mail wanneer aan de geconfigureerde **voor waarde** is voldaan.

> [!NOTE]
> Telkens wanneer aan een voor waarde wordt voldaan, verzendt de toepassing een e-mail bericht. **Schakel** de regel uit om het ontvangen van e-mail van de automatische regel te stoppen. 
  
Een nieuwe regel maken: 
- Selecteer **+ Nieuw** op de **regels** in het navigatie deel venster links.

## <a name="configure-jobs"></a>Taken configureren

In IoT Central kunt u met taken updates van apparaten of Cloud eigenschappen activeren op meerdere apparaten. Naast eigenschappen kunt u ook taken gebruiken om opdrachten op meerdere apparaten te activeren. De werk stroom wordt door IoT Central automatisch geautomatiseerd. 

1. Ga naar **taken** in het navigatie deel venster aan de linkerkant. 
2. Klik op **+ Nieuw** en configureer een of meer taken. 


## <a name="customize-your-application"></a>Uw toepassing aanpassen 
Als ontwerper kunt u verschillende instellingen wijzigen om de gebruikers ervaring in uw toepassing aan te passen.

1.  Ga naar **beheer > uw toepassing**aan te passen.
3. Gebruik de knop **wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **toepassings logo**.
4. Gebruik de knop **wijzigen** om een **browser pictogram** afbeelding te kiezen die wordt weer gegeven op de tabbladen van de browser.
5. U kunt ook de standaard **browser kleuren** vervangen door HTML-hexadecimale kleur codes toe te voegen.

   ![Uw toepassing aanpassen met Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  U kunt ook installatie kopieën van toepassingen wijzigen door te gaan naar de **beheer > toepassings instellingen** en de knop **afbeelding selecteren** om een afbeelding te kiezen die u als de installatie kopie van de toepassing wilt uploaden. 
2. Ten slotte kunt u het **thema** ook wijzigen door te klikken op **instellingen** in het Impressum van de toepassing. 

  
## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u uw toepassing met de volgende stappen:

1. Open het tabblad beheer in het linkerdeel venster van uw IoT Central-toepassing. 
2. Selecteer toepassings instellingen en klik onder aan de pagina op de knop verwijderen. 


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [bewakings concepten van water verbruik](./concepts-waterconsumptionmonitoring-architecture.md)
