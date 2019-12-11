---
title: Geef een nieuw IoT-apparaattype op in azure IoT Central | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u als Builder een nieuwe Azure IoT-apparaatprofiel maakt in uw Azure IoT Central-toepassing. U definieert de telemetrie, de status, de eigenschappen en de opdrachten voor uw type.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2127bec4d5fdf0d3bf76fb31c548eab98f910d42
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979016"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Zelf studie: een nieuw IoT-apparaattype definiëren in uw Azure IoT Central-toepassing (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Een sjabloon voor een apparaat is een blauw druk die de kenmerken en het gedrag definieert van een type apparaat dat verbinding maakt met een Azure IoT Central-toepassing.

Een opbouw functie kan bijvoorbeeld een apparaatprofiel maken voor een verbonden ventilator die de volgende kenmerken heeft:

- Verzendt een telemetrie van de Tempe ratuur
- Eigenschap voor verzenden van locatie
- Hiermee worden fout gebeurtenissen van een ventilator motor verzonden
- Hiermee wordt de status van de ventilator verzonden
- Biedt een Beschrijf bare ventilator snelheids eigenschap
- Biedt een opdracht voor het opnieuw opstarten van het apparaat
- Geeft een algemeen overzicht van het apparaat via een dash board

Met deze apparaatprofiel kan een operator echte ventilator apparaten maken en verbinden. Al deze ventilatoren hebben metingen, eigenschappen en opdrachten die Opera tors gebruiken om ze te controleren en te beheren. Opera tors van het dash board en de formulieren worden gebruikt om te communiceren met de ventilator apparaten.

> [!NOTE]
> Alleen bouwers en beheerders kunnen sjablonen voor apparaten maken, bewerken en verwijderen. Elke gebruiker kan apparaten op de pagina **apparaten** maken op basis van bestaande Apparaatinstellingen.

[IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) maakt het IOT Central mogelijk om apparaten te integreren, zonder dat u een Inge sloten apparaatcode hoeft te schrijven. De kern van IoT Plug en Play is een schema voor het capaciteits model van het apparaat dat de mogelijkheden van apparaten beschrijft. In een IoT Central preview-toepassing gebruiken Device-sjablonen deze IoT Plug en Play-mogelijkheden voor het apparaat.

Als opbouw functie hebt u verschillende opties voor het maken van Apparaatinstellingen:

