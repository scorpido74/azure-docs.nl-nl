---
title: Geef een nieuw IoT-apparaattype op in azure IoT Central | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u als Builder een nieuwe Azure IoT-apparaatprofiel maakt in uw Azure IoT Central-toepassing. U definieert de telemetrie, de status, de eigenschappen en de opdrachten voor uw type.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 1f68ddabde58c68a4096fcc7a0b0e1dd8a2ba9e6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176205"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Zelf studie: een nieuw IoT-apparaattype definiëren in uw Azure IoT Central-toepassing (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Een sjabloon voor een apparaat is een blauw druk die de kenmerken en het gedrag definieert van een type apparaat dat verbinding maakt met een Azure IoT Central-toepassing.

Een opbouw functie kan bijvoorbeeld een apparaatprofiel maken voor een verbonden ventilator die de volgende kenmerken heeft:

- Verzendt een telemetrie van de Tempe ratuur
- Eigenschap voor verzenden van locatie
- Hiermee worden fout gebeurtenissen van een ventilator motor verzonden
- Hiermee wordt de status van de ventilator verzonden
- Eigenschap Beschrijf bare ventilator snelheid
- Opdracht voor het opnieuw opstarten van het apparaat
- Dash board waarmee u een algemene weer gave van het apparaat krijgt

Met deze apparaatprofiel kan een operator echte ventilator apparaten maken en verbinden. Al deze ventilatoren hebben metingen, eigenschappen en opdrachten die Opera tors gebruiken om vervolgens te controleren en te beheren. Opera tors van het dash board en de formulieren worden gebruikt om te communiceren met de ventilator apparaten.

> [!NOTE]
> Alleen bouwers en beheerders kunnen sjablonen voor apparaten maken, bewerken en verwijderen. Elke gebruiker kan apparaten op de pagina **apparaten** maken op basis van bestaande Apparaatinstellingen.

[IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) maakt het IOT Central mogelijk om apparaten te integreren zonder dat u een Inge sloten apparaatcode hoeft te schrijven. De kern van IoT Plug en Play is een schema voor het capaciteits model van het apparaat dat de mogelijkheden van apparaten beschrijft. In een IoT Central preview-toepassing gebruiken Device-sjablonen deze IoT Plug en Play-mogelijkheden voor het apparaat.

Als opbouw functie hebt u verschillende opties voor het maken van Apparaatinstellingen:

