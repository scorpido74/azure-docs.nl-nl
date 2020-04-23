---
title: 'Zelfstudie: Een app voor het bewaken van de waterkwaliteit maken met Azure IoT Central'
description: 'Zelfstudie: Meer informatie over het maken van een toepassing voor het bewaken van de waterkwaliteit met azure IoT Central-toepassingssjablonen.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ec999d63175f63c1de6e31fdb3f72c963d228c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024469"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Zelfstudie: Een toepassing voor het bewaken van de waterkwaliteit maken in Azure IoT Central



Deze zelfstudie begeleidt u bij het maken van een toepassing voor het bewaken van de waterkwaliteit in Azure IoT Central. U maakt de toepassing op de toepassingssjabloon Azure IoT Central **Water quality monitoring.**

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik de sjabloon **voor het bewaken van de waterkwaliteit** om een toepassing voor het bewaken van de waterkwaliteit te maken.
> * Een bedieningsdashboard verkennen en aanpassen.
> * Bekijk een sjabloon voor het bewaken van de waterkwaliteit.
> * Ontdek gesimuleerde apparaten.
> * Regels verkennen en configureren.
> * Taken configureren.
> * Pas toepassingsbranding aan met behulp van white labeling.

## <a name="prerequisites"></a>Vereisten

