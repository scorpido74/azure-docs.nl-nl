---
title: Een app voor het controleren van water kwaliteit maken met Azure IoT Central
description: Meer informatie over het maken van een toepassing voor het controleren van water kwaliteit met behulp van Azure IoT Central-toepassings sjablonen.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 94e8695e01ca5e5c456ce230c4cc84b41685fd9c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026532"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-iot-central"></a>Zelf studie: een toepassing voor het controleren van water kwaliteit maken in IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt u begeleid bij het maken van een toepassing voor het controleren van Azure IoT Central water kwaliteit vanuit de sjabloon voor het controleren van de IoT Central water kwaliteit. 

In deze zelf studie leert u het volgende: 

> [!div class="checklist"]
> * Gebruik de sjabloon voor bewaking van Azure IoT Central **water kwaliteit** voor het maken van uw bewakings toepassing voor water kwaliteit
> * Het dash board van de operator verkennen en aanpassen 
> * Apparaatbeheer-sjabloon voor water kwaliteit verkennen
> * Gesimuleerde apparaten verkennen
> * Regels verkennen en configureren
> * Taken configureren
> * Uw huis stijl aanpassen met behulp van witlabels


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelf studie te volt ooien:
-  U wordt aangeraden een Azure-abonnement te nemen. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).


## <a name="create-water-quality-monitoring-app-in-iot-central"></a>Een app voor het controleren van water kwaliteit maken in IoT Central

In deze sectie gebruiken we de sjabloon voor de bewaking van Azure IoT Central **water kwaliteit** om uw toepassing voor water kwaliteit te controleren in IOT Central.


Een nieuwe toepassing voor het controleren van de IoT Central water kwaliteit van Azure maken:  

1. Ga naar de website van de [Azure IOT Central-start pagina](https://aka.ms/iotcentral) .

      Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om het te openen. u kunt zich ook aanmelden met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-waterqualitymonitoring/sign-in.png)

2. Klik op **Build** in het linkerdeel venster en selecteer het tabblad **Government** . Op de pagina Government worden verschillende Government-toepassings sjablonen weer gegeven.

    ![Sjablonen voor Government-apps bouwen](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)


1. Selecteer de toepassings sjabloon **water kwaliteit bewaken** . Deze sjabloon bevat een voor beeld van een apparaat sjabloon voor water kwaliteit, een gesimuleerd apparaat, een operator dashboard en vooraf geconfigureerde bewakings regels.    

2. Klik op **app maken**, waarmee u een nieuw formulier voor het maken van een **toepassing** kunt openen met de volgende velden:
    * **Toepassings naam**. De toepassing maakt standaard gebruik van *water kwaliteit bewaking* , gevolgd door een unieke id-reeks die IOT Central gegenereerd. U kunt desgewenst een beschrijvende toepassings naam kiezen. U kunt de toepassings naam later ook wijzigen.
    * **URL** : u kunt desgewenst de gewenste URL kiezen. U kunt de URL later ook wijzigen. 
    * Als u een Azure-abonnement hebt, voert u uw *adres lijst, het Azure-abonnement en de regio*in. Als u geen abonnement hebt, kunt u een **gratis proef versie van 7 dagen** inschakelen en de vereiste contact gegevens volt ooien.  

    Zie de [snelstart over het maken van een toepassing](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)voor meer informatie over directory's en abonnementen.

5. Klik op de knop **maken** onder aan de pagina. 

    ![Pagina Toepassing maken van Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)


6. U hebt nu een app voor het controleren van water kwaliteit gemaakt met behulp van de **bewakings sjabloon**van de Azure IOT Central water kwaliteit. 

Uw zojuist gemaakte toepassing wordt geleverd met vooraf geconfigureerd:
* Voor beeld-operator dashboards
* Voor beeld van vooraf gedefinieerde water kwaliteit monitor-sjablonen
* Bewakings apparaten voor gesimuleerde water kwaliteit
* Vooraf geconfigureerde regels en taken
* Voor beeld van een huis stijl met wit labelen 

Het is uw toepassing en u kunt deze op elk gewenst moment wijzigen. We gaan nu de toepassing verkennen en enkele aanpassingen aanbrengen.  


