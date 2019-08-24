---
title: Een sjabloon voor een apparaat instellen in een Azure IoT Central-toepassing | Microsoft Docs
description: Meer informatie over het instellen van een apparaatprofiel met metingen, instellingen, eigenschappen, regels en een dash board.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fd7cd8f73f602953573b7e6edab32bf2a6aecc05
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998126"
---
# <a name="set-up-and-manage-a-device-template-preview-features"></a>Een sjabloon voor een apparaat instellen en beheren (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

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

[IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) maakt het IOT Central mogelijk om apparaten te integreren zonder dat u een Inge sloten apparaatcode hoeft te schrijven. De kern van IoT Plug en Play is een schema voor het capaciteits model van het apparaat dat de mogelijkheden van apparaten beschrijft. In een IoT Central preview-toepassing gebruiken Device-sjablonen deze IoT Plug en Play-mogelijkheden voor het apparaat.

Als opbouw functie hebt u verschillende opties voor het maken van Apparaatinstellingen:

- Ontwerp de sjabloon voor het apparaat in IoT Central en implementeer vervolgens het hulp model van het apparaat in de code van uw apparaat.
- Importeer een mogelijkheidsprofiel uit de [Azure Certified voor IOT-Apparaatbeheer](https://aka.ms/iotdevcat) en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IOT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model. Importeer het mogelijkheidsprofiel hand matig in uw IoT Central-toepassing en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IoT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model en sluit uw echte apparaat aan op uw IoT Central-toepassing met behulp van een apparaat-eerste verbinding. IoT Central zoekt en importeert het mogelijkheidsprofiel uit de open bare opslag plaats voor u. U kunt vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toevoegen die uw IoT Central toepassing nodig heeft voor de sjabloon voor het apparaat.

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

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de telemetrie-waarde die wordt gebruikt voor dash boards en formulieren. |
| Name | De naam van het veld in het telemetrie-bericht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type capaciteit | Telemetrie. |
| Semantisch type | Het semantische type van de telemetrie, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type telemetrie, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Severity | Alleen beschikbaar voor het semantische gebeurtenis type. **Fout**, **informatie**of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de telemetrie-waarde, zoals **%** mph,, of  **&deg;C**. |
| Eenheid weer geven | Een weergave-eenheid voor gebruik in dash boards en formulieren. |
| Opmerking | Eventuele opmerkingen over de telemetrie-mogelijkheid. |
| Description | Een beschrijving van de telemetrie-mogelijkheid. |

### <a name="properties"></a>properties

Eigenschappen geven een punt in tijd waarden weer. Een apparaat kan bijvoorbeeld een eigenschap gebruiken om de doel temperatuur te rapporteren die het probeert te bereiken. U kunt schrijf bare eigenschappen instellen van IoT Central.

De volgende tabel bevat de configuratie-instellingen voor een eigenschaps mogelijkheid:

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de waarde van de eigenschap die wordt gebruikt in dash boards en formulieren. |
| Name | De naam van de eigenschap. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type capaciteit | Eigenschap. |
| Semantisch type | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type van de eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Beschrijfbaar | Als de eigenschap niet schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central. Als de eigenschap schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central en IoT Central eigenschaps updates naar het apparaat verzenden.
| Severity | Alleen beschikbaar voor het semantische gebeurtenis type. **Fout**, **informatie**of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de eigenschaps waarde,zoals mph **%** ,, of  **&deg;C**. |
| Eenheid weer geven | Een weergave-eenheid voor gebruik in dash boards en formulieren. |
| Opmerking | Eventuele opmerkingen over de eigenschaps mogelijkheid. |
| Description | Een beschrijving van de eigenschaps mogelijkheid. |

### <a name="commands"></a>Opdrachten

U kunt de opdrachten van een apparaat aanroepen vanuit IoT Central. Opdrachten geven optioneel para meters door aan het apparaat en ontvangen een reactie van het apparaat. U kunt bijvoorbeeld een opdracht aanroepen om een apparaat binnen tien seconden opnieuw op te starten.

De volgende tabel bevat de configuratie-instellingen voor een opdracht mogelijkheid:

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de opdracht die wordt gebruikt voor dash boards en formulieren. |
| Name | De naam van de opdracht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Type capaciteit | Opdracht |
| Opdracht | SynchronousExecutionType. |
| Opmerking | Eventuele opmerkingen over de opdracht mogelijkheid. |
| Description | Een beschrijving van de opdracht mogelijkheid. |
| Aanvraag | Indien ingeschakeld, een definitie van de aanvraag parameter, waaronder: naam, weergave naam, schema, eenheid en weer gave-eenheid. |
| Antwoord | Als deze optie is ingeschakeld, is dit een definitie van het antwoord op de opdracht, waaronder: naam, weergave naam, schema, eenheid en weer gave-eenheid. |

## <a name="manage-an-interface"></a>Een interface beheren

U hebt de interface niet gepubliceerd, maar u kunt de mogelijkheden bewerken die door de interface zijn gedefinieerd. Zodra de interface is gepubliceerd, moet u een nieuwe versie van de sjabloon voor het apparaat maken en de interface op een andere versie instellen. Wijzigingen waarvoor geen versie beheer nodig is, zoals het weer geven van namen of eenheden, kunnen worden gemaakt in de sectie **aanpassen** .

U kunt de interface ook exporteren als een JSON-bestand als u het opnieuw wilt gebruiken in een ander model voor functionaliteit.

## <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Gebruik Cloud eigenschappen om informatie over apparaten op te slaan in IoT Central. Cloud eigenschappen worden nooit verzonden naar een apparaat. U kunt bijvoorbeeld Cloud eigenschappen gebruiken om de naam op te slaan van de klant die het apparaat heeft geïnstalleerd of de laatste service datum van het apparaat.

De volgende tabel bevat de configuratie-instellingen voor een Cloud eigenschap:

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de waarde van de Cloud eigenschap die wordt gebruikt in dash boards en formulieren. |
| Name | De naam van de Cloud eigenschap. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
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

1. **Opdrachten** bieden een weer gave met opdrachten op het apparaat. de operator kan deze vervolgens naar uw apparaat verzenden.
1. **Overzicht** biedt een weer gave met telemetrie van apparaten, waarbij diagrammen en metrische gegevens worden weer gegeven.
1. **Over** geeft u een weer gave met apparaatgegevens, waarbij de apparaateigenschappen worden weer gegeven.

Wanneer u **Standaard weergaven genereren** hebt geselecteerd, ziet u dat ze automatisch zijn toegevoegd in de sectie **weer gaven** van uw sjabloon.

## <a name="add-dashboards"></a>Dash boards toevoegen

Voeg Dash boards toe aan een apparaatprofiel om Opera tors in staat te stellen een apparaat te visualiseren met grafieken en metrische gegevens. U kunt meerdere Dash boards voor een sjabloon voor het apparaat hebben.

Een dash board toevoegen aan een sjabloon voor het apparaat:

1. Ga naar de sjabloon voor het apparaat en selecteer **weer gaven**.
1. Kies vervolgens **visualiseren van het apparaat**.
1. Voer een naam in voor uw dash board in de naam van het **dash board**.
1. Tegels toevoegen aan uw dash board vanuit de lijst met statische, eigenschap, Cloud eigenschap, telemetrie en opdracht tegels. Sleep de tegels die u wilt toevoegen aan uw dash board en zet deze neer.
1. Als u meerdere telemetrie-waarden wilt tekenen op één grafiek tegel, selecteert u de telemetriegegevens en selecteert u vervolgens **combi neren**.
1. Configureer elke tegel die u toevoegt om aan te passen hoe gegevens worden weer gegeven door het tandwiel pictogram te selecteren of door de knop **configuratie wijzigen** in de grafiek tegel te selecteren.
1. De tegels op uw dash board rangschikken en het formaat ervan wijzigen.
1. Sla de wijzigingen op.

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

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een sjabloon voor een apparaat instelt in uw Azure IoT Central-toepassing, kunt u het volgende doen:

> [!div class="nextstepaction"]
> [Een nieuwe sjabloon versie](howto-version-device-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
> voor een apparaat maken een[MXChip IOT DevKit-apparaat verbinden met uw Azure IOT Central-toepassing](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
