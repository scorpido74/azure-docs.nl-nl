---
title: 'Zelfstudie: Een app voor het bewaken van waterverbruik maken met Azure IoT Central'
description: 'Zelfstudie: Leer een toepassing voor het bewaken van waterverbruik maken met Azure IoT Central-toepassingssjablonen.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7f00947504e5c6355379ce1e400fd2325016e05a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77122060"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Zelfstudie: Een toepassing voor het bewaken van waterverbruik maken met Azure IoT Central

In deze zelfstudie ziet u hoe u een Azure IoT Central-toepassing voor waterverbruik maken met behulp van de azure IoT Central-toepassingssjabloon voor waterverbruik.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik de azure IoT Central-sjabloon voor waterverbruik om uw toepassing voor het bewaken van het waterverbruik te maken.
> * Het bedieningsdashboard verkennen en aanpassen.
> * Verken apparaatsjablonen.
> * Ontdek gesimuleerde apparaten.
> * Regels verkennen en configureren.
> * Taken configureren.
> * Pas uw toepassingsbranding aan met behulp van white labeling.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, u er een maken op de [aanmeldingspagina van Azure.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Een app voor waterverbruikcontrole maken met Azure IoT Central

In deze sectie gebruikt u de azure IoT Central-sjabloon voor waterverbruik om uw toepassing voor waterverbruikmonitoring te maken in Azure IoT Central.

Ga als volgt te werk om een nieuwe Azure IoT Central-toepassing voor waterverbruik te maken:

1. Ga naar de website van [Azure IoT Central op de startpagina.](https://aka.ms/iotcentral)

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen. Meld u anders aan met een Microsoft-account.

    ![Voer uw organisatieaccount in](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Selecteer **Bouwen** in het linkerdeelvenster en selecteer het tabblad **Overheid.** Op **de pagina Overheid** worden verschillende toepassingssjablonen van de overheid weergegeven.

   ![Sjablonen voor overheidsapps maken](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecteer de toepassingssjabloon **Waterverbruikcontrole.**
Deze sjabloon bevat een sjabloon voor een voorbeeldvan waterverbruiksapparaat, een gesimuleerd apparaat, een bedieningsdashboard en vooraf geconfigureerde controleregels.

1. Selecteer **App maken** om het formulier Voor het maken van nieuwe **toepassingen** met de volgende velden te openen:
    * **Toepassingsnaam:** Standaard gebruikt de toepassing *Waterverbruikscontrole,* gevolgd door een unieke ID-tekenreeks die Azure IoT Central genereert. Kies eventueel een vriendelijke toepassingsnaam. U de naam van de toepassing later ook wijzigen.
    * **URL**: Azure IoT Central genereert automatisch een URL op basis van de naam van de toepassing. U ervoor kiezen om de URL naar wens bij te werken. U de URL later ook wijzigen.
    * Als u een Azure-abonnement hebt, voert u uw **directory,** **Azure-abonnement**en **locatiegegevens** in. Als u geen abonnement hebt, u de **gratis proefperiodeoptie van 7 dagen** selecteren en de vereiste contactgegevens invullen.

    Zie [Snel een toepassing maken](../core/quick-deploy-iot-central.md)voor meer informatie over mappen en abonnementen.

1. Selecteer **Maken** onderaan de pagina.

    ![Azure IoT Central Nieuwe toepassingspagina](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Pagina Informatie over centrale facturering azure IoT](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

U hebt nu een app voor het monitoren van waterverbruik gemaakt met behulp van de azure IoT Central-sjabloon voor het bewaken van waterverbruik.

De toepassing voor het monitoren van waterverbruik wordt geleverd met vooraf geconfigureerd:

* Voorbeeld van operatordashboards.
* Voorbeeld vooraf gedefinieerde waterstroom- en klepapparaatsjablonen.
* Gesimuleerde waterstroom en slimme klepapparaten.
* Regels en banen.
* Voorbeeld branding met behulp van white labeling.

Het is uw toepassing, en u het op elk gewenst moment wijzigen. Laten we nu de toepassing verkennen en enkele aanpassingen maken.

## <a name="explore-and-customize-the-operator-dashboard"></a>Het bedieningsdashboard verkennen en aanpassen

Nadat u de toepassing hebt gemaakt, wordt het **dashboard voor het waterverbruik van** de hele wereld geopend.

   ![Dashboard voor waterverbruiksmonitoring](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Als bouwer u weergaven op het dashboard voor operators maken en aanpassen. Laten we het dashboard verkennen voordat u het probeert aan te passen.

> [!NOTE]
> Alle gegevens die op het dashboard worden weergegeven, zijn gebaseerd op gesimuleerde apparaatgegevens, die we in de volgende sectie zullen onderzoeken.
  
Het dashboard bestaat uit verschillende soorten tegels:

* **Wide World Water Utility afbeelding tegel**: De eerste tegel in het dashboard is een afbeelding tegel van de fictieve water utility Wide World Water. U de tegel aanpassen door uw eigen afbeelding in te voegen of te verwijderen.
* **Gemiddelde waterstroom KPI-tegel**: De KPI-tegel is geconfigureerd om als voorbeeld *het gemiddelde in de laatste 30 minuten*weer te geven. U de KPI-tegel aanpassen en deze instellen op een ander type en tijdsbereik.
* **Apparaat opdracht tegels**: Deze tegels omvatten de **Sluitklep,** **Open klep,** en **Stel klep positie** tegels. Als u de opdrachten selecteert, gaat u naar de opdrachtpagina voor gesimuleerde apparaten. In Azure IoT Central is een *opdracht* een *type apparaatmogelijkheid.* We zullen dit concept later verkennen in het gedeelte 'Apparaatsjabloon' van deze zelfstudie.
* **Kaart voor waterdistributiegebied**: de kaart maakt gebruik van Azure Maps, die u rechtstreeks configureren in Azure IoT Central. De kaarttegel geeft de locatie van het apparaat weer. Plaats de plaats boven de kaart en probeer de besturingselementen over de kaart, zoals *inzoomen,* *uitzoomen*of *uitvouwen.*

    ![Dashboardkaart voor het bewaken van het waterverbruik](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Gemiddelde waterstroomlijndiagram** en **omgevingslijndiagram:** U een of meerdere apparaattelemetrieën visualiseren die zijn uitgezet als een lijndiagram over een gewenst tijdsbereik.
* **Gemiddelde klepdrukheatmapdiagram**: U het type heatmap-visualisatiekiezen kiezen dat u wilt zien verdeeld over een tijdsbereik met een kleurindex.
* **Waarschuwingsdrempelwaarden instellen:** u inhoudstegels voor actie toevoegen en een koppeling naar een actiepagina insluiten. In dit geval brengt de drempel voor resetwaarschuwingen u naar de toepassing **Jobs,** waar u updates uitvoeren voor apparaateigenschappen. We verkennen deze optie later in het gedeelte 'Taken configureren' van deze zelfstudie.
* **Eigenschaptegels**: in het dashboard worden **operationele informatie**van Valve weergegeven, **drempelwaarden voor stroomwaarschuwingen**en **onderhoudsinfotegels.**

### <a name="customize-the-dashboard"></a>Het dashboard aanpassen

Als bouwer u weergaven in het dashboard aanpassen voor operators.

1. Selecteer **Bewerken** om het **dashboard voor waterverbruik in**de wijde wereld aan te passen. U het dashboard aanpassen door het menu **Bewerken te** selecteren. Nadat het dashboard in de **bewerkingsmodus** is ingeschakeld, u nieuwe tegels toevoegen of configureren.

     ![Dashboard instellen](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Selecteer **+ Nieuw** om een nieuw dashboard te maken en het helemaal opnieuw te configureren. U meerdere dashboards hebben en u tussen uw dashboards bewegen in het dashboardmenu.

## <a name="explore-the-device-template"></a>De apparaatsjabloon verkennen

Een apparaatsjabloon in Azure IoT Central definieert de mogelijkheden van een apparaat, dat telemetrie, eigenschap of opdracht kan zijn. Als bouwer u een of meer apparaatsjablonen definiëren in Azure IoT Central die de mogelijkheden vertegenwoordigen van de apparaten die u verbindt.

De toepassing voor het monitoren van waterverbruik wordt geleverd met twee sjablonen voor referentieapparaten die een *stroommeter* en een *slim ventielapparaat* vertegenwoordigen.

Ga als een voorbeeld van het apparaat:

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster van uw toepassing in Azure IoT Central. In de lijst **Apparaatsjablonen** ziet u twee apparaatsjablonen, **Smart Valve** en **Flow-meter.**

   ![Apparaatsjabloon](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Selecteer de sjabloon **voor het stroommeterapparaat** en maak kennis met de apparaatmogelijkheden.

     ![Apparaatsjabloonstroommeter](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>De apparaatsjabloon aanpassen

Ga als u de apparaatsjabloon aanpassen:

1. Ga naar **Aanpassen** in het menu **Apparaatsjablonen.**
1. Zoek `Temperature` het telemetrietype.
1. De **weergavenaam** `Temperature` bijwerken `Reported temperature`naar .
1. Werk de maateenheid bij of stel de **Min-waarde** en **de maximumwaarde**in.
1. Selecteer **Opslaan** om eventuele wijzigingen op te slaan.

### <a name="add-a-cloud-property"></a>Een cloudeigenschap toevoegen

1. Ga naar **Cloudeigenschappen** in het menu **Apparaatsjablonen.**
1. Voeg een nieuwe cloudeigenschap toe door **Cloudeigenschap + Toevoegen te**selecteren.
    In Azure IoT Central u een eigenschap toevoegen die relevant is voor het apparaat. Een cloudproperty kan bijvoorbeeld een waarschuwingsdrempel zijn die specifiek is voor een installatiegebied, asset-informatie of andere onderhoudsgegevens.
1. Selecteer **Opslaan** om eventuele wijzigingen op te slaan.

### <a name="views"></a>Weergaven

De sjabloon voor het waterverbruiksmonitorapparaat wordt geleverd met vooraf gedefinieerde weergaven. Bekijk de weergaven en je updates uitvoeren. De weergaven definiëren hoe operators de apparaatgegevens zien, maar ook cloud-eigenschappen invoeren.

  ![Weergave van apparaatsjabloon's](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publiceren

Als u wijzigingen hebt aangebracht, moet u de apparaatsjabloon **publiceren.**

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken

Selecteer **+ Nieuw** om een nieuwe apparaatsjabloon te maken en volg het creatieproces.
U helemaal opnieuw een aangepaste apparaatsjabloon maken of u een apparaatsjabloon kiezen in de Azure-apparaatcatalogus.

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

In Azure IoT Central u gesimuleerde apparaten maken om uw apparaatsjabloon en -toepassing te testen. De toepassing voor het monitoren van waterverbruik heeft twee gesimuleerde apparaten toegewezen aan de **stroommeter-** en **Smart Valve-apparaatsjablonen.**

### <a name="view-the-devices"></a>Bekijk de apparaten

1. Selecteer **Apparaten** > **Alle apparaten** in het linkerdeelvenster.

   ![Deelvenster Alle apparaten](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Selecteer **Smart Valve 1**.

    ![Slimme klep 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Op het tabblad **Opdrachten** ziet u de drie apparaatopdrachten **(Sluitklep,** **Open klep**en **Kleppositie instellen)** die zijn gedefinieerd in de sjabloon **Smart Valve-apparaat.**

1. Ga naar het tabblad **Apparaateigenschappen** en het tabblad **Apparaatdashboard.**

> [!NOTE]
> Houd er rekening mee dat alle tabbladen zijn geconfigureerd vanuit de weergave van de apparaatsjabloon.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

Voeg nieuwe apparaten toe door **+ Nieuw** te selecteren op het tabblad **Apparaten.**

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In Azure IoT Central u regels maken om apparaattelemetrie automatisch te controleren en acties te activeren wanneer aan een of meer voorwaarden is voldaan. De acties kunnen bestaan uit het verzenden van e-mailmeldingen of het activeren van een Microsoft Power Automate-actie of een webhook-actie om gegevens naar andere services te verzenden.

De toepassing voor het monitoren van waterverbruik die u hebt gemaakt, heeft drie vooraf geconfigureerde regels.

### <a name="view-rules"></a>Regels weergeven

1. Selecteer **Regels** in het linkerdeelvenster.

   ![Deelvenster Regels](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Selecteer **Hoge pH-waarschuwing**, een van de vooraf geconfigureerde regels in de toepassing.

     ![Hoge pH-waarschuwing](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    De `High flow alert` regel is geconfigureerd om `Acidity (pH)` te `greater than` `Max flow threshold`controleren op basis van de voorwaarde is de . Maximale stroomdrempel is een cloudeigenschap die is gedefinieerd in de sjabloon **Smart Valve-apparaat.** De waarde `Max flow threshold` van de installatie per apparaatinstantie wordt ingesteld.

Laten we nu een e-mailactie maken.

Ga als u een actie aan de regel toevoeg:

1. Selecteer **+ E-mail**.
1. Voer **een hoge pH-waarschuwing in** als de vriendelijke **weergavenaam** voor de actie.
1. Voer het e-mailadres in dat is gekoppeld aan uw Azure IoT Central-account in **Aan**.
1. Voer eventueel een notitie in die u wilt opnemen in de tekst van de e-mail.
1. Selecteer **Gereed** om de actie te voltooien.
1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren.

Binnen een paar minuten ontvangt u een e-mail nadat aan de geconfigureerde voorwaarde is voldaan.

> [!NOTE]
> De toepassing stuurt een e-mail elke keer dat aan een voorwaarde wordt voldaan. Selecteer **Uitschakelen** om de regel uit te schakelen om geen e-mail meer te ontvangen via de geautomatiseerde regel.
  
Ga als lid van het volgende over een nieuwe regel:

* Selecteer **+ Nieuw** op het tabblad **Regels** in het linkerdeelvenster.

## <a name="configure-jobs"></a>Taken configureren

Met Azure IoT Central u met taken updates voor apparaat- of cloudeigendom activeren op meerdere apparaten. Naast eigenschappen u ook taken gebruiken om apparaatopdrachten op meerdere apparaten te activeren. Azure IoT Central automatiseert de workflow voor u.

1. Selecteer **Vacatures** in het linkerdeelvenster.
1. Selecteer **+ Nieuw**en configureer een of meer taken.

## <a name="customize-your-application"></a>Uw toepassing aanpassen

Als bouwer u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen.

1. Selecteer **Beheer** > **Uw toepassing aanpassen**.
1. Als u een afbeelding wilt kiezen die u wilt uploaden als **het toepassingslogo,** selecteert u de knop **Wijzigen.**
1. Als u een **browserafbeelding** wilt kiezen die wordt weergegeven op browsertabbladen, selecteert u de knop **Wijzigen.**
1. U ook de **standaardbrowserkleuren** vervangen door HTML-hexadecimale kleurcodes toe te voegen.

   ![Selecties voor toepassingslogo, browserpictogram en browserkleuren](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. U toepassingsafbeeldingen ook wijzigen door **instellingen voor beheertoepassing** > **te**selecteren. Als u een afbeelding wilt kiezen die u wilt uploaden als de toepassingsafbeelding, selecteert u de knop **Afbeelding selecteren.**
1. Ten slotte u **het thema** ook wijzigen door het pictogram **Instellingen** in de rechterbovenhoek van de toepassing te selecteren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet blijft gebruiken, verwijdert u deze.

1. Selecteer **Beheer** in het linkerdeelvenster van uw Azure IoT Central-toepassing.
1. Selecteer **Toepassingsinstellingen**en selecteer **Verwijderen** onder aan de pagina.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [concepten voor het monitoren van waterverbruik](./concepts-waterconsumptionmonitoring-architecture.md).