We raden u aan een Azure-abonnement te hebben om deze zelfstudie te voltooien. Als u geen Azure-abonnement hebt, u er een maken op de [aanmeldingspagina van Azure.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Een toepassing voor kwaliteitsbewaking maken in Azure IoT Central

In deze sectie gebruikt u de azure IoT Central **Water quality monitoring** template om een toepassing voor het bewaken van de waterkwaliteit te maken.

1. Ga naar de [startpagina van Azure IoT Central](https://aka.ms/iotcentral).

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen. Meld u anders aan met een Microsoft-account:

    ![Aanmelden bij uw organisatieaccount](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Selecteer **Bouwen** in het meest linkse deelvenster van Azure IoT Central en selecteer het tabblad **Overheid.** In het overheidsvenster worden verschillende toepassingssjablonen van de overheid weergegeven.

    ![Sjablonen voor overheidstoepassingen](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selecteer de toepassingssjabloon **Voor het bewaken van de waterkwaliteit.** Deze toepassingssjabloon bevat een apparaatsjabloon voor waterkwaliteit, gesimuleerde apparaten, een bedieningsdashboard en vooraf geconfigureerde controleregels.

1. Selecteer **App maken**. Het **nieuwe toepassingsvenster** wordt geopend en toont de volgende elementen:

    * **Toepassingsnaam:** Standaard is de toepassingsnaam **Waterkwaliteitsbewaking,** gevolgd door een unieke ID-tekenreeks die Azure IoT Central genereert. Als u wilt, u een weergavenaam invoeren of de naam van de toepassing later wijzigen.
    * **URL:** U elke gewenste URL invoeren of de URL-waarde later wijzigen.
    * Als u een Azure-abonnement hebt, voert u waarden in voor **Directory,** **Azure-abonnement**en **Regio**. Als u geen abonnement hebt, u **een gratis proefperiode van 7 dagen** inschakelen en de vereiste contactgegevens invullen.

    Zie Snel aan de [slag](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor meer informatie over mappen en abonnementen.

1. Selecteer de knop **Maken** linksonder op de pagina.

    ![De pagina met nieuwe toepassingen van Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![De factureringsgegevens voor nieuwe toepassingen van Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

U hebt nu een toepassing voor het bewaken van de waterkwaliteit gemaakt met behulp van de azure IoT Central **Water quality monitoring** template.

Uw nieuwe toepassing wordt geleverd met deze vooraf geconfigureerde componenten:

* Bedieningsdashboards
* Sjablonen voor het bewaken van waterkwaliteitsapparatuur
* Gesimuleerde meetapparatuur voor waterkwaliteit
* Regels en banen
* Branding die gebruik maakt van white labeling

U uw toepassing op elk gewenst moment wijzigen.

Verken vervolgens de toepassing en maak enkele aanpassingen.

## <a name="explore-and-customize-the-operator-dashboard"></a>Het bedieningsdashboard verkennen en aanpassen

Nadat u de toepassing hebt gemaakt, wordt het **dashboardvenster voor de kwaliteit van** de brede wereld geopend.

   ![Het dashboard voor het bewaken van de waterkwaliteit](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Als bouwer u weergaven op het dashboard maken en aanpassen voor gebruik door operators. Maar voordat u probeert aan te passen, verken eerst het dashboard.

Alle gegevens in het dashboard zijn gebaseerd op gesimuleerde apparaatgegevens, die in de volgende sectie worden besproken.

Het dashboard bevat de volgende soorten tegels:

* **Wide World water utility image tile**: De eerste tegel in de linkerbovenhoek van het dashboard is een afbeelding die het fictieve hulpprogramma genaamd Wide World toont. U de tegel aanpassen om uw eigen afbeelding te gebruiken, of u de tegel verwijderen.

* **Gemiddelde pH KPI-tegels:** KPI-tegels zoals **gemiddelde pH in de laatste 30 minuten** bevinden zich boven aan het dashboardvenster. U KPI-tegels aanpassen en elk instellen op een ander type en tijdsbereik.

* **Watermonitoringgebiedskaart:** Azure IoT Central maakt gebruik van Azure Maps, die u direct in uw toepassing instellen om de locatie van het apparaat weer te geven. U locatiegegevens van uw toepassing ook toewijzen aan uw apparaat en vervolgens Azure Maps gebruiken om de informatie op een kaart weer te geven. Deplaats over de kaart en probeer de besturingselementen.

* **Gemiddelde pH-verdeling heat-map grafiek**: U verschillende visualisatiegrafieken selecteren om apparaattelemetrie weer te geven op de manier die het meest geschikt is voor uw toepassing.

* **Lijndiagram voor kritieke kwaliteitsindicatoren**: U apparaattelemetrie visualiseren die is uitgezet als een lijndiagram over een tijdsbereik.  

* **Concentratie van chemische agentia staafdiagram**: U apparaattelemetrie visualiseren in een staafdiagram.

* **Actieknop**: Het dashboard bevat een tegel voor acties die een operator rechtstreeks vanuit het bewakingsdashboard kan starten. Het opnieuw instellen van de eigenschappen van een apparaat is een voorbeeld van dergelijke acties.

* **Tegels voor eigenschappenlijst:** het dashboard heeft meerdere eigenschapstegels die drempelgegevens, informatie over apparaatstatus en onderhoudsgegevens weergeven.

### <a name="customize-the-dashboard"></a>Het dashboard aanpassen

Als bouwer u weergaven op het dashboard aanpassen voor gebruik door operators.

1. Selecteer **Bewerken** om het **dashboardvenster voor de waterkwaliteit van Wide World** aan te passen. U het dashboard aanpassen door opdrachten te selecteren in het menu **Bewerken.** Nadat het dashboard in de bewerkingsmodus is ingeschakeld, u nieuwe tegels toevoegen of de bestaande bestanden configureren.

    ![Uw dashboard bewerken](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Selecteer **+ Nieuw** om een nieuw dashboard te maken dat u configureren. U meerdere dashboards hebben en er tussen navigeren vanuit het dashboardmenu.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Een sjabloon voor het bewaken van waterkwaliteit verkennen

Een apparaatsjabloon in Azure IoT Central definieert de mogelijkheden van een apparaat. Beschikbare mogelijkheden zijn telemetrie, eigenschappen en opdrachten. Als bouwer u apparaatsjablonen definiëren in Azure IoT Central die de mogelijkheden van de verbonden apparaten vertegenwoordigen. U ook gesimuleerde apparaten maken om uw apparaatsjabloon en -toepassing te testen.

De toepassing voor het bewaken van de waterkwaliteit die u hebt gemaakt, wordt geleverd met een sjabloon voor het bewaken van de waterkwaliteit.

Ga als een voorbeeld van het apparaat:

1. Selecteer **Apparaatsjablonen** in het meest linkse deelvenster van uw toepassing in Azure IoT Central.
1. Selecteer **waterkwaliteitsmonitor**in de lijst met apparaatsjablonen . Die apparaatsjabloon wordt geopend.

    ![De apparaatsjabloon](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>De apparaatsjabloon aanpassen

Oefen met het aanpassen van de volgende apparaatsjablooninstellingen:

1. Selecteer **Aanpassen**in het menu apparaatsjabloon .
1. Ga naar het type **telemetrie temperatuur.**
1. Wijzig de waarde van de **weergavenaam** in **De gerapporteerde temperatuur**.
1. Wijzig de maateenheid of stel **de Min-waarde** en **de maximumwaarde**in.
1. Selecteer **Opslaan**.

#### <a name="add-a-cloud-property"></a>Een cloudeigenschap toevoegen

1. Selecteer de eigenschap Cloud in het menu **Apparaatsjabloon**.
1. Als u een nieuwe cloudeigenschap wilt toevoegen, selecteert u **+ Cloudeigenschap toevoegen**. In Azure IoT Central u een eigenschap toevoegen die relevant is voor een apparaat, maar die naar verwachting niet door het apparaat wordt verzonden. Een voorbeeld van een dergelijke eigenschap is een waarschuwingsdrempel die specifiek is voor installatiegebied, asset-informatie of onderhoudsinformatie.
1. Selecteer **Opslaan**.

### <a name="explore-views"></a>Bekijk weergaven

De sjabloon voor het bewaken van de waterkwaliteit wordt geleverd met vooraf gedefinieerde weergaven. De weergaven definiëren hoe operators de apparaatgegevens zien en cloudeigenschappen instellen. Bekijk de weergaven en oefen met het aanbrengen van wijzigingen.

  ![Weergave van apparaatsjabloon's](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Als u wijzigingen aanbrengt, moet u **Publiceren** selecteren om de apparaatsjabloon te publiceren.

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken

1. Selecteer **+ Nieuw** om een nieuwe apparaatsjabloon te maken en volg het creatieproces.
1. Maak een aangepaste apparaatsjabloon of kies een apparaatsjabloon uit de Azure IoT-apparaatcatalogus.

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

De toepassing voor het bewaken van de waterkwaliteit die u hebt gemaakt op basis van de toepassingssjabloon, heeft twee gesimuleerde apparaten. Deze apparaten worden toegewezen aan de sjabloon voor het controlesysteem voor de waterkwaliteit.

### <a name="view-the-devices"></a>Bekijk de apparaten

1. Selecteer **Apparaten** in het meest linkse deelvenster van uw toepassing.

   ![Apparaten](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selecteer één gesimuleerd apparaat.

    ![Apparaat 1 selecteren](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Wijzig op het tabblad **Cloudeigenschappen** de **drempelwaarde Zuurgraad (pH)** van **8** naar **9**.
1. Ga naar het tabblad **Apparaateigenschappen** en het tabblad **Apparaatdashboard.**

> [!NOTE]
> Alle tabbladen zijn geconfigureerd vanuit de sjabloonweergaven van **het apparaat.**

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

Selecteer **op** het tabblad Apparaten de optie **+ Nieuw** om een nieuw apparaat toe te voegen.

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In Azure IoT Central u regels maken die automatisch de telemetrie van het apparaat controleren. Deze regels leiden tot een actie wanneer aan een van hun voorwaarden is voldaan. Een mogelijke actie is het verzenden van e-mailmeldingen. Andere mogelijkheden zijn een Microsoft Flow-actie of een webhook-actie om gegevens naar andere services te verzenden.

De toepassing voor het bewaken van de waterkwaliteit die u hebt gemaakt, heeft twee vooraf geconfigureerde regels.

### <a name="view-rules"></a>Regels weergeven

1. Selecteer **Regels** in het meest linkse deelvenster van uw toepassing.

   ![Regels](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selecteer **Hoge pH-waarschuwing**, een van de vooraf geconfigureerde regels in de toepassing.

   ![De hoge pH-waarschuwingsregel](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   De **hoge pH-waarschuwingsregel** is geconfigureerd om te controleren of de toestand van de zuurgraad (pH) groter is dan 8.

Voeg vervolgens een e-mailactie toe aan de regel:

1. Selecteer **+ E-mail**.
1. Voer in het vak **Weergavenaam** **de pH-waarschuwing Hoog in**.
1. Voer in het vak **Aan** het e-mailadres in dat is gekoppeld aan uw Azure IoT Central-account.
1. Voer eventueel een notitie in die u wilt opnemen in de tekst van de e-mail.
1. Selecteer **Gereed** om de actie te voltooien.
1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren.

Binnen een paar minuten ontvangt u e-mail wanneer aan de geconfigureerde voorwaarde is voldaan.

> [!NOTE]
> De toepassing stuurt e-mail elke keer dat aan een voorwaarde wordt voldaan. Selecteer **Uitschakelen** voor een regel om geen automatische e-mail meer te ontvangen van die regel.
  
Als u een nieuwe regel wilt maken, selecteert u **Regels** in het meest linkse deelvenster van uw toepassing en selecteert u **+Nieuw**.

## <a name="configure-jobs"></a>Taken configureren

Met Azure IoT Central-taken u updates voor apparaat- of cloudeigenschappen op meerdere apparaten activeren. U ook taken gebruiken om apparaatopdrachten op meerdere apparaten te activeren. Azure IoT Central automatiseert de workflow voor u.

1. Selecteer **Vacatures** in het meest linkse deelvenster van uw toepassing.
1. Selecteer **+Nieuw** en configureer een of meer taken.

## <a name="customize-your-application"></a>Uw toepassing aanpassen

Als bouwer u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen.

1. Selecteer **Beheer** > **Uw toepassing aanpassen**.
1. Selecteer **onder Toepassingslogo** **wijzigen** om de afbeelding te kiezen die u als logo wilt uploaden.
1. Selecteer **onder browserpictogram** **Wijzigen** om de afbeelding te kiezen die wordt weergegeven op browsertabbladen.
1. Onder **Browserkleuren**u de standaardwaarden vervangen door HTML-hexadecimale kleurcodes.
1. Selecteer **Instellingen** om de waarde van **Thema te**wijzigen.

   ![Uw toepassing aanpassen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>De toepassingsafbeelding bijwerken

1. Selecteer **Instellingen voor beheertoepassing** > **Application settings**.

1. Gebruik de knop **Afbeelding selecteren** om een afbeelding te kiezen die u wilt uploaden als de toepassingsafbeelding.

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw toepassing niet blijft gebruiken, verwijdert u de toepassing met de volgende stappen:

1. Open het tabblad **Beheer** in het meest linkse deelvenster van uw toepassing.
1. Selecteer **Toepassingsinstellingen** en selecteer de knop **Verwijderen.**

    ![Uw toepassing verwijderen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [concepten voor het bewaken van waterkwaliteit.](./concepts-waterqualitymonitoring-architecture.md)
