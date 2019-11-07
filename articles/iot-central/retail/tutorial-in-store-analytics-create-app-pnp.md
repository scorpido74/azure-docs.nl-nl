---
title: Een in-Store-Analytics-toepassing maken in azure IoT Central | Microsoft Docs
description: In deze zelf studie ziet u hoe u een retail-toepassing in-Store Analytics maakt in IoT Central. U maakt deze en past deze toe en voegt sensor apparaten toe.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 10/03/2019
ms.openlocfilehash: a662201823808c9e2e34e9210096cb081e1b9430
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615315"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Zelf studie: een in-Store Analytics-toepassing maken in azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In de zelf studie worden oplossingen bouwers weer gegeven voor het maken van een Azure IoT Central in de Store Analytics-toepassing. De voorbeeld toepassing is voor een Retail Store. Het is een oplossing voor de gemeen schappelijke bedrijfs behoefte om bezetting en omgevings omstandigheden te bewaken en aan te passen.

De voorbeeld toepassing die u bouwt, omvat drie echte apparaten: een Rigado Cascade 500-gateway en twee RuuviTag Sens oren. In de zelf studie ziet u ook hoe u de gesimuleerde bezetting sensor gebruikt die in de toepassings sjabloon is opgenomen voor test doeleinden. De Rigado C500-gateway fungeert als de communicatie hub in uw toepassing. De service communiceert met Sens oren in uw winkel en beheert de verbindingen met de Cloud. De RuuviTag is een omgevings sensor die telemetrie biedt, waaronder de Tempe ratuur, vochtigheid en druk. De gesimuleerde bezettings sensor biedt een manier om beweging en aanwezigheid bij te houden in de afhandelings gebieden van een Store. 

Deze zelf studie bevat instructies voor het verbinden van de Rigado-en RuuviTag-apparaten met uw toepassing. Als u een andere gateway en Sens oren hebt, kunt u nog steeds de stappen volgen om uw toepassing te bouwen. In de zelf studie ziet u ook hoe u gesimuleerde RuuviTag Sens oren maakt. Met de gesimuleerde Sens oren kunt u de toepassing bouwen als u geen echte apparaten hebt. 

In drie delen kunt u de oplossing voor het afhandelings-en voorwaarde bewakings systeem ontwikkelen:

* De toepassing maken en apparaten verbinden om voor waarden te controleren
* Het dash board aanpassen zodat Opera tors apparaten kunnen bewaken en beheren
* Gegevens export configureren om winkel managers in staat te stellen analyses uit te voeren en inzichten te visualiseren

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De Azure IoT Central **in-Store-analyse** sjabloon gebruiken om een Retail Store-toepassing te maken
> * De toepassings instellingen aanpassen
> * IoT-Device-sjablonen maken en aanpassen
> * Apparaten verbinden met uw toepassing
> * Regels en acties toevoegen om voor waarden te controleren

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze reeks zelf studies hebt u het volgende nodig:
* U wordt aangeraden een Azure-abonnement te nemen. U kunt eventueel een gratis proef versie van 7 dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).
* Toegang tot een gateway apparaat en twee omgevings sensors (u kunt eventueel gesimuleerde apparaten gebruiken zoals beschreven in de zelf studie)
* Device-sjablonen voor de apparaten die u gebruikt (er worden sjablonen gegeven voor alle apparaten die in de zelf studie worden gebruikt)

## <a name="create-an-application"></a>Een app maken
In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de reeks zelf studies om een volledige oplossing te bouwen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Ga naar de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) .

1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om het te openen. u kunt zich ook aanmelden met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

1. Selecteer **detail handel**.  Op de pagina Retail worden verschillende sjablonen voor de retail toepassing weer gegeven.

Voor het maken van een nieuwe toepassing in de Store Analytics-uitchecking die gebruikmaakt van preview-functies:  

1. Selecteer de toepassings sjabloon **in de Store Analytics-uitchecken** . Deze sjabloon bevat sjablonen voor alle apparaten die in de zelf studie worden gebruikt, met uitzonde ring van RuuviTag Sens oren. De sjabloon biedt ook een operator dashboard voor het controleren van de afhandelings-en omgevings voorwaarden en de Apparaatstatus. 