## <a name="explore-and-customize-operator-dashboard"></a>Het dash board van de operator verkennen en aanpassen 
Na het maken van de toepassing die u hebt gelandd in het dash board voor de bewaking van het **grote water water**.


   ![Bewakings dashboard voor water kwaliteit](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Als ontwerper kunt u weer gaven maken en aanpassen in het dash board voor Opera tors. Voordat u probeert te aanpassen, gaan we het dash board verkennen. 

Alle gegevens die in het dash board worden weer gegeven, zijn gebaseerd op gesimuleerde apparaatgegevens, die in de volgende sectie worden verkend. 

Het dash board bestaat uit verschillende soorten tegels:

* **Tegel**: de eerste tegel in het dash board is een afbeeldings tegel van een fictief water-hulp programma voor wereld wijd water. U kunt de tegel aanpassen en uw eigen installatie kopie plaatsen of verwijderen. 

* **Gemiddelde pH KPI-tegel**: u kunt zien dat er KPI-tegels bovenaan staan, zoals **gemiddeld pH in de laatste 30 minuten**. U kunt KPI-tegels aanpassen en instellen op een ander type en tijds bereik.

*  **Zone toewijzing water bewaking**: IoT Central gebruikt Azure Maps, die u rechtstreeks kunt instellen in uw toepassing en de locatie van het apparaat weer geven. U kunt ook locatie-informatie toewijzen aan uw apparaat vanuit de toepassing en kunt Azure Maps gebruiken om het weer te geven op een kaart. Beweeg de muis aanwijzer over de kaart en probeer de besturings elementen op de kaart uit te proberen. 

* **Gemiddeld heatmap diagram**voor de pH-distributie: u kunt verschillende visualisatie diagrammen kiezen om telemetriegegevens van apparaten weer te geven op de manier die het meest geschikt is voor uw toepassing. Heatmap 

* **Lijn diagram van kritieke kwaliteits indicatoren**: u kunt een of meer apparaat-teleacties die zijn getekend als een lijn diagram over een gewenst tijds bereik visualiseren.  

* **Staaf diagram voor de concentratie van chemische agents**: u kunt een of meer telemetriegegevens van een apparaat visualiseren in een staaf diagram, zoals in het voor beeld. 

* **Actie knop**: het dash board bevat een voor beeld van een actie tegel die een operator rechtstreeks vanuit het bewakings dashboard kan initiëren, zoals het starten van een actie om de eigenschappen van het apparaat opnieuw in te stellen. 

* **Tegels van eigenschappen lijst**: het dash board bevat meerdere eigenschappen tegels die drempel gegevens, informatie over Apparaatstatus en onderhouds informatie weer geven. 


### <a name="customize-dashboard"></a>Dash board aanpassen 

Als bouwer kunt u weer gaven in het dash board aanpassen voor Opera tors. 
1. Klik op **bewerken** om het dash board voor de bewaking van de **grote water kwaliteit**aan te passen. U kunt het dash board aanpassen door te klikken op het menu **bewerken** . Wanneer het dash board zich in de **bewerkings** modus bevindt, kunt u nieuwe tegels toevoegen of configureren

    ![Dash board bewerken](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

2. Klik op **+ Nieuw** om een nieuw dash board te maken en zelf te configureren. U kunt meerdere Dash boards hebben en u kunt scha kelen tussen uw Dash boards in het menu van het dash board.

## <a name="explore-water-quality-monitor-device-template"></a>Apparaatbeheer-sjabloon voor water kwaliteit verkennen
Met een sjabloon voor een apparaat in azure IoT Central definieert u de mogelijkheid van een apparaat, dat telemetrie, eigenschappen en opdrachten kan zijn. Als ontwerper kunt u Device-sjablonen definiëren in IoT Central die de mogelijkheid vertegenwoordigen van de apparaten die worden verbonden. In IoT Central kunt u ook gesimuleerde apparaten maken om de sjabloon en toepassing van uw apparaat te testen. 
 

De toepassing voor het **controleren van water kwaliteit** die u hebt gemaakt op basis van de toepassings sjabloon, wordt geleverd met een monitor sjabloon met een referentie water kwaliteit.

De sjabloon voor het apparaat weer geven:

1.  Klik op **device-sjablonen** in het navigatie deel venster links van uw toepassing in IOT Central. 
2. In de lijst met Apparaatinstellingen ziet u de **monitor water kwaliteit**. Open door op de naam te klikken.

    ![Apparaatprofiel](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customizing-the-device-template"></a>De sjabloon voor het apparaat aanpassen

Probeer het volgende aan te passen:
1. Ga naar **aanpassen** via het menu van de sjabloon voor het apparaat
2. Het type telemetrie van `Temperature` zoeken
3. Werk de **weergave naam** van `Temperature` bij naar `Reported temperature`
4. Maat eenheid bijwerken of *minimum waarde* en *maximum waarde* instellen
5. Wijzigingen **Opslaan** 

#### <a name="add-a-cloud-property"></a>Een Cloud eigenschap toevoegen 
1. Ga naar de **eigenschap Cloud** in het menu van de sjabloon voor het apparaat
2. Voeg een nieuwe Cloud eigenschap toe door op **+ Cloud eigenschap toevoegen**te klikken. In IoT Central kunt u een eigenschap toevoegen die relevant is voor het apparaat, maar niet naar verwachting door een apparaat wordt verzonden. Een voor beeld: een Cloud eigenschap kan een drempel waarde zijn die specifiek is voor de installatie, informatie over activa of onderhouds informatie, enzovoort. 
3. Wijzigingen **Opslaan** 
 
### <a name="views"></a>Weergaven 
De sjabloon monitor voor water kwaliteit wordt geleverd met vooraf gedefinieerde weer gaven. Bekijk de weer gaven en u kunt updates maken. De weer gaven bepalen hoe Opera tors de gegevens van het apparaat zien, maar ook Cloud eigenschappen in te voeren. 

  ![Sjabloon weergaven voor apparaten](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publiceren 

Als u wijzigingen hebt aangebracht, moet u ervoor zorgen dat u de sjabloon voor het apparaat **publiceert** . 


### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken 
1. Selecteer **+ Nieuw** om een nieuwe sjabloon voor het apparaat te maken en volg het aanmaak proces. 
2. Een volledig nieuwe sjabloon maken of een sjabloon voor een apparaat uit de Azure-Apparaatbeheer kiezen. 


## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

Voor de toepassing voor het **controleren van water kwaliteit** die u hebt gemaakt op basis van de toepassings sjabloon, zijn twee gesimuleerde apparaten toegewezen aan de sjabloon monitor voor water kwaliteit. 

### <a name="to-view-the-devices"></a>De apparaten weer geven:
1. Ga naar **apparaat** vanuit IOT Central navigatie deel venster links. 

   ![Apparaten](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)


2. Selecteer en klik op één gesimuleerd apparaat 

    ![Apparaat 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

3. Probeer op het tabblad **Cloud eigenschappen** de `Acidity (pH) Threshold` waarde bij te werken van `8` in `9`. 
4. Verken **het tabblad apparaateigenschappen en** het tabblad **apparaat-dash board** . 

> [!NOTE]
> Houd er rekening mee dat alle tabbladen zijn geconfigureerd in de **weer gaven**van de apparaatinstellingen.


### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen
U kunt nieuwe apparaten toevoegen door op het tabblad **apparaten** op **+ Nieuw** te klikken. 


## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In azure IoT Central kunt u regels maken om de telemetrie van apparaten automatisch te controleren en een actie activeren wanneer aan een of meer voor waarden wordt voldaan. De acties omvatten mogelijk het verzenden van e-mail meldingen of het activeren van een Microsoft Flow actie of een webhook-actie voor het verzenden van gegevens naar andere services.

De toepassing voor het **controleren van water kwaliteit** waarvoor u een sjabloon hebt gemaakt, heeft twee vooraf geconfigureerde regels.

### <a name="to-view-rules"></a>Regels weer geven:
1. Navigeer naar **regels** vanuit IOT Central navigatie deel venster links. 

   ![Regels](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)


2. Selecteer en klik op **hoge pH-waarschuwing** . Dit is een van de vooraf geconfigureerde regels in de toepassing. 

     ![Hoge pH-waarschuwing](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

    De `High pH alert` regel is geconfigureerd om te controleren op basis van de voor waarde `Acidity (pH) is greater than 8`.

Nu gaan we een e-mail actie maken.

Een actie toevoegen aan de regel:

1. Selecteer **+ e-mail adres**. 
2.  Voer een *hoge pH-waarschuwing* in als de beschrijvende **weergave naam** voor de actie.
3. Voer het e-mail **adres in dat is gekoppeld aan uw**IOT Central account in. 
4. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mail bericht.
5. Selecteer **gereed** om de actie te volt ooien.
6. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

Binnen een paar minuten ontvangt u een e-mail wanneer aan de geconfigureerde **voor waarde** is voldaan.

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

1. Selecteer **beheer > uw toepassing**aan te passen.
2. Gebruik de knop **wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **toepassings logo**.
3.  Gebruik de knop **wijzigen** om een **browser pictogram** afbeelding te kiezen die wordt weer gegeven op de tabbladen van de browser.
4. U kunt ook de standaard **browser kleuren** vervangen door HTML-hexadecimale kleur codes toe te voegen.
5. Wijzig ook het **thema** door te klikken op de **instellingen** op het Impressum.

   ![Uw toepassing aanpassen met Azure IoT Central](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="to-update-the-application-image"></a>De installatie kopie van de toepassing bijwerken:

6.  Selecteer **beheer > toepassings instellingen**.

7. Gebruik de knop **afbeelding selecteren** om een afbeelding te kiezen die u als de installatie kopie van de toepassing wilt uploaden. 


  
## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u uw toepassing met de volgende stappen:

1. Open het tabblad beheer in het linkerdeel venster van uw IoT Central-toepassing.
2. Selecteer toepassings instellingen en klik onder aan de pagina op de knop verwijderen.

    ![Toepassing verwijderen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)        



## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [bewakings concepten voor water kwaliteit](./concepts-waterqualitymonitoring-architecture.md)