- Ontwerp de sjabloon voor het apparaat in IoT Central en implementeer vervolgens het hulp model van het apparaat in de code van uw apparaat.
- Importeer een mogelijkheidsprofiel vanuit de [Azure Certified voor IOT-apparaat Catalog](https://aka.ms/iotdevcat). Voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IoT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model. Importeer het mogelijkheidsprofiel hand matig in uw IoT Central-toepassing en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IoT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model en verbind uw echte apparaat met uw IoT Central-toepassing met behulp van een apparaat-eerste verbinding. IoT Central zoekt en importeert het mogelijkheidsprofiel uit de open bare opslag plaats voor u. U kunt vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toevoegen die uw IoT Central toepassing nodig heeft voor de sjabloon voor het apparaat.

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u [een Azure IOT Central-toepassing maken](quick-deploy-iot-central.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Een sjabloon voor een apparaat maken vanuit de catalogus met apparaten

Als bouwer kunt u snel beginnen met het bouwen van uw oplossing met behulp van een IoT Plug en Play Certified-apparaat. Zie de lijst in de [Azure IOT-Apparaatbeheer](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreert met de-catalogus, zodat u een mogelijkheidsprofiel kunt importeren uit een van deze IoT Plug en Play gecertificeerde apparaten. Een sjabloon voor een apparaat maken op basis van een van deze apparaten in IoT Central:

1. Ga naar de pagina met **Apparaatinstellingen** in uw IOT Central-toepassing.
1. Selecteer **+ Nieuw**en selecteer vervolgens een van de IOT Plug en Play gecertificeerde apparaten uit de catalogus. IoT Central maakt een sjabloon op basis van dit mogelijkheidsprofiel.
1. Voeg Cloud eigenschappen, aanpassingen of weer gaven toe aan de sjabloon voor uw apparaat.
1. Selecteer **publiceren** om de sjabloon beschikbaar te maken voor Opera tors om apparaten weer te geven en te verbinden.

## <a name="create-a-device-template-from-scratch"></a>Een volledig nieuwe sjabloon voor een apparaat maken

Een sjabloon voor het apparaat bevat:

- Een _mogelijkheidsprofiel_ waarmee de telemetrie, eigenschappen en opdrachten worden opgegeven die het apparaat implementeert. Deze mogelijkheden zijn ingedeeld in een of meer interfaces.
- _Cloud eigenschappen_ waarmee informatie wordt gedefinieerd die uw IOT Central-app over uw apparaten opslaat. Een Cloud eigenschap kan bijvoorbeeld de datum vastleggen waarop een apparaat voor het laatst is verwerkt. Deze informatie wordt nooit gedeeld met het apparaat.
- Met _aanpassingen_ kunnen de opbouw functie sommige definities in het mogelijkheidsprofiel overschrijven. De opbouw functie kan bijvoorbeeld de naam van een eigenschap apparaat onderdrukken. Eigenschaps namen worden weer gegeven in IoT Central Dash boards en formulieren.
- Met _Dash boards en formulieren_ kan de opbouw functie een gebruikers interface maken waarmee Opera tors de apparaten kunnen controleren en beheren die zijn verbonden met uw toepassing.

Een sjabloon voor een apparaat maken in IoT Central:

1. Ga naar de pagina met **Apparaatinstellingen** in uw IOT Central-toepassing.
1. Selecteer **+ nieuw** > **aangepast**.
1. Voer een naam in voor de sjabloon, zoals **omgevings sensor**.
1. Druk op **Enter**. IoT Central maakt een lege sjabloon voor het apparaat.

## <a name="manage-a-device-template"></a>Een sjabloon voor een apparaat beheren

U kunt de naam van een sjabloon wijzigen of verwijderen via de start pagina van de sjabloon.

Nadat u een mogelijkheidsprofiel aan uw sjabloon hebt toegevoegd, kunt u dit model publiceren. Totdat u de sjabloon hebt gepubliceerd, kunt u op de pagina **apparaten** geen verbinding maken met een apparaat dat is gebaseerd op deze sjabloon voor uw Opera tors.

## <a name="create-a-capability-model"></a>Een mogelijkheidsprofiel maken

Als u een mogelijkheidsprofiel wilt maken, kunt u het volgende doen:

- Gebruik IoT Central om een volledig aangepast model te maken.
- Importeer een model uit een JSON-bestand. Een opbouw functie voor apparaten kan Visual Studio code gebruiken om een mogelijkheidsprofiel voor uw toepassing te ontwerpen.
- Selecteer een van de apparaten in de catalogus met apparaten. Met deze optie wordt het mogelijkheidsprofiel geïmporteerd dat de fabrikant heeft gepubliceerd voor dit apparaat. Een functionaliteits model voor apparaten dat is geïmporteerd zoals dit wordt automatisch gepubliceerd.

## <a name="manage-a-capability-model"></a>Een mogelijkheidsprofiel beheren

Nadat u een mogelijkheidsprofiel hebt gemaakt, kunt u het volgende doen:

- Interfaces toevoegen aan het model. Een model moet ten minste één interface hebben.
- Meta gegevens van het model bewerken, zoals de ID, naam ruimte en naam.
- Verwijder het model.

## <a name="create-an-interface"></a>Een interface maken

De capaciteit van een apparaat moet ten minste één interface hebben. Een interface is een herbruikbare verzameling mogelijkheden.

Een interface maken:

1. Ga naar het mogelijkheidsprofiel en kies **+ interface toevoegen**.

1. Op de pagina **een interface selecteren** kunt u het volgende doen:

    - Een volledig nieuwe aangepaste interface maken.
    - Importeer een bestaande interface uit een bestand. Een opbouw functie voor apparaten kan Visual Studio code gebruiken om een interface voor uw apparaat te maken.
    - Kies een van de standaard interfaces, zoals de **apparaatgegevens** interface. Standaard interfaces geven de mogelijkheden die voor veel apparaten worden gebruikt. Deze standaard interfaces worden gepubliceerd door Azure IoT en kunnen niet worden geversied of bewerkt.

1. Nadat u een interface hebt gemaakt, kiest u **identiteit bewerken** om de weergave naam van de interface te wijzigen.

1. Als u een volledig nieuwe aangepaste interface wilt maken, kunt u de mogelijkheden van uw apparaat toevoegen. De mogelijkheden van apparaten zijn telemetrie, eigenschappen en opdrachten.

### <a name="telemetry"></a>Telemetrie

Telemetrie is een stroom van waarden die van het apparaat worden verzonden, meestal van een sensor. Een sensor kan bijvoorbeeld de omgevings temperatuur rapporteren.

De volgende tabel bevat de configuratie-instellingen voor een telemetrie-mogelijkheid:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de telemetrie-waarde die wordt gebruikt voor dash boards en formulieren. |
| Naam | De naam van het veld in het telemetrie-bericht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type van mogelijkheid | Telemetrie. |
| Semantisch type | Het semantische type van de telemetrie, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type telemetrie, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Ernst | Alleen beschikbaar voor het semantische gebeurtenis type. De ernst is **fout**, **informatie**of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de telemetrische waarde, zoals **mph**, **%** of **&deg;C**. |
| Eenheid weer geven | Een weergave-eenheid voor gebruik in dash boards en formulieren. |
| Opmerking | Eventuele opmerkingen over de telemetrie-mogelijkheid. |
| Beschrijving | Een beschrijving van de telemetrie-mogelijkheid. |

### <a name="properties"></a>Eigenschappen

Eigenschappen vertegenwoordigen waarden van het tijdstip. Een apparaat kan bijvoorbeeld een eigenschap gebruiken om de doel temperatuur te rapporteren die het probeert te bereiken. U kunt schrijf bare eigenschappen instellen van IoT Central.

De volgende tabel bevat de configuratie-instellingen voor een eigenschaps mogelijkheid:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de waarde van de eigenschap die wordt gebruikt in dash boards en formulieren. |
| Naam | De naam van de eigenschap. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type van mogelijkheid | Eigenschap. |
| Semantisch type | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type van de eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Beschrijfbaar | Als de eigenschap niet schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central. Als de eigenschap schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central en IoT Central eigenschaps updates naar het apparaat verzenden.
| Ernst | Alleen beschikbaar voor het semantische gebeurtenis type. De ernst is **fout**, **informatie**of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de waarde van de eigenschap, zoals **mph**, **%** of **&deg;C**. |
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
| Type van mogelijkheid | Cmd. |
| Opdracht | `SynchronousExecutionType`. |
| Opmerking | Eventuele opmerkingen over de opdracht mogelijkheid. |
| Beschrijving | Een beschrijving van de opdracht mogelijkheid. |
| Aanvraag | Indien ingeschakeld, een definitie van de aanvraag parameter, met inbegrip van: naam, weergave naam, schema, eenheid en weer gave-eenheid. |
| Antwoord | Als deze optie is ingeschakeld, wordt een definitie van het opdracht antwoord gegeven, waaronder: naam, weergave naam, schema, eenheid en weer gave-eenheid. |

## <a name="manage-an-interface"></a>Een interface beheren

Als u de interface nog niet hebt gepubliceerd, kunt u de mogelijkheden bewerken die door de interface zijn gedefinieerd. Als u de interface hebt gepubliceerd en u wijzigingen wilt aanbrengen, moet u een nieuwe versie van de sjabloon voor het apparaat maken en de interface versie. In het gedeelte **aanpassen** kunt u wijzigingen aanbrengen waarvoor geen versie beheer nodig is, zoals het weer geven van namen of eenheden.

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

Het genereren van standaard weergaven is een snelle manier om uw belang rijke apparaatgegevens te visualiseren. U hebt Maxi maal drie standaard weergaven gegenereerd voor uw apparaatprofiel:

- **Opdrachten** bieden een weer gave met opdrachten op het apparaat, zodat uw operator ze naar uw apparaat kan verzenden.
- **Overzicht** biedt een weer gave met telemetrie van apparaten, weer geven van grafieken en metrische gegevens.
- **Informatie over** het weer geven van informatie over apparaten en de weer gave van apparaateigenschappen.

Nadat u **Standaard weergaven genereren**hebt geselecteerd, ziet u dat ze automatisch zijn toegevoegd onder het gedeelte **weer gaven** van de sjabloon voor uw apparaat.

## <a name="add-dashboards"></a>Dash boards toevoegen

Voeg Dash boards toe aan een apparaatprofiel om Opera tors in staat te stellen een apparaat te visualiseren met behulp van grafieken en metrische gegevens. U kunt meerdere Dash boards voor een sjabloon voor het apparaat hebben.

Een dash board toevoegen aan een sjabloon voor het apparaat:

1. Ga naar de sjabloon voor het apparaat en selecteer **weer gaven**.
1. Kies **het apparaat visualiseren**.
1. Voer een naam in voor uw dash board in de naam van het **dash board**.
1. Tegels toevoegen aan uw dash board vanuit de lijst met statische, eigenschap, Cloud eigenschap, telemetrie en opdracht tegels. Sleep de tegels die u wilt toevoegen aan uw dash board en zet deze neer.
1. Als u meerdere telemetrie-waarden wilt tekenen op één grafiek tegel, selecteert u de telemetriegegevens en selecteert u vervolgens **combi neren**.
1. Configureer elke tegel die u toevoegt om de manier aan te passen waarop gegevens worden weer gegeven. U kunt dit doen door het tandwiel pictogram te selecteren of door **configuratie wijzigen** te selecteren in de grafiek tegel.
1. De tegels op uw dash board rangschikken en het formaat ervan wijzigen.
1. Sla de wijzigingen op.

### <a name="configure-preview-device-to-view-dashboard"></a>Preview-apparaat configureren om het dash board weer te geven

Selecteer **Preview-apparaat configureren**om uw dash board weer te geven en te testen. Op deze manier kunt u het dash board weer geven als uw operator ziet na publicatie. Gebruik deze optie om te controleren of de juiste gegevens in uw weer gaven worden weer gegeven. U kunt kiezen uit de volgende mogelijkheden:

- Geen preview-apparaat.
- Het echte test apparaat dat u hebt geconfigureerd voor de sjabloon voor het apparaat.
- Een bestaand apparaat in uw toepassing met behulp van de apparaat-ID.

## <a name="add-forms"></a>Formulieren toevoegen

Voeg formulieren toe aan een apparaatprofiel om Opera tors in staat te stellen om een apparaat te beheren door eigenschappen te bekijken en in te stellen. Opera tors kunnen alleen eigenschappen van Cloud eigenschappen en beschrijf bare apparaten bewerken. U kunt meerdere formulieren voor een sjabloon voor het apparaat hebben.

Een formulier toevoegen aan een sjabloon voor een apparaat:

1. Ga naar de sjabloon voor het apparaat en selecteer **weer gaven**.
1. Kies **apparaat-en Cloud gegevens bewerken**.
1. Voer een naam in voor het formulier in de vorm van een **formulier naam**.
1. Selecteer het aantal kolommen dat moet worden gebruikt om het formulier in te delen.
1. Voeg eigenschappen toe aan een bestaande sectie in het formulier of selecteer Eigenschappen en kies **sectie toevoegen**. Gebruik secties om eigenschappen in uw formulier te groeperen. U kunt een titel toevoegen aan een sectie.
1. Configureer elke eigenschap op het formulier om het gedrag aan te passen.
1. De eigenschappen van het formulier rangschikken.
1. Sla de wijzigingen op.

## <a name="publish-a-device-template"></a>Een apparaatsjabloon publiceren

Voordat u verbinding kunt maken met een apparaat dat het mogelijkheidsprofiel implementeert, moet u de sjabloon voor het apparaat publiceren.

Nadat u een sjabloon voor een apparaat hebt gepubliceerd, kunt u alleen beperkte wijzigingen aanbrengen in het functionaliteits model van het apparaat. Als u een interface wilt wijzigen, moet u [een nieuwe versie maken en publiceren](./howto-version-device-template.md).

Als u een sjabloon voor een apparaat wilt publiceren, gaat u naar uw apparaatprofiel en selecteert u **publiceren**.

Nadat u een sjabloon voor een apparaat hebt gepubliceerd, kan een operator naar de pagina **apparaten** gaan en een echte of gesimuleerde apparaten toevoegen die gebruikmaken van de sjabloon van het apparaat. U kunt door gaan met het wijzigen en opslaan van de sjabloon voor het apparaat wanneer u wijzigingen aanbrengt. Wanneer u deze wijzigingen wilt pushen naar de operator om weer te geven op de pagina **apparaten** , moet u elke keer **publiceren** selecteren.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Een nieuw type IoT-gateway-apparaat definiëren (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt uitgelegd hoe u, als ontwerper, een sjabloon voor een gateway apparaat gebruikt om een nieuw type IoT-apparaat te definiëren in uw IoT Central-toepassing. 

In deze sectie maakt u een sjabloon voor **Slim bouwen** van apparaten. Een Smart buil ding gateway-apparaat:

* Verzendt telemetrie, zoals de Tempe ratuur en bezetting.
* Reageert op schrijf bare eigenschappen wanneer deze in de cloud worden bijgewerkt, zoals het verzend interval voor telemetrie.
* Reageert op opdrachten, zoals het opnieuw instellen van de Tempe ratuur.
* Hiermee kunnen relaties met andere hulp modellen voor apparaten worden ondersteund.

### <a name="create-iot-device-templates"></a>IoT-Device-sjablonen maken

U kunt als volgt IoT-Device-sjablonen maken: 

1. Selecteer in het linkernavigatievenster de optie **Apparaatbeheer**. Selecteer vervolgens **+ Nieuw**en selecteer de tegel **IOT-apparaten** tegel en bezetting. Selecteer **volgende: aanpassen**.

   ![Scherm afbeelding van de pagina met Apparaatinstellingen en opties](./media/tutorial-define-iot-device-type/gateway-downstream-new.png)

1. Op de pagina **controleren** selecteert u **maken**. 

   ![Schermopname van de pagina Beoordelen en maken](./media/tutorial-define-iot-device-type/gateway-downstream-review.png)

1. Er wordt een nieuwe apparaatprofiel gemaakt. 

   ![Scherm opname van de sjabloon nieuw apparaat](./media/tutorial-define-iot-device-type/occupancy-sensor.png)

U kunt als volgt een Device-sjabloon maken voor S1-sensor:

1. Selecteer in het linkernavigatievenster de optie **Apparaatbeheer**. Selecteer vervolgens **+ Nieuw**, en selecteer de tegel **IOT-apparaten** en selecteer de tegel bezettings sensor. Selecteer **volgende: aanpassen**.

   ![Scherm afbeelding van de pagina met Apparaatinstellingen en opties](./media/tutorial-define-iot-device-type/s1-sensor.png)

1. Op de pagina **controleren** selecteert u **maken**. 

   ![Schermopname van de pagina Beoordelen en maken](./media/tutorial-define-iot-device-type/s1-review.png)

1. Er wordt een nieuwe apparaatprofiel gemaakt. 

   ![Scherm opname van de sjabloon nieuw apparaat](./media/tutorial-define-iot-device-type/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Een sjabloon voor een IoT-gateway apparaat maken

U kunt ervoor kiezen om een sjabloon voor een IoT-gateway apparaat te maken. Het gateway apparaat heeft een relatie met downstream-apparaten die verbinding maken met IoT Central via het gateway apparaat. 

### <a name="downstream-device-relationships-with-gateway-device"></a>De relaties van het downstream-apparaat met het gateway apparaat

IoT-apparaten kunnen verbinding maken met een IoT-gateway apparaat.

![Diagram van relatie tussen gateway apparaat en downstream-apparaten](./media/tutorial-define-iot-device-type/gatewaypattern.png)

Als opbouw functie kunt u sjablonen voor IoT-gateway apparaten maken en bewerken in uw toepassing. Nadat u een apparaataccount hebt gepubliceerd, kunt u echte apparaten die de sjabloon voor het apparaat implementeren, verbinden.

### <a name="select-a-device-template-type"></a>Een type sjabloon voor een apparaat selecteren 

Een nieuwe apparaatprofiel toevoegen aan uw toepassing:

1. Selecteer in het linkerdeel venster het tabblad **device sjablonen** .

   ![Scherm afbeelding van de pagina met Apparaatinstellingen](./media/tutorial-define-iot-device-type/devicetemplate.png)

1. Selecteer **+ Nieuw** om te beginnen met het maken van een nieuwe sjabloon voor het apparaat.

   ![Scherm afbeelding van de pagina met Apparaatinstellingen, met nieuwe gemarkeerd](./media/tutorial-define-iot-device-type/devicetemplatenew.png)

   ![Scherm afbeelding van de pagina apparaat aanpassen](./media/tutorial-define-iot-device-type/gateway-review.png)

1. Selecteer op de pagina **sjabloon type selecteren** de optie **Azure IOT**en selecteer vervolgens **volgende: aanpassen**.

   ![Scherm afbeelding van de pagina sjabloon type selecteren](./media/tutorial-define-iot-device-type/gateway-customize.png)

1. Schakel het selectie vakje gateway in en selecteer **maken**.

   ![Scherm afbeelding van de pagina apparaat aanpassen, met gemarkeerde gateway](./media/tutorial-define-iot-device-type/gateway-review.png)

1. Op de pagina controleren selecteert u **maken**. 

1. Voer de naam van de gateway sjabloon in, de **sjabloon Smart buil ding gateway**. Selecteer de **aangepaste** tegel.

1. Een standaard interface voor **apparaten**toevoegen.

### <a name="add-relationships"></a>Relaties toevoegen

U kunt downstream-relaties toevoegen aan apparaatfuncties voor apparaten die u verbindt met een gateway apparaat.

Maak relaties met de functionaliteits modellen van het downstream-apparaat. Selecteer **Opslaan**.

![Scherm opname van de sjabloon Smart buil ding gateway, waarbij verschillende opties zijn gemarkeerd](./media/tutorial-define-iot-device-type/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud eigenschappen** >  **+ Cloud eigenschap toevoegen**. Gebruik de informatie in de volgende tabel om een Cloud eigenschap toe te voegen aan de sjabloon van uw apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Datum   |
    | Klantnaam     | Geen          | Tekenreeks |

2. Selecteer **Opslaan**.

### <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen om een interface te wijzigen of om IoT Central-specifieke functies toe te voegen aan een mogelijkheid waarvoor u geen versie hoeft te hebben van uw apparaat. U kunt velden aanpassen wanneer het capaciteits model zich in een concept of gepubliceerde status bevindt. U kunt alleen velden aanpassen die de interface compatibiliteit niet verstoren. U kunt bijvoorbeeld:

- Pas de weergave naam en de eenheden van een mogelijkheid aan.
- Een standaard kleur toevoegen die moet worden gebruikt wanneer de waarde in een grafiek wordt weer gegeven.
- Geef de initiële, minimale en maximale waarde voor een eigenschap op.

U kunt de naam of het type van de mogelijkheid niet aanpassen.

Wanneer u klaar bent met aanpassen, selecteert u **Opslaan**.

### <a name="create-views"></a>Weergaven aanmaken

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het omgevings sensor apparaat weer te geven voor een operator. Met uw aanpassingen kan de operator de omgevings sensor apparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren.

### <a name="generate-default-views"></a>Standaard weergaven genereren

Als u **Standaard weergaven genereren**selecteert, kunt u het **overzicht** genereren en **over** Dash boards. 

## <a name="publish-a-device-template"></a>Een apparaatsjabloon publiceren

Voordat u een gesimuleerde omgevings sensor kunt maken, of als u een echte omgevings sensor wilt verbinden, moet u de sjabloon voor het apparaat publiceren.

Een sjabloon voor een apparaat publiceren:

1. Ga op de pagina **Apparaatinstellingen** naar uw sjabloon.

2. Selecteer **Publiceren**.

3. Kies in het dialoog venster **een sjabloon voor het publiceren van een apparaat** de optie **publiceren**.

Nadat een apparaat sjabloon is gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** en aan de operator. U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter updates voor de Cloud eigenschappen, aanpassingen en weer gaven maken in een sjabloon voor een gepubliceerd apparaat. Deze updates zorgen er niet voor dat er een nieuwe versie wordt gemaakt. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="create-a-gateway-simulated-device"></a>Een door de gateway gesimuleerd apparaat maken

Maak in de Verkenner een gesimuleerde Smart-gateway voor slim bouwen. 

![Scherm afbeelding van het dialoog venster nieuw apparaat maken](./media/tutorial-define-iot-device-type/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Downstream gesimuleerde apparaten maken

Maak in de Verkenner een gesimuleerde bezettings sensor. 

![Scherm afbeelding van het dialoog venster nieuw apparaat maken](./media/tutorial-define-iot-device-type/occupancydevice.png)

Maak in de Verkenner een gesimuleerde S1-sensor. 

![Scherm afbeelding van het dialoog venster nieuw apparaat maken](./media/tutorial-define-iot-device-type/s1device.png)

## <a name="add-downstream-devices-relationships-to-a-gateway-device"></a>Relaties voor downstream-apparaten toevoegen aan een gateway apparaat

Selecteer S1-sensor en bezetting sensor en selecteer **verbinding maken met Gateway**. 

![Scherm afbeelding van de bezettings sensor, met verbinding maken met de gateway gemarkeerd](./media/tutorial-define-iot-device-type/connecttogateway.png)

Selecteer een sjabloon voor het gateway-apparaat en het exemplaar van het gateway apparaat en selecteer **lid worden**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

* Maak een nieuwe IoT-gateway als een sjabloon voor een apparaat.
* Cloud eigenschappen maken.
* Aanpassingen maken.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* Relaties toevoegen.
* De sjabloon van het apparaat publiceren.

U kunt nu het volgende doen:

> [!div class="nextstepaction"]
> [Een apparaat verbinden](tutorial-connect-pnp-device.md)
