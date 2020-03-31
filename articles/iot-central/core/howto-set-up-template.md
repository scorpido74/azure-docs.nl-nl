---
title: Een nieuw IoT-apparaattype definiëren in Azure IoT Central | Microsoft Documenten
description: In dit artikel ziet u als bouwer hoe u een nieuwe Azure IoT-apparaatsjabloon maakt in uw Azure IoT Central-toepassing. U definieert de telemetrie, status, eigenschappen en opdrachten voor uw type.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 6f93d74653aab78e48e613ddf9252a0876548b95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157667"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Een nieuw IoT-apparaattype definiëren in uw Azure IoT Central-toepassing

Een apparaatsjabloon is een blauwdruk die de kenmerken en het gedrag definieert van een type apparaat dat verbinding maakt met een Azure IoT Central-toepassing.

Een bouwer kan bijvoorbeeld een apparaatsjabloon maken voor een verbonden ventilator met de volgende kenmerken:

- Hiermee verzendt u temperatuurtelemetrie
- Verzendt locatie, eigenschap
- Stuurt foutgebeurtenissen voor ventilatormotoren
- Hiermee verzendt u de bedrijfsstatus van de ventilator
- Biedt een schrijfbare ventilatorsnelheideigenschap
- Biedt een opdracht om het apparaat opnieuw op te starten
- Geeft u een algemeen overzicht van het apparaat via een dashboard

Op basis van deze apparaatsjabloon kan een operator echte ventilatorapparaten maken en aansluiten. Al deze ventilatoren hebben metingen, eigenschappen en opdrachten die operators gebruiken om ze te controleren en te beheren. Operators gebruiken de dashboards en formulieren van het apparaat om te communiceren met de ventilatorapparaten.

> [!NOTE]
> Alleen bouwers en beheerders kunnen apparaatsjablonen maken, bewerken en verwijderen. Elke gebruiker kan apparaten maken op de pagina **Apparaten** op basis van bestaande apparaatsjablonen.

[IoT Plug and Play (preview)](../../iot-pnp/overview-iot-plug-and-play.md) stelt IoT Central in staat om apparaten te integreren, zonder dat u een ingesloten apparaatcode schrijft. De kern van IoT Plug and Play (preview) is een modelschema voor apparaatmogelijkheden dat apparaatmogelijkheden beschrijft. In een IoT Central-toepassing gebruiken apparaatsjablonen deze IoT Plug and Play -apparaatcapaciteitsmodellen .In an IoT Central application, device templates use these IoT Plug and Play (preview) device capability models.

Als bouwer hebt u verschillende opties voor het maken van apparaatsjablonen:

