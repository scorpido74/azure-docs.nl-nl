---
title: Een verbonden afval beheer-app maken met Azure IoT Central | Microsoft Docs
description: Leer hoe u een verbonden afval beheer toepassing maakt met behulp van Azure IoT Central toepassings sjablonen.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a0bf3b555334b67659975c150711eab02b957c6e
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026604"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Zelf studie: een verbonden afval beheer toepassing maken in IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt u begeleid bij het maken van een met Azure IoT Central verbonden afval beheer toepassing vanuit de sjabloon IoT Central **verbonden afval beheer** toepassing. 

In deze zelf studie leert u het volgende: 

> [!div class="checklist"]
> * Gebruik de sjabloon voor het beheer van verbonden **afval** van Azure IOT Central om uw verbonden afval beheer toepassing te maken
> * Het dash board van de operator verkennen en aanpassen 
> * Sjabloon voor het apparaat voor verbonden afval bakken verkennen
> * Gesimuleerde apparaten verkennen
> * Regels verkennen en configureren
> * Taken configureren
> * Uw huis stijl aanpassen met behulp van witlabels

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelf studie te volt ooien:
-  U wordt aangeraden een Azure-abonnement te nemen. U kunt eventueel een gratis proef versie van 7 dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Een verbonden afval beheer-app maken in IoT Central

In deze sectie gebruiken we de Azure IoT Central-sjabloon voor het **beheer van verbonden afval** om uw verbonden afval beheer toepassing in IOT Central te maken.

Een nieuwe Azure IoT Central-toepassing voor het beheer van verbonden afval maken:  

