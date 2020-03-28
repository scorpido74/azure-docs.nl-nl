---
title: 'Zelfstudie: Een app voor verbonden afvalbeheer maken met Azure IoT Central'
description: 'Zelfstudie: Leer een beheertoepassing voor verbonden afvalbeheer maken met Azure IoT Central-toepassingssjablonen.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 274d725a0e7987c8cb15363ba276aec7ebc541d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77426357"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Zelfstudie: Een toepassing voor verbonden afvalbeheer maken in IoT Central



Met deze zelfstudie u een Azure IoT Central-toepassing voor verbonden afvalbeheer maken op basis van de sjabloon **voor afvalbeheerbeheer van** IoT Central Connected. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]

> * Gebruik de azure IoT Central **Connected-afvalbeheersjabloon** om uw beheertoepassing voor verbonden afvalbeheer te maken
> * Bedieningsdashboard verkennen en aanpassen 
> * Sjabloon voor verbonden afvalopslagapparaten verkennen
> * Gesimuleerde apparaten verkennen
> * Regels verkennen en configureren
> * Taken configureren
> * Uw toepassingsbranding aanpassen met whitelabeling

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:
-  Een Azure-abonnement wordt aanbevolen. U optioneel een gratis proefperiode van 7 dagen gebruiken. Als u geen Azure-abonnement hebt, u er een maken op de [aanmeldingspagina van Azure.](https://aka.ms/createazuresubscription)

## <a name="create-connected-waste-management-app-in-iot-central"></a>Connected Waste Management-app maken in IoT Central

In deze sectie gebruikt u de azure IoT Central **Connected-afvalbeheersjabloon** om uw toepassing voor verbonden afvalbeheer te maken in IoT Central.

Ga als volgende voor een nieuwe Azure IoT Central connected waste management-toepassing:  

1. Navigeer naar de website [van Azure IoT Central-startpagina.](https://aka.ms/iotcentral)

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen, anders meldt u zich aan met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Klik op **Bouwen** in het linkerdeelvenster en selecteer het tabblad **Overheid.** Op de overheidspagina worden verschillende toepassingssjablonen van de overheid weergegeven.

    ![Sjablonen voor overheidsapps maken](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecteer de toepassingssjabloon **Verbonden afvalbeheer.** Deze sjabloon bevat sjabloon voor een voorbeeld van verbonden afvalopslag, gesimuleerd apparaat, bedieningsdashboard en vooraf geconfigureerde controleregels.    

2. Klik **op App maken**, waarmee het formulier Nieuwe **toepassingwordt** geopend met de volgende velden:
    * **Toepassingsnaam**. Standaard maakt de toepassing gebruik *van Verbonden afvalbeheer,* gevolgd door een unieke ID-tekenreeks die IoT Central genereert. Kies eventueel een vriendelijke toepassingsnaam. U de naam van de toepassing later ook wijzigen.
    * **URL** – Optioneel u kiezen voor de gewenste URL. U de URL later ook wijzigen. 
    * Als u een Azure-abonnement hebt, voert u uw *directory, Azure-abonnement en Regio in.* Als u geen abonnement hebt, u **een gratis proefperiode van 7 dagen** inschakelen en de vereiste contactgegevens invullen.  

    Zie de [snelstart over het maken van een toepassing](../core/quick-deploy-iot-central.md)voor meer informatie over directory's en abonnementen.

5. Klik onder aan de pagina op De knop **Maken.** 

    ![Azure IoT Central Create Connected Waste Application pagina](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central-factureringsgegevens maken](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. U hebt nu een verbonden afvalbeheer-app gemaakt met behulp van de azure IoT Central **Connected-afvalbeheersjabloon.** 

Gefeliciteerd! Uw nieuw gemaakte toepassing wordt geleverd met vooraf geconfigureerd:
* Voorbeeldoperatordashboards
* Voorbeeld van vooraf gedefinieerde sjablonen voor verbonden afvalopslag
* Gesimuleerde aangesloten afvalbakapparaten
* Vooraf geconfigureerde regels en taken
* Voorbeeld van branding met behulp van white labeling 

Het is uw toepassing en u het op elk gewenst moment wijzigen. Laten we nu de toepassing verkennen en enkele aanpassingen maken.  

## <a name="explore-and-customize-operator-dashboard"></a>Bedieningsdashboard verkennen en aanpassen 
Na het maken van de applicatie kom je terecht in het **Wide Waste connected waste management dashboard.**

   ![Dashboard verbonden afvalbeheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Als bouwer u weergaven op het dashboard voor operators maken en aanpassen. Voordat u probeert aan te passen, gaan we het dashboard verkennen. 

> [!NOTE]
> Alle gegevens die in het dashboard worden weergegeven, zijn gebaseerd op gesimuleerde apparaatgegevens, die in de volgende sectie worden onderzocht. 

Het dashboard bestaat uit verschillende soorten tegels:

* ***Wide World Waste utility image tile***: de eerste tegel in het dashboard is een afbeeldingstegel van een fictief Afvalbedrijf "Wide World Waste". U de tegel aanpassen en uw eigen afbeelding plaatsen of verwijderen. 

* ***Afbeeldingstegel afvalopslag:*** u afbeeldings- en inhoudstegels gebruiken om een visuele weergave te maken van het apparaat dat wordt gecontroleerd, samen met een beschrijvende tekst. 

* ***KPI-tegel opvulniveau***: de tegel geeft een waarde weer die wordt gerapporteerd door een *vulniveausensor* in een afvalopslaglocatie. *Vulniveau* en andere sensoren zoals *geurmeter* of *gewicht* in een afvalbak kunnen op afstand worden gecontroleerd. Een operator kan actie ondernemen, zoals het verzenden van vuilniswagens. 

*  ***Gebiedskaart voor afvalbewaking***: de kaart maakt gebruik van Azure Maps, die u rechtstreeks configureren in Azure IoT Central. De kaarttegel geeft de locatie van het apparaat weer. Probeer over de kaart te zweven en probeer de besturingselementen over de kaart, zoals inzoomen, uitzoomen of uitvouwen.

     ![Dashboardkaart verbonden afvalbeheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Vul-, geur-, gewichtsbalkdiagram:** u één of meerdere telemetriegegevens van een apparaat visualiseren in een staafdiagram. U ook het staafdiagram uitbreiden.  

  ![Dashboarddiagram verbonden afvalbeheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Inhoudstegel Field Services**: het dashboard bevat een koppeling naar hoe u integreren met Dynamics 365 Field Services vanuit uw Azure IoT Central-toepassing. U bijvoorbeeld Field Services gebruiken om tickets te maken voor het verzenden van afvalophaalservices. 


### <a name="customize-dashboard"></a>Dashboard aanpassen 

Als bouwer u weergaven in dashboard voor operators aanpassen. U proberen:
1. Klik op **Bewerken** om het **dashboard voor verbonden afvalbeheer in De Wijwereld**aan te passen. U het dashboard aanpassen door op het menu **Bewerken te** klikken. Zodra het dashboard in de **bewerkingsmodus** is, u nieuwe tegels toevoegen of configureren 

    ![Dashboard bewerken](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. U ook klikken op **+ Nieuw** om nieuw dashboard te maken en configureren vanaf nul. U meerdere dashboards hebben en u navigeren tussen uw dashboards vanuit het dashboardmenu. 

## <a name="explore-connected-waste-bin-device-template"></a>Sjabloon voor verbonden afvalopslagapparaten verkennen

Een apparaatsjabloon in Azure IoT Central definieert de mogelijkheden van een apparaat, dat telemetrie, eigenschappen of opdrachten kan zijn. Als bouwer u apparaatsjablonen definiëren die de mogelijkheden vertegenwoordigen van de apparaten waarmee u verbinding maakt. 
 

De **toepassing Verbonden afvalbeheer** wordt geleverd met een sjabloon voor een sjabloon voor een apparaat met een eigen beheervan de afvalbak.

Ga als een voorbeeld van het apparaat:

1. Klik op **Apparaatsjablonen** in het linkerdeelvenster van uw toepassing in IoT Central. 

    ![Apparaatsjabloon](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. In de lijst Apparaatsjablonen ziet u **Verbonden afvalopslag**. Open door op de naam te klikken.

3. Vertrouwd te raken met de mogelijkheden van de apparaatsjabloon. U zien dat het definieert sensoren zoals *Vulniveau,* *Geur meter,* *gewicht,* *locatie,* en anderen.

   ![Apparaatsjabloon](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>De apparaatsjabloon aanpassen

Probeer het volgende aan te passen:
1. Navigeren **naar aanpassen vanuit** het menu apparaatsjabloon
2. Het `Odor meter` telemetrietype zoeken
3. De **weergavenaam** `Odor meter` bijwerken naar`Odor level`
4. U ook proberen de meeteenheid bijwerken of *de minwaarde* en *de maximumwaarde* instellen
5. **Eventuele** wijzigingen opslaan 

### <a name="add-a-cloud-property"></a>Een cloudeigenschap toevoegen 

1. Navigeren naar **de eigenschap Cloud** vanuit het menu apparaatsjabloon
2. Voeg een nieuwe cloudeigenschap toe door te klikken op **+ Cloudeigenschap toevoegen**. In IoT Central u een eigenschap toevoegen die relevant is voor het apparaat, maar waarvan niet wordt verwacht dat deze door een apparaat wordt verzonden. Een cloudproperty kan bijvoorbeeld een waarschuwingsdrempel zijn die specifiek is voor installatiegebied, asset-informatie of onderhoudsinformatie en andere informatie. 
3. **Eventuele** wijzigingen opslaan 
 
### <a name="views"></a>Weergaven 
* De sjabloon voor verbonden afvalopslagapparaten wordt geleverd met vooraf gedefinieerde weergaven. Bekijk de weergaven en je updates maken. De weergaven definiëren hoe operators de apparaatgegevens zien, maar ook cloud-eigenschappen invoeren. 

  ![Weergave van apparaatsjabloon's](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publiceren 

* Als u wijzigingen hebt aangebracht, moet u de apparaatsjabloon **publiceren.** 

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken 

* Selecteer **+ Nieuw** om een nieuwe apparaatsjabloon te maken en volg het creatieproces. U helemaal opnieuw een aangepaste apparaatsjabloon maken of u een apparaatsjabloon kiezen in de Azure-apparaatcatalogus. 

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

In IoT Central u gesimuleerde apparaten maken om uw apparaatsjabloon en -toepassing te testen. 

De **toepassing Verbonden afvalbeheer** heeft twee gesimuleerde apparaten die zijn toegewezen aan de sjabloon voor de aangesloten afvalopslag. 

### <a name="to-view-the-devices"></a>Ga als het gaat om het bekijken van de apparaten:

1. Navigeer naar **apparaat vanuit** het linkerdeelvenster van IoT Centraal. 

   ![Apparaten](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Selecteer en klik op het apparaat Verbonden afvalopslag.  

     ![Apparaat 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navigeer naar het tabblad **Cloudeigenschappen** en probeer de `Bin full alert threshold` waarde bij te werken van `95` . `100` 
* Ga naar het tabblad **Apparaateigenschappen** en het tabblad **Apparaatdashboard.** 

> [!NOTE]
> Houd er rekening mee dat alle tabbladen zijn geconfigureerd via de **apparaatsjabloonweergaven**.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

* U nieuwe apparaten toevoegen door op **+ Nieuw** te klikken op het tabblad **Apparaten.** 

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In Azure IoT Central u regels maken om automatisch te controleren op apparaattelemetrie en acties activeren wanneer aan een of meer voorwaarden is voldaan. De acties kunnen bestaan uit het verzenden van e-mailmeldingen, het activeren van een Microsoft Flow-actie of een webhook-actie om gegevens naar andere services te verzenden.

De toepassing **Verbonden afvalbeheer** heeft vier voorbeeldregels.

### <a name="to-view-rules"></a>Regels bekijken:
1. Navigeren naar **regels vanuit** het linkerdeelvenster van IoT Centraal

   ![Regels](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. De **waarschuwing Volledig opslaglocatie selecteren**

     ![Waarschuwing voor volledige opslaglocatie](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. De `Bin full alert` controles wanneer **voorwaarde** `Fill level is greater than or equal to Bin full alert threshold`.

    Het `Bin full alert threshold` is een *cloudeigenschap* `Connected waste bin` die is gedefinieerd in de apparaatsjabloon. 

Laten we nu een e-mailactie maken.

### <a name="create-an-email-action"></a>Een e-mailactie maken
Ga als lid van de actie voor de regel:
1. Selecteer **+ E-mail**. 
2. Voer *een hoge pH-waarschuwing in* als de vriendelijke **weergavenaam** voor de actie.
3. Voer het e-mailadres in dat is gekoppeld aan uw IoT Central-account in **Aan**. 
4. Voer eventueel een notitie in die u wilt opnemen in de tekst van de e-mail.
5. Selecteer **Gereed** om de actie te voltooien.
6. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

U moet e-mail ontvangen wanneer aan de geconfigureerde **voorwaarde** is voldaan.

> [!NOTE]
> De applicatie stuurt e-mail elke keer dat aan een voorwaarde wordt voldaan. **Schakel** de regel uit om geen e-mail meer te ontvangen van de geautomatiseerde regel. 
  
Ga als lid van het volgende over een nieuwe regel: 
1. Selecteer **+Nieuw** in het **linkerdeelvenster.**

## <a name="configure-jobs"></a>Taken configureren

Met IoT Central u met taken updates voor apparaat- of cloudeigenschappen activeren op meerdere apparaten. Naast eigenschappen u ook taken gebruiken om apparaatopdrachten op meerdere apparaten te activeren. IoT Central automatiseert de workflow voor u. 

1. Ga vanuit het linkerdeelvenster naar **Vacatures.** 
2. Klik **op +Nieuw** en configureer een of meer taken. 


## <a name="customize-your-application"></a>Uw toepassing aanpassen 

Als bouwer u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen.

### <a name="to-change-the-application-theme"></a>Ga als u het toepassingsthema wijzigen:

1. Ga naar **Administratie > Uw toepassing aanpassen.**
3. Gebruik de knop **Wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **toepassingslogo.**
4. Gebruik de knop **Wijzigen** om een **browserafbeelding** te kiezen die wordt weergegeven op browsertabbladen.
5. U ook de **standaardbrowserkleuren** vervangen door HTML-hexadecimale kleurcodes toe te voegen.

   ![Azure IoT Central past uw toepassing aan](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. U toepassingsafbeeldingen ook wijzigen door naar de **instellingen voor beheer > toepassing** te gaan en **afbeeldingsknop selecteren** om een afbeelding te kiezen die u als toepassingsafbeelding wilt uploaden.
7. Ten slotte u **het thema** ook wijzigen door op **Instellingen** op de mastkop van de toepassing te klikken.

  
## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet blijft gebruiken, verwijdert u uw toepassing met de volgende stappen:

1. Open het tabblad Beheer in het linkerdeelvenster van uw IoT Central-toepassing.
2. Selecteer Toepassingsinstellingen en klik onder aan de pagina op Verwijderen.

  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [connected waste management concepten](./concepts-connectedwastemanagement-architecture.md)