1. U kunt desgewenst een beschrijvende **toepassings naam**kiezen. Deze toepassing is gebaseerd op een fictieve Retail Store met de naam contoso. In deze zelf studie wordt de naam van de **toepassing** *Contoso uitgecheckt*. De toepassings sjabloon is gebaseerd op het fictieve bedrijf North Wind. In deze zelf studie gebruikt u contoso voor meer informatie over het aanpassen van de toepassing.

    > [!NOTE]
    > Als u een beschrijvende **toepassings naam**gebruikt, moet u nog steeds een unieke waarde voor de **URL**van de toepassing gebruiken.

1. Als u een Azure-abonnement hebt, voert u uw *adres lijst, het Azure-abonnement en de regio*in. Als u geen abonnement hebt, kunt u een **gratis proef versie van 7 dagen** inschakelen en de vereiste contact gegevens volt ooien.  

    Zie de [snelstart over het maken van een toepassing](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)voor meer informatie over directory's en abonnementen.

1. Selecteer **Maken**.

    ![Pagina Toepassing maken van Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template.png)

## <a name="customize-application-settings"></a>Toepassings instellingen aanpassen
Als ontwerper kunt u verschillende instellingen wijzigen om de gebruikers ervaring in uw toepassing aan te passen. In deze sectie selecteert u een vooraf gedefinieerd toepassings thema. U leert ook hoe u een aangepast thema maakt en hoe u de installatie kopie van de toepassing bijwerkt. Met een aangepast thema kunt u de kleuren van de toepassings browser, het browser pictogram en het toepassings logo dat wordt weer gegeven in het Impressum instellen.

Een vooraf gedefinieerd toepassings thema selecteren:

1. Selecteer **instellingen** in het Impressum.

    ![Toepassings instellingen van Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/settings-icon.png)

2. Selecteer een nieuw **thema**.

3. Selecteer **Opslaan**.

In plaats van een vooraf gedefinieerd thema te gebruiken, kunt u een aangepast thema maken. Als u een aantal voorbeeld afbeeldingen wilt gebruiken om de toepassing aan te passen en de zelf studie te volt ooien, downloadt u de voor [beeld-installatie kopieën van Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Een aangepast thema maken:

1. Vouw het linkerdeel venster uit als dat nog niet is gebeurd.

    ![Linkerdeel venster van Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/dashboard-expand.png)

1. Selecteer **beheer > uw toepassing**aan te passen.

1. Gebruik de knop **wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **toepassings logo**. Geef eventueel een waarde op voor de **ALT-tekst**van het logo. 

1. Gebruik de knop **wijzigen** om een **browser pictogram** afbeelding te kiezen die wordt weer gegeven op de tabbladen van de browser.

1. U kunt eventueel de standaard **browser kleuren** vervangen door HTML-hexadecimale kleur codes toe te voegen. Voeg *#008575*toe voor de **koptekst**.  Voeg *#A1F3EA*toe voor het **accent**. 

1. Selecteer **Opslaan**. 

    ![Logo van Azure IoT Central aangepast](./media/tutorial-in-store-analytics-create-app-pnp/select-application-logo.png)

    Nadat u de app hebt opgeslagen, worden de browser kleuren, het logo in het Impressum en het browser pictogram bijgewerkt. 

    ![Toepassings instellingen van Azure IoT Central bijgewerkt](./media/tutorial-in-store-analytics-create-app-pnp/saved-application-settings.png)

De installatie kopie van de toepassing bijwerken:

1. Selecteer **beheer > toepassings instellingen**.

1. Gebruik de knop **afbeelding selecteren** om een afbeelding te kiezen die u als de installatie kopie van de toepassing wilt uploaden. Deze afbeelding wordt weer gegeven op de tegel toepassing op de pagina **mijn apps** van de IOT Central toepassings beheer.

1. Selecteer **Opslaan**.

1. Ga eventueel naar de weer gave **mijn apps** op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) . Op de tegel toepassing wordt de bijgewerkte installatie kopie van de toepassing weer gegeven.

    ![Installatie kopie van de toepassing Azure IoT Central aanpassen](./media/tutorial-in-store-analytics-create-app-pnp/customize-application-image.png)

