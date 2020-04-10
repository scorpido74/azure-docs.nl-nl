---
title: Zelfstudie - Een analysetoepassing in de winkel maken in Azure IoT Central
description: In deze zelfstudie ziet u hoe u een in-store analytics-retailtoepassing maakt in IoT Central. U maakt het, past het aan en voegt sensorapparaten toe.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 50dd6038a8642f13cea7840fff723a5cf12ce2dd
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000250"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Zelfstudie: Een analysetoepassing in de winkel maken in Azure IoT Central



De zelfstudie toont oplossingsbouwers hoe u een Azure IoT Central-analysetoepassing maken. De voorbeeldtoepassing is voor een winkel. Het is een oplossing voor de gemeenschappelijke zakelijke behoefte om te controleren en aan te passen aan de bezettingsgraad en omgevingsomstandigheden.

De voorbeeldtoepassing die u bouwt, bevat drie echte apparaten: een Rigado Cascade 500-gateway en twee RuuviTag-sensoren. De zelfstudie laat ook zien hoe u de gesimuleerde bezettingssensor in de toepassingssjabloon gebruiken voor testdoeleinden. De Rigado C500 gateway fungeert als de communicatiehub in uw toepassing. Het communiceert met sensoren in uw winkel en beheert hun verbindingen met de cloud. De RuuviTag is een omgevingssensor die telemetrie biedt, inclusief temperatuur, vochtigheid en druk. De gesimuleerde bezettingssensor biedt een manier om beweging en aanwezigheid in de afrekengebieden van een winkel te volgen. 

Deze zelfstudie bevat aanwijzingen voor het aansluiten van de Rigado- en RuuviTag-apparaten op uw toepassing. Als u een andere gateway en sensoren hebt, u nog steeds de stappen volgen om uw toepassing te bouwen. De tutorial laat ook zien hoe je gesimuleerde RuuviTag-sensoren maakt. Met de gesimuleerde sensoren u de toepassing bouwen als u geen echte apparaten hebt. 

Je ontwikkelt de kassa- en conditiebewaking oplossing in drie delen:

* De toepassing maken en apparaten verbinden om de omstandigheden te bewaken
* Het dashboard aanpassen om operators in staat te stellen apparaten te bewaken en te beheren
* Configureer het exporteren van gegevens om storemanagers in staat te stellen analyses uit te voeren en inzichten te visualiseren

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De Azure IoT Central **In-store analytics - checkout** template gebruiken om een winkeltoepassing te maken
> * De toepassingsinstellingen aanpassen
> * IoT-apparaatsjablonen maken en aanpassen
> * Apparaten verbinden met uw toepassing
> * Regels en acties toevoegen om de omstandigheden te controleren

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudiereeks te voltooien, moet je het beste zijn:
* Een Azure-abonnement wordt aanbevolen. U optioneel een gratis proefperiode van 7 dagen gebruiken. Als u geen Azure-abonnement hebt, u er een maken op de [aanmeldingspagina van Azure.](https://aka.ms/createazuresubscription)
* Toegang tot een gateway-apparaat en twee omgevingssensoren (u optioneel gesimuleerde apparaten gebruiken zoals beschreven in de zelfstudie)
* Apparaatsjablonen voor de apparaten die u gebruikt (er worden sjablonen verstrekt voor alle apparaten die in de zelfstudie worden gebruikt)

## <a name="create-an-application"></a>Een app maken
In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de hele zelfstudiereeks om een complete oplossing te bouwen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Navigeer naar de website [azure IoT Central-toepassingsbeheer.](https://aka.ms/iotcentral)

1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen, anders meldt u zich aan met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

1. Selecteer **Detailhandel**.  Op de retailpagina worden verschillende retailtoepassingssjablonen weergegeven.

Ga als lid van het volgende over een nieuwe checkout-toepassing voor analyse in de winkel:  

1. Selecteer de **sjabloon In-store analytics - checkout** application. Deze sjabloon bevat apparaatsjablonen voor alle apparaten die in de zelfstudie worden gebruikt, behalve ruuvitag-sensoren. De sjabloon biedt ook een operatordashboard voor het bewaken van de afhandeling en de omgevingsomstandigheden en de status van het apparaat. 

1. Kies eventueel een vriendelijke **toepassingsnaam.** Deze applicatie is gebaseerd op een fictieve winkel genaamd Contoso. De zelfstudie maakt gebruik van de **aanvraagnaam** *Contoso checkout*. De toepassingssjabloon is gebaseerd op het fictieve bedrijf Northwind. In deze zelfstudie gebruikt u Contoso om te leren hoe u de toepassing aanpassen.

    > [!NOTE]
    > Als u een vriendelijke **toepassingsnaam gebruikt,** moet u nog steeds een unieke waarde gebruiken voor de **URL**van de toepassing.

1. Als u een Azure-abonnement hebt, voert u uw *directory, Azure-abonnement en Regio in.* Als u geen abonnement hebt, u **een gratis proefperiode van 7 dagen** inschakelen en de vereiste contactgegevens invullen.  

    Zie de [snelstart over het maken van een toepassing](../core/quick-deploy-iot-central.md)voor meer informatie over directory's en abonnementen.

1. Selecteer **Maken**.

    ![Pagina Toepassing maken van Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Factureringsgegevens van Azure IoT Central Create Application](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Toepassingsinstellingen aanpassen

Als bouwer u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen. In deze sectie selecteert u een vooraf gedefinieerd toepassingsthema. Optioneel leert u hoe u een aangepast thema maakt en de toepassingsafbeelding bijwerkt. Met een aangepast thema u de kleuren van de toepassingsbrowser, het browserpictogram en het toepassingslogo instellen dat in de mastkop wordt weergegeven.

Ga als lid van het woord om een vooraf gedefinieerd toepassingsthema te selecteren:

1. Selecteer **Instellingen** op de mastkop.

    ![Azure IoT Central-toepassingsinstellingen](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Selecteer een nieuw **thema**.

3. Selecteer **Opslaan**.

In plaats van een vooraf gedefinieerd thema te gebruiken, u een aangepast thema maken. Als u een set voorbeeldafbeeldingen wilt gebruiken om de toepassing aan te passen en de zelfstudie te voltooien, downloadt u de Voorbeeldafbeeldingen van [Contoso.](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)

Ga als lid van het andere om een aangepast thema te maken:

1. Vouw het linkerdeelvenster uit, zo niet al uitgebreid.

    ![Linkerdeelvenster azure IoT Centraal](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Selecteer **Beheer > Uw toepassing aanpassen**.

1. Gebruik de knop **Wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **toepassingslogo.** Geef eventueel een waarde op voor **alternatieve tekst van het logo**. 

1. Gebruik de knop **Wijzigen** om een **browserafbeelding** te kiezen die wordt weergegeven op browsertabbladen.

1. Vervang de **standaardbrowserkleuren** optioneel door HTML-hexadecimale kleurcodes toe te voegen. Voeg voor de **koptekst** *#008575*toe.  Voeg voor de **Accent** *#A1F3EA*toe. 

1. Selecteer **Opslaan**. 

    ![Aangepast azure IoT Central-logo](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Nadat u deze hebt opgeslagen, werkt de toepassing de browserkleuren, het logo in de mastkop en het browserpictogram bij. 

    ![Azure IoT Central heeft de bijgewerkte toepassingsinstellingen](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Ga als u de toepassingsafbeelding bij:

1. Selecteer **Instellingen voor beheer > toepassing**.

1. Gebruik de knop **Afbeelding selecteren** om een afbeelding te kiezen die u wilt uploaden als de toepassingsafbeelding. Deze afbeelding wordt weergegeven op de toepassingstegel op de pagina **Mijn apps** van de Toepassingsbeheer Van IoT Centraal.

1. Selecteer **Opslaan**.

1. Navigeer eventueel naar de weergave **Mijn apps** op de website van [Azure IoT Central-toepassingsbeheer.](https://aka.ms/iotcentral) In de toepassingstegel wordt de bijgewerkte toepassingsafbeelding weergegeven.

    ![Azure IoT Central pas toepassingsafbeelding aan](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Apparaatsjablonen maken
Als bouwer u apparaatsjablonen maken waarmee u en de toepassingsoperatoren apparaten kunnen configureren en beheren. U maakt een sjabloon door een aangepaste sjabloon te maken, door een bestaand sjabloonbestand te importeren of door een sjabloon te importeren uit de Azure IoT-apparaatcatalogus. Nadat u een apparaatsjabloon hebt gemaakt en aangepast, gebruikt u deze om echte apparaten aan uw toepassing te verbinden. Gebruik optioneel een apparaatsjabloon om gesimuleerde apparaten te genereren om te testen.

De **in-store analytics - checkout** application template heeft apparaatsjablonen voor verschillende apparaten.  Er zijn apparaatsjablonen voor twee van de drie apparaten die u in de toepassing gebruikt. De ruuviTag-apparaatsjabloon is niet opgenomen in de sjabloon **Voor het beheren van de winkel.** In deze sectie voegt u een apparaatsjabloon voor RuuviTag-sensoren toe aan uw toepassing.

Ga als lid van de knop om een ruuvitag-apparaatsjabloon aan uw toepassing toe te voegen:

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster.

1. Selecteer **+ Nieuw** om een nieuwe apparaatsjabloon te maken.

1. Zoek en selecteer de **ruuvitagsensorapparaatsjabloon** in de Azure IoT-apparaatcatalogus. 

1. Selecteer **Volgende: Aanpassen**.

    ![Azure IoT Central RuuviTag-sensorapparaatsjabloon](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Selecteer **Maken**. De toepassing voegt de RuuviTag-apparaatsjabloon toe.

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster. Op de pagina worden alle apparaatsjablonen weergegeven die zijn opgenomen in de toepassingssjabloon en de ruuvitag-apparaatsjabloon die u zojuist hebt toegevoegd.

    ![Azure IoT Central RuuviTag-sensorapparaatsjabloon](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Apparaatsjablonen aanpassen
U de apparaatsjablonen in uw toepassing op drie manieren aanpassen. Eerst past u de native ingebouwde interfaces in uw apparaten aan door de apparaatmogelijkheden te wijzigen. Met een temperatuursensor u bijvoorbeeld details wijzigen, zoals de weergavenaam van de temperatuurinterface, het gegevenstype, de meeteenheden en minimale en maximale werkbereiken. 

Ten tweede u uw apparaatsjablonen aanpassen door cloudeigenschappen toe te voegen. Cloud-eigenschappen maken geen deel uit van de ingebouwde apparaatmogelijkheden. Cloudeigenschappen zijn aangepaste gegevens die uw Azure IoT Central-toepassing maakt, opslaat en koppelt aan uw apparaten. Een voorbeeld van een cloudeigenschap kan een berekende waarde zijn, of metagegevens, zoals een locatie die u wilt koppelen aan een set apparaten. 

Ten derde u apparaatsjablonen aanpassen door aangepaste weergaven te maken. Weergaven bieden operators een manier om telemetrie en metagegevens voor uw apparaten te visualiseren, zoals apparaatstatistieken en status.

Hier gebruikt u de eerste twee methoden om de apparaatsjabloon voor uw RuuviTag-sensoren aan te passen. Zie snel aan de [slag](../core/quick-create-simulated-device.md) voor informatie over het maken van weergaven voor uw sensoren.

Ga als bedoeld als het gaat om de ingebouwde interfaces van de ruuvitag-apparaatsjabloon aan te passen:

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster. 

1. Selecteer de sjabloon voor RuuviTag-sensoren. 

1. Verberg het linkerdeelvenster. In de overzichtsweergave van de sjabloon worden de apparaatmogelijkheden weergegeven.

    ![Overzichtsweergave azure IoT Central RuuviTag-apparaatsjabloon](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Selecteer **Aanpassen** in het menu ruuviTag-apparaatsjabloon. 

1. Schuif in de lijst met `humidity` mogelijkheden en zoek het telemetrietype. Het is het rijitem met de bewerkbare **weergavenaamwaarde** van *vochtigheid.*

In de volgende stappen `humidity` past u het telemetrietype aan voor de RuuviTag-sensoren. Optioneel u enkele andere telemetrietypen aanpassen.

Breng `humidity` voor het telemetrietype de volgende wijzigingen aan:

1. Selecteer het **besturingselement Uitvouwen** om de schemadetails voor de rij uit te vouwen.

1. Werk de **waarde Weergavenaam** bij van *vochtigheid* naar een aangepaste waarde, zoals *Relatieve vochtigheid*.

1. Wijzig de optie **Semantisch type** van *Geen* naar *Vochtigheid*.  Stel destijds schemawaarden in voor het type vochtigheidstelemetrie in de uitgebreide schemaweergave. Met schema-instellingen u gedetailleerde validatievereisten maken voor de gegevens die uw sensoren bijhouden. U bijvoorbeeld minimum- en maximale bedrijfsbereikwaarden instellen voor een bepaalde interface.

1. Selecteer **Opslaan** om uw wijzigingen op te slaan.

    ![Azure IoT Central RuuviTag-apparaatsjabloon aanpassing](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Ga als lid van de cloud over op een apparaatsjabloon in uw toepassing:

1. Selecteer **Cloud-eigenschappen** in het menu RuuviTag-apparaatsjabloon.

1. Selecteer **Cloudeigenschap toevoegen**. 

Geef de volgende waarden op om een aangepaste eigenschap te maken om de locatie van elk apparaat op te slaan:

1. Voer de *waardelocatie* voor de **weergavenaam in**. Deze waarde wordt automatisch gekopieerd naar het veld **Naam,** een vriendelijke naam voor de eigenschap. U de gekopieerde waarde gebruiken of wijzigen.

1. Selecteer *Tekenreeks* in de vervolgkeuzelijst **Schema.** Met een tekenreekstype u een locatienaamtekenreeks koppelen aan elk apparaat op basis van de sjabloon. U bijvoorbeeld een gebied in een winkel aan elk apparaat koppelen. Optioneel u het **semantische type** van uw eigenschap instellen op *Locatie*en hiermee wordt het **schema** automatisch ingesteld op *Geopoint.* Hiermee u GPS-coördinaten koppelen aan een apparaat. 

1. **Minimumlengte instellen** op *2*. 

1. **Witruimte bijsnijden** instellen op **aan**.

1. Selecteer **Opslaan** om uw aangepaste cloudeigenschap op te slaan.

    ![Azure IoT Central RuuviTag-apparaatsjabloon aanpassing](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Selecteer **Publiceren**. 

    Als u een apparaatsjabloon publiceert, is deze zichtbaar voor toepassingsoperatoren. Nadat u een sjabloon hebt gepubliceerd, gebruikt u deze om gesimuleerde apparaten te genereren voor het testen of om echte apparaten aan uw toepassing te verbinden. Als u al apparaten hebt die zijn aangesloten op uw toepassing, worden de wijzigingen in de apparaten in de gewenste wijziging doorgevoerd.

## <a name="add-devices"></a>Apparaten toevoegen
Nadat u apparaatsjablonen hebt gemaakt en aangepast, is het tijd om apparaten toe te voegen. 

Voor deze zelfstudie gebruikt u de volgende set echte en gesimuleerde apparaten om de toepassing te bouwen:
- Een echte Rigado C500 gateway
- Twee echte RuuviTag sensoren
- Een gesimuleerde **bezettingssensor.** De gesimuleerde sensor is opgenomen in de toepassingssjabloon, zodat u deze niet hoeft te maken. 

> [!NOTE]
> Als u geen echte apparaten hebt, u deze zelfstudie nog steeds voltooien door gesimuleerde RuuviTag-sensoren te maken. De volgende aanwijzingen bevatten stappen om een gesimuleerde RuuviTag te maken. U hoeft geen gesimuleerde gateway te maken.

Voltooi de stappen in de volgende twee artikelen om een echte Rigado-gateway en RuuviTag-sensoren aan te sluiten. Nadat u klaar bent, gaat u terug naar deze zelfstudie. Omdat u in deze zelfstudie al apparaatsjablonen hebt gemaakt, hoeft u deze niet opnieuw te maken in de volgende reeks aanwijzingen.

- Zie [Een Rigado Cascade 500 aansluiten op uw Azure IoT Central-toepassing](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)als u een Rigado-gateway wilt aansluiten.
- Zie [RuuviTag-sensor aansluiten op uw Azure IoT Central-toepassing om](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)RuuviTag-sensoren aan te sluiten. U deze aanwijzingen ook gebruiken om indien nodig twee gesimuleerde sensoren te maken.

## <a name="add-rules-and-actions"></a>Regels en acties toevoegen
Als onderdeel van het gebruik van sensoren in uw Azure IoT Central-toepassing om de omstandigheden te controleren, u regels maken om acties uit te voeren wanneer aan bepaalde voorwaarden is voldaan. Een regel is gekoppeld aan een apparaatsjabloon en een of meer apparaten en bevat voorwaarden waaraan moet worden voldaan op basis van apparaattelemetrie of gebeurtenissen. Een regel bevat ook een of meer bijbehorende acties. De acties kunnen bestaan uit het verzenden van e-mailmeldingen of het activeren van een webhook-actie om gegevens naar andere services te verzenden. De **sjabloon In-store analytics - checkout** application bevat een aantal vooraf gedefinieerde regels voor de apparaten in de toepassing.

In deze sectie maakt u een nieuwe regel die de maximale relatieve vochtigheidsgraad controleert op basis van de telemetrie van de RuuviTag-sensor. U voegt een actie toe aan de regel, zodat als de luchtvochtigheid het maximum overschrijdt, de toepassing e-mail verzendt. 

Ga als lid van het werk om een regel te maken: 

1. Vouw het linkerdeelvenster uit.

1. Selecteer **Regels**.

1. Selecteer **+ Nieuw**.

1. Voer *vochtigheidsniveau in* als de naam van de regel. 

1. Kies de ruuvitag-apparaatsjabloon in **Scopes**. De regel die u definieert, is van toepassing op alle sensoren op basis van die sjabloon. Optioneel u een filter maken dat de regel alleen toepast op een gedefinieerde subset van de sensoren. 

1. Kies `Relative humidity` als **telemetrie**. Het is de apparaatmogelijkheid die u in een vorige stap hebt aangepast.

1. Kies `Is greater than` als **operator**. 

1. Voer een typisch indoor vochtigheidsniveau voor het bovenste bereik in voor uw omgeving in als **de waarde.** Voer bijvoorbeeld *65*in . U hebt een voorwaarde gesteld voor uw regel die optreedt wanneer de relatieve vochtigheid in een RuuviTag echte of gesimuleerde sensor deze waarde overschrijdt. Het kan nodig zijn om de waarde omhoog of omlaag aan te passen, afhankelijk van het normale vochtigheidsbereik in uw omgeving.  

   ![Azure IoT Central regelvoorwaarden toevoegen](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Ga als u een actie aan de regel toevoeg:

1. Selecteer **+ E-mail**. 

1. Voer *een melding met hoge luchtvochtigheid* in als de vriendelijke **weergavenaam** voor de actie. 

1. Voer het e-mailadres in dat aan uw account is gekoppeld in **Aan**. Als u een andere e-mail gebruikt, moet het adres dat u gebruikt, zijn voor een gebruiker die aan de toepassing is toegevoegd. De gebruiker moet zich ook minstens één keer aanmelden en uitloggen.

1. Voer eventueel een notitie in die u wilt opnemen in de tekst van de e-mail.

1. Selecteer **Gereed** om de actie te voltooien.

   ![Azure IoT Central voegt acties toe aan regels](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

    Binnen een paar minuten moet het opgegeven e-mailaccount e-mails beginnen te ontvangen. De toepassing stuurt e-mail elke keer dat een sensor aangeeft dat de luchtvochtigheid hoger is dan de waarde in uw conditie.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u het volgende geleerd:

* De Azure IoT Central **In-store analytics - checkout** template gebruiken om een winkeltoepassing te maken
* De toepassingsinstellingen aanpassen
* IoT-apparaatsjablonen maken en aanpassen
* Apparaten verbinden met uw toepassing
* Regels en acties toevoegen om de omstandigheden te controleren

Nu u een Azure IoT Central-toepassing voor conditiebewaking hebt gemaakt, volgt hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Het bedieningsdashboard aanpassen](./tutorial-in-store-analytics-customize-dashboard.md)