1. Ga naar de website van de [Azure IOT Central-start pagina](https://aka.ms/iotcentral) .

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om het te openen. u kunt zich ook aanmelden met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Klik op **Build** in het linkerdeel venster en selecteer het tabblad **Government** . Op de pagina Government worden verschillende Government-toepassings sjablonen weer gegeven.

    ![Sjablonen voor Government-apps bouwen](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecteer de sjabloon **verbonden afval beheer** toepassing. Deze sjabloon bevat voor beelden van gekoppelde afval lade, gesimuleerd apparaat, operator dashboard en vooraf geconfigureerde bewakings regels.    

2. Klik op **app maken**, waarmee u een nieuw formulier voor het maken van een **toepassing** kunt openen met de volgende velden:
    * **Toepassings naam**. De toepassing maakt standaard gebruik van *verbonden afval beheer* , gevolgd door een unieke id-reeks die IOT Central gegenereerd. U kunt desgewenst een beschrijvende toepassings naam kiezen. U kunt de toepassings naam later ook wijzigen.
    * **URL** : u kunt desgewenst de gewenste URL kiezen. U kunt de URL later ook wijzigen. 
    * Als u een Azure-abonnement hebt, voert u uw *adres lijst, het Azure-abonnement en de regio*in. Als u geen abonnement hebt, kunt u een **gratis proef versie van 7 dagen** inschakelen en de vereiste contact gegevens volt ooien.  

    Zie de [snelstart over het maken van een toepassing](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)voor meer informatie over directory's en abonnementen.

5. Klik op de knop **maken** onder aan de pagina. 

    ![Azure IoT Central-pagina voor het maken van een verbonden afval toepassing](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)

6. U hebt nu een verbonden afval beheer-app gemaakt met behulp van de sjabloon voor het **beheer van verbonden afval**van Azure IOT Central. 

Gefeliciteerd! Uw zojuist gemaakte toepassing wordt geleverd met vooraf geconfigureerd:
* Voor beeld-operator dashboards
* Voor beeld van vooraf gedefinieerde gekoppelde afval lade-sjablonen voor apparaten
* Gesimuleerde gekoppelde afval bakken-apparaten
* Vooraf geconfigureerde regels en taken
* Voor beeld van een huis stijl met wit labelen 

Het is uw toepassing en u kunt deze op elk gewenst moment wijzigen. We gaan nu de toepassing verkennen en enkele aanpassingen aanbrengen.  

## <a name="explore-and-customize-operator-dashboard"></a>Het dash board van de operator verkennen en aanpassen 
Nadat u de toepassing hebt gemaakt die u hebt gelandd in het **dash board voor het beheer van verbonden afval**.

   ![Dash board voor verbonden afval beheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Als ontwerper kunt u weer gaven maken en aanpassen in het dash board voor Opera tors. Voordat u probeert te aanpassen, gaan we het dash board verkennen. 

> [!NOTE]
> Alle gegevens die in het dash board worden weer gegeven, zijn gebaseerd op gesimuleerde apparaatgegevens, die in de volgende sectie worden verkend. 

Het dash board bestaat uit verschillende soorten tegels:

* ***Tegel***: de eerste tegel in het dash board is een afbeeldings tegel van een fictief afval programma voor grote wereld wijd afval. U kunt de tegel aanpassen en uw eigen installatie kopie plaatsen of verwijderen. 

* Tegel met afbeeldingen van ***afval bakken***: u kunt afbeeldingen en inhouds tegels gebruiken om een visuele weer gave te maken van het apparaat dat wordt bewaakt samen met een beschrijvende tekst. 

* ***KPI-tegel op opvul niveau***: de tegel geeft een waarde weer die is gerapporteerd door een sensor op *opvul niveau* in een afval lade. *Opvul niveau* en andere Sens oren zoals *odor meter* of *gewicht* in een afval lade kunnen op afstand worden bewaakt. Een operator kan actie ondernemen, zoals de mand verzamelings truck voor het verzenden van de Prullenbak. 

*  ***Overzicht van de bewakings ruimte***: de kaart maakt gebruik van Azure Maps, die u rechtstreeks in azure IOT Central kunt configureren. Op de kaart tegel wordt de locatie van het apparaat weer gegeven. Beweeg de muis aanwijzer over de kaart en probeer de besturings elementen op de kaart uit, zoals inzoomen, uitzoomen of uitvouwen.

     ![Dash board toewijzing verbonden afval beheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **staaf diagram voor *opvulling, odor, gewichts niveau**: u kunt een of meer telemetriegegevens van een apparaat visualiseren in een staaf diagram. U kunt ook het staaf diagram verg Roten.  

  ![Staaf diagram voor verbonden afval beheer dashboard](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Inhouds tegel voor veld Services**: het dash board bevat een koppeling naar de integratie met Dynamics 365-veld services van uw Azure IOT Central-toepassing. U kunt bijvoorbeeld Field Services gebruiken om tickets te maken voor het verzenden van Collection-Services voor verplaatsen. 


### <a name="customize-dashboard"></a>Dash board aanpassen 

Als bouwer kunt u weer gaven in het dash board aanpassen voor Opera tors. U kunt het volgende proberen:
1. Klik op **bewerken** om het **Wide World Connected afval beheer-dash board**aan te passen. U kunt het dash board aanpassen door te klikken op het menu **bewerken** . Wanneer het dash board zich in de **bewerkings** modus bevindt, kunt u nieuwe tegels toevoegen of configureren 

    ![Dash board bewerken](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. U kunt ook klikken op **+ Nieuw** om een nieuw dash board te maken en helemaal nieuwe configureren. U kunt meerdere Dash boards hebben en u kunt scha kelen tussen uw Dash boards in het menu van het dash board. 

## <a name="explore-connected-waste-bin-device-template"></a>Sjabloon voor het apparaat voor verbonden afval bakken verkennen

Met een apparaatprofiel in azure IoT Central definieert u de mogelijkheid van een apparaat, dat telemetrie, eigenschappen of opdracht kan zijn. Als opbouw functie kunt u apparaatprofielen definiëren die de mogelijkheid vertegenwoordigen van de apparaten die u wilt verbinden. 
 

De **verbonden afval beheer** toepassing wordt geleverd met een voor beeld van een gekoppelde sjabloon voor het koppelen van afval bakken.

De sjabloon voor het apparaat weer geven:

1. Klik op **device-sjablonen** in het navigatie deel venster links van uw toepassing in IOT Central. 

    ![Apparaatprofiel](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. In de lijst met Apparaatinstellingen ziet u de **gekoppelde afval lade**. Open door op de naam te klikken.

3. Familirize met de mogelijkheden van het apparaat. U kunt zien dat Sens oren als *opvul niveau*, *odor meter*, *gewicht*, *locatie* , enzovoort worden gedefinieerd.

   ![Apparaatprofiel](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>De sjabloon voor het apparaat aanpassen

Probeer het volgende aan te passen:
1. Ga naar **aanpassen** via het menu van de sjabloon voor het apparaat
2. Het type telemetrie van `Odor meter` zoeken
3. Werk de **weergave naam** van `Odor meter` bij naar `Odor level`
4. U kunt ook de maat eenheid bijwerken of de *minimum waarde* en de *maximum waarde* instellen
5. Wijzigingen **Opslaan** 

### <a name="add-a-cloud-property"></a>Een Cloud eigenschap toevoegen 

1. Ga naar de **eigenschap Cloud** in het menu van de sjabloon voor het apparaat
2. Voeg een nieuwe Cloud eigenschap toe door op **+ Cloud eigenschap toevoegen**te klikken. In IoT Central kunt u een eigenschap toevoegen die relevant is voor het apparaat, maar niet naar verwachting door een apparaat wordt verzonden. Een voor beeld: een Cloud eigenschap kan een drempel waarde zijn die specifiek is voor de installatie, informatie over activa of onderhouds informatie, enzovoort. 
3. Wijzigingen **Opslaan** 
 
### <a name="views"></a>Weergaven 
* De sjabloon voor het koppelen van afval bakken is voorzien van vooraf gedefinieerde weer gaven. Bekijk de weer gaven en u kunt updates maken. De weer gaven bepalen hoe Opera tors de gegevens van het apparaat zien, maar ook Cloud eigenschappen in te voeren. 

  ![Sjabloon weergaven voor apparaten](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publiceren 

* Als u wijzigingen hebt aangebracht, moet u ervoor zorgen dat u de sjabloon voor het apparaat **publiceert** . 

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken 

* Selecteer **+ Nieuw** om een nieuwe sjabloon voor het apparaat te maken en volg het aanmaak proces. U kunt zelf een volledig nieuwe sjabloon maken of een sjabloon voor een apparaat uit de Azure-Apparaatbeheer kiezen. 

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

In IoT Central kunt u gesimuleerde apparaten maken om de sjabloon en toepassing van uw apparaat te testen. 

De **verbonden afval beheer** toepassing beschikt over twee gesimuleerde apparaten die zijn toegewezen aan de sjabloon voor aangesloten afval lade. 

### <a name="to-view-the-devices"></a>De apparaten weer geven:

1. Ga naar **apparaat** vanuit IOT Central navigatie deel venster links. 

   ![Apparaten](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Selecteer en klik op verbonden afval lade apparaat.  

     ![Apparaat 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Ga naar het tabblad **Cloud eigenschappen** , probeer de `Bin full alert threshold` waarde bij te werken van `95` naar `100`. 
* Verken **het tabblad apparaateigenschappen en** het tabblad **apparaat-dash board** . 

> [!NOTE]
> Houd er rekening mee dat alle tabbladen zijn geconfigureerd in de **weer gaven**van de apparaatinstellingen.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

* U kunt nieuwe apparaten toevoegen door op het tabblad **apparaten** op **+ Nieuw** te klikken. 

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In azure IoT Central kunt u regels maken om de telemetrie van apparaten automatisch te controleren en acties activeren wanneer aan een of meer voor waarden wordt voldaan. De acties omvatten mogelijk het verzenden van e-mail meldingen, het activeren van een Microsoft Flow actie of een webhook-actie voor het verzenden van gegevens naar andere services.

De toepassing voor het **beheer van verbonden afval** heeft vier voorbeeld regels.

### <a name="to-view-rules"></a>Regels weer geven:
1. Navigeren naar **regels** vanuit IOT Central navigatie deel venster links

   ![Regels](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. De **volledige waarschuwing** voor de lade selecteren

     ![Volledige bin-waarschuwing](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. De `Bin full alert` controleert wanneer **voor waarde** `Fill level is greater than or equal to Bin full alert threshold`.

    De `Bin full alert threshold` is een *Cloud eigenschap* die is gedefinieerd in de sjabloon voor `Connected waste bin` apparaat. 

Nu gaan we een e-mail actie maken.

### <a name="create-an-email-action"></a>Een e-mail actie maken
Een e-mail actie configureren in de acties lijst van de regel:
1. Selecteer **+ e-mail adres**. 
2. Voer een *hoge pH-waarschuwing* in als de beschrijvende **weergave naam** voor de actie.
3. Voer het e-mail **adres in dat is gekoppeld aan uw**IOT Central account in. 
4. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mail bericht.
5. Selecteer **gereed** om de actie te volt ooien.
6. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

U ontvangt een e-mail wanneer aan de geconfigureerde **voor waarde** is voldaan.

> [!NOTE]
> Telkens wanneer aan een voor waarde wordt voldaan, verzendt de toepassing een e-mail bericht. **Schakel** de regel uit om het ontvangen van e-mail van de automatische regel te stoppen. 
  
Een nieuwe regel maken: 
1. Selecteer **+ Nieuw** op de **regels** in het navigatie deel venster links.

## <a name="configure-jobs"></a>Taken configureren

In IoT Central kunt u met taken apparaat-of Cloud eigenschappen updates op meerdere apparaten activeren. Naast eigenschappen kunt u ook taken gebruiken om opdrachten op meerdere apparaten te activeren. De werk stroom wordt door IoT Central automatisch geautomatiseerd. 

1. Ga naar **taken** in het navigatie deel venster aan de linkerkant. 
2. Klik op **+ Nieuw** en configureer een of meer taken. 


## <a name="customize-your-application"></a>Uw toepassing aanpassen 

Als ontwerper kunt u verschillende instellingen wijzigen om de gebruikers ervaring in uw toepassing aan te passen.

### <a name="to-change-the-application-theme"></a>Het toepassings thema wijzigen:

1. Ga naar **beheer > uw toepassing**aan te passen.
3. Gebruik de knop **wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **toepassings logo**.
4. Gebruik de knop **wijzigen** om een **browser pictogram** afbeelding te kiezen die wordt weer gegeven op de tabbladen van de browser.
5. U kunt ook de standaard **browser kleuren** vervangen door HTML-hexadecimale kleur codes toe te voegen.

   ![Uw toepassing aanpassen met Azure IoT Central](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. U kunt ook installatie kopieën van toepassingen wijzigen door te gaan naar de **beheer > toepassings instellingen** en de knop **afbeelding selecteren** om een afbeelding te kiezen die u als de installatie kopie van de toepassing wilt uploaden.
7. Ten slotte kunt u het **thema** ook wijzigen door te klikken op **instellingen** in het Impressum van de toepassing.

  
## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u uw toepassing met de volgende stappen:

1. Open het tabblad beheer in het linkerdeel venster van uw IoT Central-toepassing.
2. Selecteer toepassings instellingen en klik onder aan de pagina op de knop verwijderen.

  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [concepten van een verbonden afval beheer](./concepts-connectedwastemanagement-architecture.md)