## <a name="create-device-templates"></a>Device-sjablonen maken
Als ontwerper kunt u sjablonen voor apparaten maken waarmee u en de toepassings operators apparaten kunnen configureren en beheren. Als u een sjabloon maakt door een aangepaste sjabloon te maken, kunt u een sjabloon bestand importeren of door in de Azure IoT-apparaatprofiel een-sjablonen te importeren. Nadat u een sjabloon hebt gemaakt en aangepast, kunt u deze gebruiken om echte apparaten te verbinden met uw toepassing. Gebruik eventueel een apparaatprofiel om gesimuleerde apparaten te genereren voor het testen.

De toepassings sjabloon **in de Store analyse-uitchecken** bevat Device-sjablonen voor verschillende apparaten.  Er zijn sjablonen voor twee van de drie apparaten die u in de toepassing gebruikt. Het RuuviTag is niet opgenomen in de toepassings sjabloon **in-Store Analytics-Checker** . In deze sectie voegt u een sjabloon voor RuuviTag Sens oren toe aan uw toepassing.

Een sjabloon voor RuuviTag-apparaten toevoegen aan uw toepassing:

1. Selecteer **Apparaatbeheer** in het linkerdeel venster.

1. Selecteer **+ Nieuw** om een nieuwe sjabloon voor het apparaat te maken.

1. Zoek en selecteer de sjabloon **RuuviTag** sensor device in de Azure IOT-apparaatprofiel. 

1. Selecteer **volgende: aanpassen**.

    ![Sjabloon voor Azure IoT Central-sensor RuuviTag](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template.png)

1. Selecteer **Maken**. De toepassing voegt de RuuviTag-apparaatprofiel toe.

1. Selecteer **Apparaatbeheer** in het linkerdeel venster. De pagina bevat alle device-sjablonen die zijn opgenomen in de toepassings sjabloon en de RuuviTag die u zojuist hebt toegevoegd.

    ![Sjabloon voor Azure IoT Central-sensor RuuviTag](./media/tutorial-in-store-analytics-create-app-pnp/device-templates-list.png)

## <a name="customize-device-templates"></a>Apparaatinstellingen aanpassen
U kunt op drie manieren de sjablonen voor apparaten in uw toepassing aanpassen. Eerst past u de ingebouwde interface van het systeem aan op uw apparaten door de mogelijkheden van het apparaat te wijzigen. Met een temperatuur sensor kunt u bijvoorbeeld details wijzigen, zoals de weergave naam van de temperatuur interface, het gegevens type, de maat eenheden en de minimale en maximale hoeveelheid werk reeksen. 

Ten tweede kunt u uw Apparaatinstellingen aanpassen door Cloud eigenschappen toe te voegen. Cloud eigenschappen maken geen deel uit van de ingebouwde mogelijkheden van het apparaat. Cloud eigenschappen zijn aangepaste gegevens die door uw Azure IoT Central-toepassing worden gemaakt, opgeslagen en gekoppeld aan uw apparaten. Een voor beeld van een Cloud eigenschap kan een berekende waarde zijn, of meta gegevens zoals een locatie die u wilt koppelen aan een set apparaten. 

Ten derde kunt u ook de apparaatinstellingen aanpassen door aangepaste weer gaven te maken. Weer gaven bieden Opera tors voor het visualiseren van telemetriegegevens en meta gegevens voor uw apparaten, zoals metrische gegevens over apparaten en status.