- Ontwerp de sjabloon voor het apparaat in IoT Central en implementeer vervolgens het hulp model van het apparaat in de code van uw apparaat.
- Importeer een mogelijkheidsprofiel uit de [Azure Certified voor IOT-Apparaatbeheer](https://aka.ms/iotdevcat) en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IOT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model. Importeer het mogelijkheidsprofiel hand matig in uw IoT Central-toepassing en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IoT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model en sluit uw echte apparaat aan op uw IoT Central-toepassing met behulp van een apparaat-eerste verbinding. IoT Central zoekt en importeert het mogelijkheidsprofiel uit de open bare opslag plaats voor u. U kunt vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toevoegen die uw IoT Central toepassing nodig heeft voor de sjabloon voor het apparaat.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure IoT Central-toepassing nodig om deze zelfstudie te voltooien. Volg deze Snelstartgids om [een Azure IOT Central-toepassing te maken](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="create-a-device-template-from-the-device-catalog"></a>Een sjabloon voor een apparaat maken vanuit de catalogus met apparaten

Als bouwer kunt u snel beginnen met het bouwen van uw oplossing met behulp van een IoT Plug en Play Certified-apparaat dat wordt vermeld in de [Azure IOT-apparaatinstantie](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreert met de catalogus met apparaten zodat u een mogelijkheidsprofiel kunt importeren uit een van deze IoT Plug en Play gecertificeerde apparaten. Een sjabloon voor een apparaat maken op basis van een van deze apparaten in IoT Central:

1. Ga naar de pagina met **Apparaatinstellingen** in uw IOT Central-toepassing.
1. Selecteer **+ Nieuw**en selecteer vervolgens een van de IoT-Plug en Play gecertificeerde apparaten uit de onderstaande catalogus. IoT Central maakt een sjabloon op basis van dit mogelijkheidsprofiel.
1. Voeg Cloud eigenschappen, aanpassingen of weer gaven toe aan de sjabloon voor uw apparaat.
1. Selecteer **publiceren** om deze apparaatprofiel te publiceren, zodat deze beschikbaar is voor Opera tors om apparaten weer te geven en te verbinden.

## <a name="create-a-device-template-from-scratch"></a>Een volledig nieuwe sjabloon voor een apparaat maken

Een sjabloon voor het apparaat bevat:

- Een _mogelijkheidsprofiel_ waarmee de telemetrie, eigenschappen en opdrachten worden opgegeven die het apparaat implementeert. Deze mogelijkheden zijn ingedeeld in een of meer interfaces.
- _Cloud eigenschappen_ waarmee informatie wordt gedefinieerd die uw IOT Central-app over uw apparaten opslaat. Bijvoorbeeld, een Cloud eigenschap kan de gegevens opnemen die een apparaat voor het laatst is verwerkt. Deze informatie wordt nooit gedeeld met het apparaat.
- Met _aanpassingen_ kunnen de opbouw functie sommige definities in het mogelijkheidsprofiel overschrijven. De opbouw functie kan bijvoorbeeld de naam van een eigenschap apparaat overschrijven. Eigenschaps namen worden weer gegeven in IoT Central Dash boards en formulieren.
- Met _Dash boards en formulieren_ kan de opbouw functie een gebruikers interface maken waarmee Opera tors de apparaten kunnen controleren en beheren die zijn verbonden met uw toepassing.

Een sjabloon voor een apparaat maken in IoT Central:

1. Ga naar de pagina met **Apparaatinstellingen** in uw IOT Central-toepassing.
1. Selecteer **+ Nieuw**en selecteer vervolgens **aangepast**.
1. Voer een naam in voor de sjabloon, zoals **omgevings sensor**.
1. Druk op **Enter**. IoT Central maakt een lege sjabloon voor het apparaat.

## <a name="manage-a-device-template"></a>Een sjabloon voor een apparaat beheren

U kunt de naam van een sjabloon wijzigen of verwijderen via de start pagina van de sjabloon.

Nadat u een mogelijkheidsprofiel aan uw sjabloon hebt toegevoegd, kunt u dit model publiceren. U kunt op de pagina **apparaten** geen verbinding maken met een apparaat dat is gebaseerd op deze sjabloon om uw Opera tors te zien totdat u de sjabloon hebt gepubliceerd.

## <a name="create-a-capability-model"></a>Een mogelijkheidsprofiel maken

Als u een mogelijkheidsprofiel wilt maken, kunt u het volgende doen:

- Gebruik IoT Central om een volledig aangepast model te maken.
- Importeer een model uit een JSON-bestand. Een opbouw functie voor apparaten kan Visual Studio code gebruiken om een mogelijkheidsprofiel voor uw toepassing te ontwerpen.
- Selecteer een van de apparaten in de catalogus met apparaten. Met deze optie wordt het mogelijkheidsprofiel geïmporteerd dat de fabrikant heeft gepubliceerd voor dit apparaat. Een functionaliteits model voor apparaten dat is geïmporteerd zoals dit wordt automatisch gepubliceerd.

## <a name="manage-a-capability-model"></a>Een mogelijkheidsprofiel beheren

Nadat u een mogelijkheidsprofiel hebt gemaakt, kunt u het volgende doen:

- Interfaces toevoegen aan het model. Een model moet ten minste één interface hebben.
- Model meta gegevens bewerken, zoals de ID, naam ruimte en naam.
- Verwijder het model.

## <a name="create-an-interface"></a>Een interface maken

De capaciteit van een apparaat moet ten minste één interface hebben. Een interface is een herbruikbare verzameling mogelijkheden.

Een interface maken:

1. Ga naar het mogelijkheidsprofiel en kies **+ interface toevoegen**.

1. Op de pagina **een interface selecteren** kunt u het volgende doen:

    - Een volledig nieuwe aangepaste interface maken.
    - Importeer een bestaande interface uit een bestand. Een opbouw functie voor apparaten kan Visual Studio code gebruiken om een interface voor uw apparaat te maken.
    - Kies een van de standaard interfaces, zoals de **apparaatgegevens** interface. Standaard interfaces geven de mogelijkheden die voor veel apparaten worden gebruikt. Deze standaard interfaces worden gepubliceerd door Microsoft Azure IoT en kunnen niet worden geversied of bewerkt.

1. Nadat u een interface hebt gemaakt, kiest u **identiteit bewerken** om de weergave naam van de interface te wijzigen.

1. Als u een volledig nieuwe aangepaste interface wilt maken, kunt u de mogelijkheden van uw apparaat toevoegen. De mogelijkheden van apparaten zijn telemetrie, eigenschappen en opdrachten.

### <a name="telemetry"></a>Telemetrie

Telemetrie is een stroom van waarden die van het apparaat worden verzonden, meestal van een sensor. Een sensor kan bijvoorbeeld de omgevings temperatuur rapporteren.

De volgende tabel bevat de configuratie-instellingen voor een telemetrie-mogelijkheid:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de telemetrie-waarde die wordt gebruikt voor dash boards en formulieren. |
| Naam | De naam van het veld in het telemetrie-bericht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type capaciteit | Telemetrie. |
| Semantisch type | Het semantische type van de telemetrie, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type telemetrie, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Ernst | Alleen beschikbaar voor het semantische gebeurtenis type. **Fout**, **informatie**of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de telemetrie-waarde, zoals **mph**, **%** of **&deg;C**. |
| Eenheid weer geven | Een weergave-eenheid voor gebruik in dash boards en formulieren. |
| Opmerking | Eventuele opmerkingen over de telemetrie-mogelijkheid. |
| Beschrijving | Een beschrijving van de telemetrie-mogelijkheid. |

### <a name="properties"></a>Eigenschappen

Eigenschappen geven een punt in tijd waarden weer. Een apparaat kan bijvoorbeeld een eigenschap gebruiken om de doel temperatuur te rapporteren die het probeert te bereiken. U kunt schrijf bare eigenschappen instellen van IoT Central.

De volgende tabel bevat de configuratie-instellingen voor een eigenschaps mogelijkheid:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de waarde van de eigenschap die wordt gebruikt in dash boards en formulieren. |
| Naam | De naam van de eigenschap. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type capaciteit | Eigenschap. |
| Semantisch type | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type van de eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Beschrijf bare | Als de eigenschap niet schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central. Als de eigenschap schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central en IoT Central eigenschaps updates naar het apparaat verzenden.
| Ernst | Alleen beschikbaar voor het semantische gebeurtenis type. **Fout**, **informatie**of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de eigenschaps waarde, zoals **mph**, **%** of **&deg;C**. |
| Eenheid weer geven | Een weergave-eenheid voor gebruik in dash boards en formulieren. |
| Opmerking | Eventuele opmerkingen over de eigenschaps mogelijkheid. |
| Beschrijving | Een beschrijving van de eigenschaps mogelijkheid. |

### <a name="commands"></a>Opdrachten

U kunt de opdrachten van een apparaat aanroepen vanuit IoT Central. Opdrachten geven optioneel para meters door aan het apparaat en ontvangen een reactie van het apparaat. U kunt bijvoorbeeld een opdracht aanroepen om een apparaat binnen tien seconden opnieuw op te starten.

De volgende tabel bevat de configuratie-instellingen voor een opdracht mogelijkheid:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de opdracht die wordt gebruikt voor dash boards en formulieren. |
| Naam | De naam van de opdracht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type capaciteit | Opdracht |
| Opdracht | SynchronousExecutionType. |
| Opmerking | Eventuele opmerkingen over de opdracht mogelijkheid. |
| Beschrijving | Een beschrijving van de opdracht mogelijkheid. |
| Aanvraag | Indien ingeschakeld, een definitie van de aanvraag parameter, waaronder: naam, weergave naam, schema, eenheid en weer gave-eenheid. |
| Antwoord | Als deze optie is ingeschakeld, is dit een definitie van het antwoord op de opdracht, waaronder: naam, weergave naam, schema, eenheid en weer gave-eenheid. |

## <a name="manage-an-interface"></a>Een interface beheren

U hebt de interface niet gepubliceerd, maar u kunt de mogelijkheden bewerken die door de interface zijn gedefinieerd. Zodra de interface is gepubliceerd, moet u een nieuwe versie van de sjabloon voor het apparaat maken en de interface op een andere versie instellen. Wijzigingen waarvoor geen versie beheer nodig is, zoals het weer geven van namen of eenheden, kunnen worden gemaakt in de sectie **aanpassen** .

U kunt de interface ook exporteren als een JSON-bestand als u het opnieuw wilt gebruiken in een ander model voor functionaliteit.

## <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Gebruik Cloud eigenschappen om informatie over apparaten op te slaan in IoT Central. Cloud eigenschappen worden nooit verzonden naar een apparaat. U kunt bijvoorbeeld Cloud eigenschappen gebruiken om de naam op te slaan van de klant die het apparaat heeft geïnstalleerd of de laatste service datum van het apparaat.

De volgende tabel bevat de configuratie-instellingen voor een Cloud eigenschap:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de waarde van de Cloud eigenschap die wordt gebruikt in dash boards en formulieren. |
| Naam | De naam van de Cloud eigenschap. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Semantisch type | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type van de Cloud eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. |

## <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen wanneer u een geïmporteerde interface wilt wijzigen of IoT Central-specifieke functies aan een mogelijkheid wilt toevoegen. U kunt alleen velden aanpassen die de interface compatibiliteit niet verstoren. U kunt bijvoorbeeld:

- Pas de weergave naam en de eenheden van een mogelijkheid aan.
- Een standaard kleur toevoegen die moet worden gebruikt wanneer de waarde in een grafiek wordt weer gegeven.
- Geef de initiële, minimale en maximale waarde voor een eigenschap op.

U kunt de naam of het type van de mogelijkheid niet aanpassen. Als er wijzigingen zijn die u niet in de sectie **aanpassen** kunt aanbrengen, moet u de sjabloon voor het apparaat en de interface voor het wijzigen van de functionaliteit.

### <a name="generate-default-views"></a>Standaard weergaven genereren

Het genereren van standaard weergaven is een snelle manier om aan de slag te gaan met het visualiseren van uw belang rijke apparaatgegevens. U hebt Maxi maal drie standaard weergaven die worden gegenereerd voor uw apparaatprofiel:

- **Opdrachten** bieden een weer gave met opdrachten op het apparaat. de operator kan deze vervolgens naar uw apparaat verzenden.
- **Overzicht** biedt een weer gave met telemetrie van apparaten, waarbij diagrammen en metrische gegevens worden weer gegeven.
- **Over** geeft u een weer gave met apparaatgegevens, waarbij de apparaateigenschappen worden weer gegeven.

Wanneer u **Standaard weergaven genereren** hebt geselecteerd, ziet u dat ze automatisch zijn toegevoegd in de sectie **weer gaven** van uw sjabloon.

## <a name="add-dashboards"></a>Dash boards toevoegen

Voeg Dash boards toe aan een apparaatprofiel om Opera tors in staat te stellen een apparaat te visualiseren met grafieken en metrische gegevens. U kunt meerdere Dash boards voor een sjabloon voor het apparaat hebben.

Een dash board toevoegen aan een sjabloon voor het apparaat:

- Ga naar de sjabloon voor het apparaat en selecteer **weer gaven**.
- Kies vervolgens **visualiseren van het apparaat**.
- Voer een naam in voor uw dash board in de naam van het **dash board**.
- Tegels toevoegen aan uw dash board vanuit de lijst met statische, eigenschap, Cloud eigenschap, telemetrie en opdracht tegels. Sleep de tegels die u wilt toevoegen aan uw dash board en zet deze neer.
- Als u meerdere telemetrie-waarden wilt tekenen op één grafiek tegel, selecteert u de telemetriegegevens en selecteert u vervolgens **combi neren**.
- Configureer elke tegel die u toevoegt om aan te passen hoe gegevens worden weer gegeven door het tandwiel pictogram te selecteren of door de knop **configuratie wijzigen** in de grafiek tegel te selecteren.
- De tegels op uw dash board rangschikken en het formaat ervan wijzigen.
- Sla de wijzigingen op.

### <a name="configure-preview-device-to-view-dashboard"></a>Preview-apparaat configureren om het dash board weer te geven

Als u uw dash board wilt weer geven en testen, kunt u **Preview-apparaat configureren**selecteren, zodat u het dash board ziet wanneer uw operator wordt gepubliceerd. Met deze optie kunt u controleren of de juiste gegevens in uw weer gaven worden weer gegeven. U kunt kiezen uit geen preview-apparaat, het echte test apparaat dat u hebt geconfigureerd voor uw sjabloon of een bestaand apparaat in uw toepassing met behulp van de apparaat-ID.

## <a name="add-forms"></a>Formulieren toevoegen

Voeg formulieren toe aan een apparaatprofiel om Opera tors in staat te stellen om een apparaat te beheren door eigenschappen te bekijken en in te stellen. Opera tors kunnen alleen eigenschappen van Cloud eigenschappen en beschrijf bare apparaten bewerken. U kunt meerdere formulieren voor een sjabloon voor het apparaat hebben.

Een formulier toevoegen aan een sjabloon voor een apparaat:

1. Ga naar de sjabloon voor het apparaat en selecteer **weer gaven**.
1. Kies vervolgens **apparaat-en Cloud gegevens bewerken**.
1. Voer een naam in voor het formulier in de vorm van een **formulier naam**.
1. Selecteer het aantal kolommen dat moet worden gebruikt om het formulier in te delen.
1. Voeg eigenschappen toe aan een bestaande sectie in het formulier of selecteer Eigenschappen en kies **sectie toevoegen**. Gebruik secties om eigenschappen in uw formulier te groeperen. U kunt een titel toevoegen aan een sectie.
1. Configureer elke eigenschap op het formulier om het gedrag aan te passen.
1. De eigenschappen van het formulier rangschikken.
1. Sla de wijzigingen op.

## <a name="publish-a-device-template"></a>Een sjabloon voor een apparaat publiceren

Voordat u verbinding kunt maken met een apparaat dat het mogelijkheidsprofiel implementeert, moet u de sjabloon voor het apparaat publiceren.

Nadat u een sjabloon voor een apparaat hebt gepubliceerd, kunt u alleen beperkte wijzigingen aanbrengen in het functionaliteits model van het apparaat. Als u een interface wilt wijzigen, moet u [een nieuwe versie maken en publiceren](./howto-version-device-template-pnp.md).

Als u een sjabloon voor een apparaat wilt publiceren, gaat u naar uw apparaatprofiel en selecteert u **publiceren**.

Nadat u een sjabloon voor een apparaat hebt gepubliceerd, kan een operator naar de pagina **apparaten** gaan en een echte of gesimuleerde apparaten toevoegen die gebruikmaken van de sjabloon van het apparaat. U kunt door gaan met het wijzigen en opslaan van de sjabloon voor het apparaat wanneer u wijzigingen aanbrengt. Als u deze wijzigingen echter wilt pushen naar de operator die u wilt weer geven op de pagina **apparaten** , moet u elke keer **publiceren** selecteren.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Een nieuw type IoT-gateway-apparaat definiëren (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt uitgelegd hoe u, als ontwerper, een sjabloon voor een gateway apparaat gebruikt om een nieuw type Azure IoT-apparaat te definiëren in uw Azure IoT Central-toepassing. 

In deze sectie maakt u een sjabloon voor het bouwen van een **Smart** -apparaat. Een Smart buil ding gateway-apparaat:

* Verzendt telemetrie, zoals Tempe ratuur en bezetting.
* Reageert op Beschrijf bare eigenschappen wanneer deze in de cloud worden bijgewerkt, zoals het verzend interval voor telemetrie.
* Reageert op opdrachten zoals het opnieuw instellen van de Tempe ratuur.
* Hiermee staat u relaties toe met andere hulp modellen voor apparaten

### <a name="create-iot-device-templates"></a>IoT-Device-sjablonen maken

U maakt IoT-Device-sjablonen. 

Klik op Device-sjablonen in het linkernavigatievenster, klik op **+ Nieuw**, selecteer **IOT-apparaat** tegel en selecteer bezettings sensor tegel en klik op **volgende: aanpassen**

![IoT-apparaat](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-new.png)

De pagina controleren wordt weer gegeven. Klik op de knop **maken** . 

![IoT-apparaat](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-review.png)

Nieuwe apparaatprofiel wordt gemaakt. 

![IoT-apparaat](./media/tutorial-define-iot-device-type-pnp/occupancy-sensor.png)

U maakt een Device-sjabloon voor S1-sensor. 

Klik op Device-sjablonen in het linkernavigatievenster, klik op **+ Nieuw**, selecteer **IOT-apparaat** tegel en selecteer bezettings sensor tegel en klik op **volgende: aanpassen**

![IoT-apparaat](./media/tutorial-define-iot-device-type-pnp/s1-sensor.png)

De pagina controleren wordt weer gegeven. Klik op de knop **maken** . 

![Downstream-apparaat](./media/tutorial-define-iot-device-type-pnp/s1-review.png)

Nieuwe apparaatprofiel wordt gemaakt. 

![Downstream-apparaat](./media/tutorial-define-iot-device-type-pnp/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Een sjabloon voor een IoT-gateway apparaat maken

U kunt ervoor kiezen om een sjabloon voor een IoT-gateway apparaat te maken. Het gateway apparaat heeft relaties met downstream-apparaten die via het gateway apparaat verbinding maken met IoT Central. 

### <a name="downstream-device-relationships-with-gateway-device"></a>De relaties van het downstream-apparaat met het gateway apparaat

IoT-apparaten kunnen verbinding maken met een Azure IoT-gateway apparaat 

![Pagina centrale toepassing](./media/tutorial-define-iot-device-type-pnp/gatewaypattern.png)

Als ontwerper kunt u Azure IoT-gateway-Apparaatbeheer maken en bewerken in uw toepassing. Nadat u een apparaataccount hebt gepubliceerd, kunt u echte apparaten die de sjabloon voor het apparaat implementeren, verbinden.

### <a name="select-device-template-type"></a>Type sjabloon voor apparaat selecteren 

Als u een nieuwe apparaatprofiel wilt toevoegen aan uw toepassing, gaat u naar de pagina met **Apparaatinstellingen** . Hiertoe selecteert u het tabblad **device templates** in het linkerdeel venster.

![Pagina centrale toepassing](./media/tutorial-define-iot-device-type-pnp/devicetemplate.png)

Klik op **+ Nieuw** om te beginnen met het maken van een nieuwe sjabloon voor het apparaat.

![Apparaatinstellingen-nieuw](./media/tutorial-define-iot-device-type-pnp/devicetemplatenew.png)

![Selectie van Device-sjablonen-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

U gaat op de selectie pagina voor het type apparaat sjabloon land. Selecteer **Azure IOT** -tegel en klik op **volgende:** knop aan de onderkant aanpassen

Selecteer een gateway selectie vakje en klik op **maken** 

![Selectie van Device-sjablonen-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-customize.png)

U wordt weer gegeven met een controle pagina, klikt u op **maken** 

![Device-sjabloon-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

Voer de naam van de gateway sjabloon in **Smart buil ding gateway-sjabloon**in. Klik op **aangepaste** tegel.

Een standaard interface voor **apparaten**toevoegen.

### <a name="add-relationships"></a>Relaties toevoegen

U kunt downstream-relaties toevoegen aan apparaatfuncties voor apparaten waarmee u verbinding maakt met het gateway apparaat.

Maak relaties met de functionaliteits modellen van het downstream-apparaat. Klik op **Opslaan**.

![Device-sjabloon-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud eigenschappen** en vervolgens **+ eigenschap Cloud toevoegen**. Gebruik de informatie in de volgende tabel om een Cloud eigenschap toe te voegen aan de sjabloon van uw apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Datum   |
    | Klant naam     | Geen          | Tekenreeks |

2. Selecteer **Opslaan** om uw wijzigingen op te slaan:

### <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen wanneer u een interface moet wijzigen of IoT Central-specifieke functies wilt toevoegen aan een mogelijkheid waarvoor u geen versie hoeft te hebben van uw apparaat. U kunt velden aanpassen wanneer het capaciteits model zich in een concept of gepubliceerde status bevindt. U kunt alleen velden aanpassen die de interface compatibiliteit niet verstoren. U kunt bijvoorbeeld:

- Pas de weergave naam en de eenheden van een mogelijkheid aan.
- Een standaard kleur toevoegen die moet worden gebruikt wanneer de waarde in een grafiek wordt weer gegeven.
- Geef de initiële, minimale en maximale waarde voor een eigenschap op.

U kunt de naam of het type van de mogelijkheid niet aanpassen. Klik op **Opslaan**.

### <a name="create-views"></a>Weer gaven maken

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het omgevings sensor apparaat weer te geven voor een operator. Met uw aanpassingen kan de operator de omgevings sensor apparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren.

### <a name="generate-default-views"></a>Standaard weergaven genereren

Voor deze zelf studie klikt u op standaard weergaven genereren. Overzicht & over Dash boards worden gegenereerd. 

## <a name="publish-device-template"></a>Device-sjabloon publiceren

Voordat u een gesimuleerde omgevings sensor kunt maken, of als u een echte omgevings sensor wilt verbinden, moet u de sjabloon voor het apparaat publiceren.

Een sjabloon voor een apparaat publiceren:

1. Ga op de pagina **Apparaatinstellingen** naar uw sjabloon.

2. Selecteer **Publiceren**.

3. Kies in het dialoog venster **een sjabloon voor het publiceren van een apparaat** de optie **publiceren**:

Nadat een apparaat sjabloon is gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** en aan de operator. U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter wel updates maken voor de eigenschappen, aanpassingen en weer gaven van de cloud in een sjabloon voor gepubliceerde apparaten zonder versie beheer. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="create-gateway-simulated-device"></a>Door de gateway gesimuleerd apparaat maken

Maak een gesimuleerde Smart-gateway voor slim bouwen vanuit Device Explorer. 

![Device-sjabloon-gateway](./media/tutorial-define-iot-device-type-pnp/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Downstream gesimuleerde apparaten maken

Maak een gesimuleerde bezetting sensor met Device Explorer. 

![Sjabloon voor apparaten-bezetting](./media/tutorial-define-iot-device-type-pnp/occupancydevice.png)

Maak een gesimuleerde S1-sensor in Device Explorer. 

![Device-sjabloon-S1](./media/tutorial-define-iot-device-type-pnp/s1device.png)

## <a name="add-downstream-devices-relationships-to-gateway-device"></a>Relaties met downstream-apparaten toevoegen aan gateway apparaat

Selecteer S1-sensor en bezetting-sensor en klik op **verbinding maken met Gateway**. 

![Device-sjabloon-S1](./media/tutorial-define-iot-device-type-pnp/connecttogateway.png)

Selecteer de sjabloon gateway apparaat, instantie van gateway apparaat en klik op **lid worden**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

* Een nieuwe IoT-gateway maken als een sjabloon voor een apparaat
* Cloud eigenschappen maken.
* Aanpassingen maken.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* Relaties toevoegen
* De sjabloon van het apparaat publiceren.

Hier volgt de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een apparaat verbinden](tutorial-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