- Ontwerp de apparaatsjabloon in IoT Central en implementeer het apparaatcapaciteitsmodel in uw apparaatcode.
- Importeer een apparaatcapaciteitsmodel uit de [Azure Certified for IoT-apparaatcatalogus.](https://aka.ms/iotdevcat) Voeg vervolgens alle cloudeigenschappen, aanpassingen en dashboards toe die uw IoT Central-toepassingsbehoeften nodig hebben.
- Maak een apparaatcapaciteitsmodel met Visual Studio Code. Implementeer uw apparaatcode van het model. Importeer het apparaatcapaciteitsmodel handmatig in uw IoT Central-toepassing en voeg vervolgens alle cloudeigenschappen, aanpassingen en dashboards toe die uw IoT Central-toepassingsbehoeften nodig hebben.
- Maak een apparaatcapaciteitsmodel met Visual Studio Code. Implementeer uw apparaatcode van het model en verbind uw echte apparaat met uw IoT Central-toepassing via een apparaat-eerste verbinding. IoT Central vindt en importeert het apparaatcapaciteitsmodel voor u uit de openbare opslagplaats. U vervolgens alle cloudeigenschappen, aanpassingen en dashboards die uw IoT Central-toepassing nodig heeft, toevoegen aan de apparaatsjabloon.

## <a name="create-a-device-template-from-the-device-catalog"></a>Een apparaatsjabloon maken vanuit de apparaatcatalogus

Als bouwer u snel beginnen met het uitbouwen van uw oplossing met behulp van een IoT Plug and Play (preview) gecertificeerd apparaat. Zie de lijst in de [Azure IoT-apparaatcatalogus](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreert met de apparaatcatalogus, zodat u een apparaatcapaciteitsmodel importeren van een van deze IoT Plug and Play -gecertificeerde apparaten (preview). Ga als lid van het apparaat om een apparaatsjabloon te maken vanaf een van deze apparaten in IoT Central:

1. Ga naar de pagina **Apparaatsjablonen** in uw IoT Central-toepassing.
1. Selecteer **+ Nieuw**en selecteer vervolgens een van de iot-plug- en play-apparaten (preview) in de catalogus. IoT Central maakt een apparaatsjabloon op basis van dit apparaatcapaciteitsmodel.
1. Voeg cloudeigenschappen, aanpassingen of weergaven toe aan uw apparaatsjabloon.
1. Selecteer **Publiceren** om de sjabloon beschikbaar te maken voor operators om apparaten weer te geven en aan te sluiten.

## <a name="create-a-device-template-from-scratch"></a>Een apparaatsjabloon helemaal opnieuw maken

Een apparaatsjabloon bevat:

- Een _apparaatcapaciteitsmodel_ dat de telemetrie, eigenschappen en opdrachten opgeeft die het apparaat implementeert. Deze mogelijkheden zijn ingedeeld in een of meer interfaces.
- _Cloudeigenschappen_ die informatie definiëren die uw IoT Central-toepassing over uw apparaten opslaat. Een cloudeigenschap kan bijvoorbeeld de datum registreren waarop een apparaat voor het laatst is onderhouden. Deze informatie wordt nooit gedeeld met het apparaat.
- _Met aanpassingen_ kan de bouwer een aantal definities in het apparaatcapaciteitsmodel overschrijven. De bouwer kan bijvoorbeeld de naam van een apparaateigenschap overschrijven. Eigenschapnamen worden weergegeven in IoT Central-dashboards en formulieren.
- _Met dashboards en formulieren_ kan de bouwer een gebruikersinterface maken waarmee operators de apparaten die met uw toepassing zijn verbonden, kunnen controleren en beheren.

Ga als een apparaatsjabloon maken in IoT Central:

1. Ga naar de pagina **Apparaatsjablonen** in uw IoT Central-toepassing.
1. Selecteer **+ Nieuw** > **aangepast**.
1. Voer een naam in voor uw sjabloon, zoals **omgevingssensor.**
1. Druk op **Enter**. IoT Central maakt een lege apparaatsjabloon.

## <a name="manage-a-device-template"></a>Een apparaatsjabloon beheren

U de naam van een sjabloon wijzigen of verwijderen van de startpagina van de sjabloon.

Nadat u een apparaatcapaciteitsmodel aan uw sjabloon hebt toegevoegd, u deze publiceren. Totdat u de sjabloon hebt gepubliceerd, u geen apparaat op basis van deze sjabloon aansluiten, dat uw operators op de pagina **Apparaten** kunnen zien.

## <a name="create-a-capability-model"></a>Een capaciteitsmodel maken

Als u een apparaatcapaciteitsmodel wilt maken, u het als:

- Gebruik IoT Central om een aangepast model helemaal opnieuw te maken.
- Een model importeren uit een JSON-bestand. Een apparaatbouwer heeft mogelijk Visual Studio Code gebruikt om een apparaatcapaciteitsmodel voor uw toepassing te maken.
- Selecteer een van de apparaten in de apparaatcatalogus. Met deze optie wordt het apparaatcapaciteitsmodel geïmporteerd dat de fabrikant voor dit apparaat heeft gepubliceerd. Een apparaatcapaciteitsmodel dat als volgt wordt geïmporteerd, wordt automatisch gepubliceerd.

## <a name="manage-a-capability-model"></a>Een capaciteitsmodel beheren

Nadat u een apparaatcapaciteitsmodel hebt gemaakt, u het:

- Interfaces toevoegen aan het model. Een model moet ten minste één interface hebben.
- Metagegevens van modellen bewerken, zoals de id, naamruimte en naam.
- Verwijder het model.

## <a name="create-an-interface"></a>Een interface maken

Een apparaatmoet ten minste één interface hebben. Een interface is een herbruikbare verzameling van mogelijkheden.

Ga als u een interface maken:

1. Ga naar het apparaatcapaciteitsmodel en kies **+ Interface toevoegen**.

1. Op de pagina **Selecteer een interface** u het nieuws op:

    - Maak een aangepaste interface vanaf nul.
    - Een bestaande interface importeren uit een bestand. Een apparaatbouwer heeft mogelijk Visual Studio Code gebruikt om een interface voor uw apparaat te maken.
    - Kies een van de standaardinterfaces, zoals de **interface apparaatinformatie.** Standaardinterfaces geven de mogelijkheden op die veel apparaten gemeen hebben. Deze standaardinterfaces worden gepubliceerd door Azure IoT en kunnen niet worden geversioned of bewerkt.

1. Nadat u een interface hebt gemaakt, kiest u **Identiteit bewerken** om de weergavenaam van de interface te wijzigen.

1. Als u ervoor kiest om een aangepaste interface helemaal opnieuw te maken, u de mogelijkheden van uw apparaat toevoegen. Apparaatmogelijkheden zijn telemetrie, eigenschappen en opdrachten.

### <a name="telemetry"></a>Telemetrie

Telemetrie is een stroom van waarden die vanaf het apparaat worden verzonden, meestal vanaf een sensor. Een sensor kan bijvoorbeeld de omgevingstemperatuur rapporteren.

In de volgende tabel worden de configuratie-instellingen voor een telemetriemogelijkheid weergegeven:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergavenaam voor de telemetriewaarde die wordt gebruikt op dashboards en formulieren. |
| Name | De naam van het veld in het telemetriebericht. IoT Central genereert een waarde voor dit veld op basis van de weergavenaam, maar u indien nodig uw eigen waarde kiezen. |
| Type mogelijkheid | Telemetrie. |
| Semantisch type | Het semantische type telemetrie, zoals temperatuur, status of gebeurtenis. De keuze van het semantische type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het type telemetriegegevens, zoals dubbel, tekenreeks of vector. De beschikbare keuzes worden bepaald door het semantische type. Schema is niet beschikbaar voor de gebeurtenis- en statusssen semantische typen. |
| Severity | Alleen beschikbaar voor het semantische type gebeurtenis. De ernst is **Fout,** **Informatie**of **Waarschuwing**. |
| Statuswaarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke statuswaarden, die elk weergavenaam, naam, opsommingstype en waarde hebben. |
| Eenheid | Een eenheid voor de telemetriewaarde, ** &deg;** zoals **mph**, **%** of C . |
| Weergave-eenheid | Een display-eenheid voor gebruik op dashboards en formulieren. |
| Opmerking | Eventuele opmerkingen over de telemetrie mogelijkheid. |
| Beschrijving | Een beschrijving van de telemetriemogelijkheid. |

### <a name="properties"></a>Eigenschappen

Eigenschappen vertegenwoordigen point-in-time waarden. Een apparaat kan bijvoorbeeld een eigenschap gebruiken om de doeltemperatuur te rapporteren die het probeert te bereiken. U schrijfbare eigenschappen van IoT Central instellen.

In de volgende tabel worden de configuratie-instellingen voor een eigenschapsmogelijkheid weergegeven:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergavenaam voor de eigenschapswaarde die wordt gebruikt op dashboards en formulieren. |
| Name | De naam van het pand. IoT Central genereert een waarde voor dit veld op basis van de weergavenaam, maar u indien nodig uw eigen waarde kiezen. |
| Type mogelijkheid | Eigenschap. |
| Semantisch type | Het semantische type van de eigenschap, zoals temperatuur, status of gebeurtenis. De keuze van het semantische type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het type eigenschapsgegevens, zoals dubbel, tekenreeks of vector. De beschikbare keuzes worden bepaald door het semantische type. Schema is niet beschikbaar voor de gebeurtenis- en statusssen semantische typen. |
| Beschrijfbaar | Als de eigenschap niet kan worden geschreven, kan het apparaat eigenschapswaarden rapporteren aan IoT Central. Als de eigenschap schrijfbaar is, kan het apparaat eigenschapswaarden rapporteren aan IoT Central en kan IoT Central eigenschapsupdates naar het apparaat verzenden.
| Severity | Alleen beschikbaar voor het semantische type gebeurtenis. De ernst is **Fout,** **Informatie**of **Waarschuwing**. |
| Statuswaarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke statuswaarden, die elk weergavenaam, naam, opsommingstype en waarde hebben. |
| Eenheid | Een eenheid voor de waarde van **%** de eigenschap, zoals **mph**, of ** &deg;C**. |
| Weergave-eenheid | Een display-eenheid voor gebruik op dashboards en formulieren. |
| Opmerking | Eventuele opmerkingen over de eigenschap vermogen. |
| Beschrijving | Een beschrijving van de eigenschappencapaciteit. |

### <a name="commands"></a>Opdrachten

U apparaatopdrachten van IoT Central bellen. Opdrachten geven optioneel parameters door aan het apparaat en ontvangen een antwoord van het apparaat. U bijvoorbeeld een opdracht aanroepen om een apparaat binnen 10 seconden opnieuw op te starten.

In de volgende tabel worden de configuratie-instellingen voor een opdrachtmogelijkheid weergegeven:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergavenaam voor de opdracht die wordt gebruikt op dashboards en formulieren. |
| Name | De naam van de opdracht. IoT Central genereert een waarde voor dit veld op basis van de weergavenaam, maar u indien nodig uw eigen waarde kiezen. |
| Type mogelijkheid | Opdracht. |
| Opdracht | `SynchronousExecutionType`. |
| Opmerking | Eventuele opmerkingen over de opdrachtcapaciteit. |
| Beschrijving | Een beschrijving van de opdrachtcapaciteit. |
| Aanvraag | Indien ingeschakeld, een definitie van de parameter request, inclusief: naam, weergavenaam, schema, eenheid en weergave-eenheid. |
| Antwoord | Als dit is ingeschakeld, wordt een definitie van het opdrachtantwoord gedefinieerd, waaronder: naam, weergavenaam, schema, eenheid en weergave-eenheid. |

## <a name="manage-an-interface"></a>Een interface beheren

Als u de interface niet hebt gepubliceerd, u de mogelijkheden bewerken die door de interface zijn gedefinieerd. Nadat u de interface hebt gepubliceerd, moet u een nieuwe versie van de apparaatsjabloon en versie van de interface maken als u wijzigingen wilt aanbrengen. U wijzigingen aanbrengen waarvoor geen versienodig is, zoals weergavenamen of eenheden, in de sectie **Aanpassen.**

U de interface ook exporteren als EEN JSON-bestand als u het opnieuw wilt gebruiken in een ander capaciteitsmodel.

## <a name="add-cloud-properties"></a>Cloudeigenschappen toevoegen

Gebruik cloudeigenschappen om informatie over apparaten op te slaan in IoT Central. Cloud-eigenschappen worden nooit naar een apparaat verzonden. U bijvoorbeeld cloudeigenschappen gebruiken om de naam op te slaan van de klant die het apparaat heeft geïnstalleerd of de laatste servicedatum van het apparaat.

In de volgende tabel worden de configuratie-instellingen voor een cloudeigenschap weergegeven:

| Veld | Beschrijving |
| ----- | ----------- |
| Weergavenaam | De weergavenaam voor de waarde van de cloudeigenschap die wordt gebruikt op dashboards en formulieren. |
| Name | De naam van de cloudeigenschap. IoT Central genereert een waarde voor dit veld op basis van de weergavenaam, maar u indien nodig uw eigen waarde kiezen. |
| Semantisch type | Het semantische type van de eigenschap, zoals temperatuur, status of gebeurtenis. De keuze van het semantische type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevenstype cloudeigenschap, zoals dubbel, tekenreeks of vector. De beschikbare keuzes worden bepaald door het semantische type. |

## <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen wanneer u een geïmporteerde interface moet wijzigen of IoT Central-specifieke functies aan een mogelijkheid moet toevoegen. U alleen velden aanpassen die de compatibiliteit van de interface niet verbreken. U kunt bijvoorbeeld:

- Pas de weergavenaam en eenheden van een mogelijkheid aan.
- Voeg een standaardkleur toe die u wilt gebruiken wanneer de waarde in een grafiek wordt weergegeven.
- Geef initiële, minimale en maximale waarden voor een eigenschap op.

U de naam of het type mogelijkheid niet aanpassen. Als er wijzigingen zijn die u niet aanbrengen in de sectie **Aanpassen,** moet u de sjabloon en interface van uw apparaat versie gebruiken om de mogelijkheid te wijzigen.

### <a name="generate-default-views"></a>Standaardweergaven genereren

Het genereren van standaardweergaven is een snelle manier om uw belangrijke apparaatgegevens te visualiseren. Er zijn maximaal drie standaardweergaven gegenereerd voor uw apparaatsjabloon:

- **Opdrachten** bieden een weergave met apparaatopdrachten en stelt de operator in staat deze naar uw apparaat te verzenden.
- **Overzicht** biedt een weergave met apparaattelemetrie, het weergeven van grafieken en statistieken.
- **Over** biedt een weergave met apparaatinformatie, met apparaateigenschappen.

Nadat u **Standaardweergaven genereren**hebt geselecteerd, ziet u dat ze automatisch zijn toegevoegd onder de sectie **Weergaven** van uw apparaatsjabloon.

## <a name="add-dashboards"></a>Dashboards toevoegen

Voeg dashboards toe aan een apparaatsjabloon om operators in staat te stellen een apparaat te visualiseren met behulp van grafieken en statistieken. U meerdere dashboards voor een apparaatsjabloon hebben.

Een dashboard toevoegen aan een apparaatsjabloon:

1. Ga naar de apparaatsjabloon en selecteer **Weergaven**.
1. Kies **Het apparaat visualiseren**.
1. Voer een naam in voor uw dashboard in **Dashboardnaam**.
1. Voeg tegels toe aan uw dashboard vanuit de lijst met statische, eigenschap, cloudeigenschap, telemetrie en opdrachttegels. Sleep en laat de tegels die u wilt toevoegen aan uw dashboard slepen en neerzetten.
1. Als u meerdere telemetriewaarden wilt plotten op één grafiektegel, selecteert u de telemetriewaarden en selecteert u **Combineren**.
1. Configureer elke tegel die u toevoegt om aan te passen hoe deze gegevens weergeeft. U dit doen door het tandwielpictogram te selecteren of door **Configuratie wijzigen** op de grafiektegel te selecteren.
1. Schik en wijzig het formaat van de tegels op uw dashboard.
1. Sla de wijzigingen op.

### <a name="configure-preview-device-to-view-dashboard"></a>Voorbeeldapparaat configureren om dashboard weer te geven

Als u uw dashboard wilt weergeven en testen, selecteert **u Voorvertoningsapparaat configureren**. Hierdoor u het dashboard zien zoals uw operator het ziet nadat het is gepubliceerd. Gebruik deze optie om te valideren dat uw weergaven de juiste gegevens weergeven. U kunt kiezen uit de volgende mogelijkheden:

- Geen voorbeeldapparaat.
- Het echte testapparaat dat u hebt geconfigureerd voor uw apparaatsjabloon.
- Een bestaand apparaat in uw toepassing, met behulp van de apparaat-ID.

## <a name="add-forms"></a>Formulieren toevoegen

Voeg formulieren toe aan een apparaatsjabloon om operators in staat te stellen een apparaat te beheren door eigenschappen weer te geven en in te stellen. Operators kunnen alleen cloudeigenschappen en schrijfbare apparaateigenschappen bewerken. U meerdere formulieren voor een apparaatsjabloon hebben.

Ga als lid van het formulier naar een apparaatsjabloon:

1. Ga naar de apparaatsjabloon en selecteer **Weergaven**.
1. Kies **Bewerkingsapparaat en Cloudgegevens**.
1. Voer een naam in voor uw formulier in **Formuliernaam**.
1. Selecteer het aantal kolommen dat u wilt gebruiken om uw formulier in te stellen.
1. Voeg eigenschappen toe aan een bestaande sectie op uw formulier of selecteer eigenschappen en kies **Sectie toevoegen**. Secties gebruiken om eigenschappen op uw formulier te groeperen. U een titel aan een sectie toevoegen.
1. Configureer elke eigenschap op het formulier om zijn gedrag aan te passen.
1. Schik de eigenschappen op uw formulier.
1. Sla de wijzigingen op.

## <a name="publish-a-device-template"></a>Een apparaatsjabloon publiceren

Voordat u een apparaat verbinden dat uw apparaatcapaciteitsmodel implementeert, moet u de apparaatsjabloon publiceren.

Nadat u een apparaatsjabloon hebt gepubliceerd, u slechts beperkte wijzigingen aanbrengen in het apparaatcapaciteitsmodel. Als u een interface wilt wijzigen, moet u [een nieuwe versie maken en publiceren.](./howto-version-device-template.md)

Als u een apparaatsjabloon wilt publiceren, gaat u naar uw apparaatsjabloon en selecteert u **Publiceren**.

Nadat u een apparaatsjabloon hebt gepubliceerd, kan een operator naar de pagina **Apparaten** gaan en echte of gesimuleerde apparaten toevoegen die uw apparaatsjabloon gebruiken. U uw apparaatsjabloon blijven wijzigen en opslaan terwijl u wijzigingen aanbrengt. Wanneer u deze wijzigingen naar de operator wilt pushen om onder de pagina **Apparaten** weer te geven, moet u Elke keer **Publiceren** selecteren.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

* Maak een nieuwe IoT-apparaatsjabloon.
* Cloudeigenschappen maken.
* Maak aanpassingen.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* Publiceer uw apparaatsjabloon.

Vervolgens u:

> [!div class="nextstepaction"]
> [Een apparaat verbinden](howto-connect-devkit.md)