Hier kunt u de eerste twee methoden gebruiken om de Device-sjabloon voor uw RuuviTag-Sens oren aan te passen. Meer informatie over het maken van weer gaven voor uw Sens oren vindt [u in de Snelstartgids een gesimuleerd apparaat toevoegen aan de IOT Central-toepassing](../core/quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

De ingebouwde interfaces van de sjabloon voor het RuuviTag-apparaat aanpassen:

1. Selecteer **Apparaatbeheer** in het linkerdeel venster. 

1. Selecteer de sjabloon voor RuuviTag Sens oren. 

1. Verberg het linkerdeel venster. In de weer gave samen vatting van de sjabloon worden de mogelijkheden van het apparaat weer gegeven.

    ![Weer gave samen vatting van Azure IoT Central RuuviTag-Apparaatbeheer](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-summary-view.png)

1. Selecteer **aanpassen** in het menu van de RuuviTag. 

1. Schuif in de lijst met mogelijkheden en zoek het type telemetrie van de `humidity`. Het is het rij-item met de Bewerk bare **weergave naam** waarde *vochtigheids graad*.

In de volgende stappen past u het `humidity` type telemetrie voor de RuuviTag Sens oren aan. Desgewenst kunt u enkele van de andere telemetrie-typen aanpassen.

Voor het `humidity` type telemetrie brengt u de volgende wijzigingen aan:

1. Selecteer het besturings element **uitvouwen** om de schema Details voor de rij uit te vouwen.

1. Werk de **weergave naam** waarde bij van *vochtigheid* in een aangepaste waarde, zoals *relatieve vochtigheid*.

1. Wijzig de optie **semantisch type** van *geen* in *vochtigheid*.  Stel eventueel schema waarden in voor het type telemetrie van de vochtigheid in de uitgevouwen schema weergave. Met schema-instellingen kunt u gedetailleerde validatie vereisten maken voor de gegevens die uw Sens oren volgen. U kunt bijvoorbeeld minimum-en maximum waarden voor het besturings bereik instellen voor een bepaalde interface.

1. Selecteer **Opslaan** om uw wijzigingen op te slaan.

    ![Aanpassing van Azure IoT Central RuuviTag-Apparaatbeheer](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-customize.png)

Een Cloud eigenschap toevoegen aan een sjabloon voor een apparaat in uw toepassing:

1. Selecteer **Cloud eigenschappen** in het menu van de RuuviTag.

1. Selecteer **Cloud eigenschap toevoegen**. 

Geef de volgende waarden op om een aangepaste eigenschap te maken voor het opslaan van de locatie van elk apparaat:

1. Voer de *locatie* van de waarde in voor de **weergave naam**. Deze waarde wordt automatisch gekopieerd naar het veld **naam** , een beschrijvende naam voor de eigenschap. U kunt de gekopieerde waarde gebruiken of wijzigen.

1. Selecteer *teken reeks* in de vervolg keuzelijst **schema** . Met een teken reeks type kunt u een locatie naam reeks koppelen aan elk apparaat op basis van de sjabloon. U kunt bijvoorbeeld een gebied in een archief koppelen aan elk apparaat. Desgewenst kunt u het **semantische type** van uw eigenschap instellen op *locatie*en wordt het **schema** automatisch ingesteld op *geopunt*. Zo kunt u GPS-coördinaten koppelen aan een apparaat. 

1. Stel de **minimum lengte** in op *2*. 

1. Stel **spaties** in **op aan**.

1. Selecteer **Opslaan** om uw aangepaste Cloud eigenschap op te slaan.

    ![Aanpassing van Azure IoT Central RuuviTag-Apparaatbeheer](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-cloud-property.png)

1. Selecteer **Publiceren**. 

    Als u een apparaataccount publiceert, wordt het zichtbaar voor toepassings operators. Nadat u een sjabloon hebt gepubliceerd, kunt u deze gebruiken om gesimuleerde apparaten te genereren voor het testen of om echte apparaten te verbinden met uw toepassing. Als er al apparaten zijn verbonden met uw toepassing, wordt door het publiceren van een aangepaste sjabloon de wijzigingen naar de apparaten gepusht.

## <a name="add-devices"></a>Apparaten toevoegen
Nadat u-sjablonen voor apparaten hebt gemaakt en aangepast, is het tijd om apparaten toe te voegen. 

Voor deze zelf studie gebruikt u de volgende set echte en gesimuleerde apparaten om de toepassing te bouwen:
- Een echte Rigado C500-gateway
- Twee echte RuuviTag Sens oren
- Een gesimuleerde **bezetting** sensor. De gesimuleerde sensor is opgenomen in de toepassings sjabloon, dus u hoeft deze niet te maken. 

> [!NOTE]
> Als u geen echte apparaten hebt, kunt u deze zelf studie nog steeds volt ooien door gesimuleerde RuuviTag Sens oren te maken. De volgende instructies bevatten stappen voor het maken van een gesimuleerde RuuviTag. U hoeft geen gesimuleerde gateway te maken.

Volg de stappen in de volgende twee artikelen om een echte Rigado-gateway en RuuviTag Sens oren te verbinden. Wanneer u klaar bent, keert u terug naar deze zelf studie. Omdat u al Device-sjablonen in deze zelf studie hebt gemaakt, hoeft u deze niet opnieuw te maken in de volgende reeks richtingen.

- Zie [verbinding maken met een Rigado Cascade 500 met uw Azure IOT Central-toepassing](../core/howto-connect-rigado-cascade-500-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)om verbinding te maken met een Rigado-gateway.
- Zie [een RuuviTag-sensor verbinden met uw Azure IOT Central-toepassing](../core/howto-connect-ruuvi-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)om verbinding te maken met RuuviTag Sens oren. U kunt deze instructies ook gebruiken om twee gesimuleerde Sens oren te maken, indien nodig.

## <a name="add-rules-and-actions"></a>Regels en acties toevoegen
Als onderdeel van het gebruik van Sens oren in uw Azure IoT Central-toepassing om voor waarden te bewaken, kunt u regels maken voor het uitvoeren van acties wanneer aan bepaalde voor waarden wordt voldaan. Een regel is gekoppeld aan een apparaatprofiel en een of meer apparaten en bevat voor waarden waaraan moet worden voldaan op basis van telemetrie van apparaten of gebeurtenissen. Een regel heeft ook een of meer gekoppelde acties. De acties omvatten mogelijk het verzenden van e-mail meldingen of het activeren van een webhook-actie voor het verzenden van gegevens naar andere services. De toepassings sjabloon **in de Store analyse-uitchecken** bevat enkele vooraf gedefinieerde regels voor de apparaten in de toepassing.

In deze sectie maakt u een nieuwe regel waarmee het maximale relatieve vochtigheids niveau wordt gecontroleerd op basis van de telemetrie van de RuuviTag-sensor. U voegt een actie toe aan de regel, zodat de toepassing een e-mail verzendt als de vochtigheid het maximum overschrijdt. 

Een regel maken: 

1. Vouw het linkerdeel venster uit.

1. Selecteer **regels**.

1. Selecteer **+ Nieuw**.

1. Voer *vochtigheids niveau* in als de naam van de regel. 

1. Kies het RuuviTag in **scopes**. De regel die u definieert, wordt toegepast op alle Sens oren op basis van die sjabloon. U kunt eventueel een filter maken waarmee de regel wordt toegepast op een gedefinieerde subset van de Sens oren. 

1. Kies `Relative humidity` als **telemetrie**. Dit is de functionaliteit van het apparaat die u in een vorige stap hebt aangepast.

1. Kies `Is greater than` als **operator**. 

1. Voer een typische lucht vochtigheid in voor uw omgeving als **waarde**. Voer bijvoorbeeld *65*in. U hebt een voor waarde ingesteld voor de regel die zich voordoet wanneer de relatieve vochtigheid in een RuuviTag echte of gesimuleerde sensor deze waarde overschrijdt. Mogelijk moet u de waarde naar boven of beneden aanpassen, afhankelijk van het normale vochtigheids bereik in uw omgeving.  

   ![Regel voorwaarden voor Azure IoT Central toevoegen](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-conditions.png)

Een actie toevoegen aan de regel:

1. Selecteer **+ e-mail adres**. 

1. Voer de *melding hoge lucht vochtigheid* in als de beschrijvende **weergave naam** voor de actie. 

1. Voer het e-mail adres in dat is gekoppeld aan uw account in **tot**. Als u een andere e-mail gebruikt, moet het adres dat u gebruikt, zijn voor een gebruiker die aan de toepassing is toegevoegd. De gebruiker moet zich ook ten minste één keer aanmelden en uitschrijven.

1. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mail bericht.

1. Selecteer **gereed** om de actie te volt ooien.

   ![Azure IoT Central acties toevoegen aan regels](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-action.png)

1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

    Binnen een paar minuten moet het opgegeven e-mail account beginnen met het ontvangen van e-mails. De toepassing verzendt een e-mail telkens wanneer een sensor aangeeft dat het vochtigheids niveau groter is dan de waarde in uw voor waarde.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heeft u het volgende geleerd:

* De Azure IoT Central **in-Store-analyse** sjabloon gebruiken om een Retail Store-toepassing te maken
* De toepassings instellingen aanpassen
* IoT-Device-sjablonen maken en aanpassen
* Apparaten verbinden met uw toepassing
* Regels en acties toevoegen om voor waarden te controleren

Nu u een Azure IoT Central condition-bewakings toepassing hebt gemaakt, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Het operator dashboard aanpassen](./tutorial-in-store-analytics-customize-dashboard-pnp.md)
