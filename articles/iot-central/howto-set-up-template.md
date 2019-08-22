---
title: Een sjabloon voor een apparaat instellen in een Azure IoT Central-toepassing | Microsoft Docs
description: Meer informatie over het instellen van een apparaatprofiel met metingen, instellingen, eigenschappen, regels en een dash board.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 63c0a04a6d18d6af850b1492d2efa9df9aa65219
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877367"
---
# <a name="set-up-a-device-template"></a>Een sjabloon van het apparaat instellen

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Een sjabloon voor een apparaat is een blauw druk die de kenmerken en het gedrag definieert van een type apparaat dat verbinding maakt met een Azure IoT Central-toepassing.

Een opbouw functie kan bijvoorbeeld een apparaatprofiel maken voor een verbonden ventilator die de volgende kenmerken heeft:

- Meting van de telemetrische Tempe ratuur
- Locatie meting
- Meting van fout gebeurtenis door ventilator motor
- Meting van de operationele status van ventilator
- Instelling voor ventilator snelheid
- Regels voor het verzenden van waarschuwingen
- Dash board waarmee u een algemene weer gave van het apparaat krijgt

Met deze apparaatprofiel kan een operator echte ventilator apparaten maken en verbinden met namen als **ventilator-1** en **ventilator-2**. Al deze ventilatoren hebben metingen, instellingen, eigenschappen, regels en een dash board die gebruikers van uw toepassing kunnen bewaken en beheren.

> [!NOTE]
> Alleen bouwers en beheerders kunnen sjablonen voor apparaten maken, bewerken en verwijderen. Elke gebruiker kan op de **device Explorer** pagina apparaten maken op basis van bestaande Apparaatinstellingen.

## <a name="create-a-device-template"></a>Een sjabloon voor een apparaat maken

1. Navigeer naar de pagina met **Apparaatinstellingen** .

2. Als u een sjabloon wilt maken, begint u met selecteren en selecteert u **+ Nieuw**.

3. Als u snel aan de slag wilt gaan, kiest u uit de bestaande vooraf gemaakte sjablonen. Anders selecteert u **aangepast**, voert u een naam in en klikt u op **maken** om uw eigen sjabloon helemaal zelf te bouwen.

   ![Bibliotheek met Apparaatbeheer](./media/howto-set-up-template/newtemplate.png)

4. Wanneer u een aangepaste sjabloon maakt, ziet u de pagina met details van het **apparaat** voor de nieuwe sjabloon. IoT Central maakt automatisch een gesimuleerd apparaat wanneer u een sjabloon voor een apparaat maakt. Met een gesimuleerd apparaat kunt u het gedrag van uw toepassing testen voordat u een echt apparaat verbindt.

In de volgende secties wordt elk van de tabbladen op de pagina **Apparaatbeheer** beschreven.

## <a name="measurements"></a>Metingen

Metingen zijn de gegevens die afkomstig zijn van uw apparaat. U kunt meerdere metingen toevoegen aan uw apparaatprofiel zodat deze overeenkomen met de mogelijkheden van uw apparaat.

- Telemetrie-metingen zijn de numerieke gegevens punten die uw apparaat in de loop van de tijd verzamelt. Ze worden weer gegeven als een doorlopende stroom. Een voor beeld is een Tempe ratuur.
- **Gebeurtenis** metingen zijn tijdgebonden gegevens die een betekenis hebben op het apparaat. Een Ernst niveau vertegenwoordigt het belang van een gebeurtenis. Een voor beeld is een ventilator fout.
- **Status** metingen vertegenwoordigen de status van het apparaat of de onderdelen ervan gedurende een bepaalde periode. Een ventilator modus kan bijvoorbeeld worden gedefinieerd als het **besturings systeem** en worden **gestopt** als de twee mogelijke statussen.
- **Locatie** metingen zijn de lengte-en breedte coördinaten van het apparaat gedurende een bepaalde periode in. Een ventilator kan bijvoorbeeld van de ene locatie naar de andere worden verplaatst.

### <a name="create-a-telemetry-measurement"></a>Een telemetrie-meting maken

Als u een nieuwe telemetrische meting wilt toevoegen, selecteert u **+ nieuwe meting**, kiest u telemetrie als meet type en voert u de details in het formulier in.

> [!NOTE]
> De veld namen in de sjabloon van het apparaat moeten overeenkomen met de namen van de eigenschappen in de bijbehorende apparaatcode, zodat de telemetrische meting in de toepassing wordt weer gegeven wanneer een echt apparaat is verbonden. Doe hetzelfde wanneer u instellingen, apparaateigenschappen en opdrachten configureert terwijl u de sjabloon voor het apparaat in de volgende secties blijft definiëren.

U kunt bijvoorbeeld een nieuwe meting van de telemetrische Tempe ratuur toevoegen:

| Weergavenaam        | Veldnaam    |  Eenheden    | Min.   |Max.|
| --------------------| ------------- |-----------|-------|---|
| Temperatuur         | temp          |  degC     |  0    |100|

![Formulier telemetrie maken met Details voor de meting van de Tempe ratuur](./media/howto-set-up-template/measurementsform.png)

Nadat u **Opslaan**hebt geselecteerd, wordt de meting van de **Tempe ratuur** weer gegeven in de lijst met metingen. Kortom, u ziet de visualisatie van de temperatuur gegevens van het gesimuleerde apparaat.

Bij het weer geven van telemetrie kunt u kiezen uit de volgende aggregatie opties: Average, minimum, maximum, Sum en Count. **Gemiddelde** is geselecteerd als de standaard aggregatie in de grafiek.

> [!NOTE]
> Het gegevens type van de telemetrie-meting is een getal met drijvende komma.

### <a name="create-an-event-measurement"></a>Een gebeurtenis meting maken

Als u een nieuwe gebeurtenis meting wilt toevoegen, selecteert u **+ nieuwe meting** en selecteert u **gebeurtenis** als metings type. Voer de details in op het **gebeurtenis formulier maken** .

Geef de **weergave naam**, **veld naam**en **Ernst** Details op voor de gebeurtenis. U kunt kiezen uit de drie beschik bare Ernst niveaus: **Fout**, **waarschuwing**en **informatie**.

U kunt bijvoorbeeld een nieuwe fout gebeurtenis voor een **ventilator motor** toevoegen.

| Weergavenaam        | Veldnaam    |  Standaardurgentie |
| --------------------| ------------- |-----------|
| Storing ventilatormotor     | fanmotorerror |  Fout    |

![Formulier gebeurtenis maken met Details voor een ventilator motor gebeurtenis](./media/howto-set-up-template/eventmeasurementsform.png)

Nadat u **Opslaan**hebt geselecteerd, wordt de fout meting van de **ventilator motor** weer gegeven in de lijst met metingen. Kortom, u ziet de visualisatie van de gebeurtenis gegevens van het gesimuleerde apparaat.

Als u meer informatie over een gebeurtenis wilt weer geven, selecteert u het gebeurtenis pictogram in de grafiek:

![Details voor de gebeurtenis ' ventilator motor error '](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Het gegevens type van de gebeurtenis meting is teken reeks.

### <a name="create-a-state-measurement"></a>Een status meting maken

Als u een nieuwe status meting wilt toevoegen, selecteert u de knop **+ nieuwe meting** en selecteert u **status** als metings type. Voer de details in op het formulier **status maken** .

Geef de details op voor de **weergave naam**, de **veld naam**en de **waarden** van de status. Elke waarde kan ook een weergave naam hebben die wordt gebruikt wanneer de waarde wordt weer gegeven in grafieken en tabellen.

U kunt bijvoorbeeld een nieuwe status voor de **ventilator modus** toevoegen die twee mogelijke waarden heeft die het apparaat kan verzenden, gebruiken en **stoppen**.

| Weergavenaam | Veldnaam    |  Waarde 1   | Weergavenaam | Waarde 2    |Weergavenaam  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Ventilatormodus     | ventilatormodus       |  1         | Actief    |     0      | Gestopt      |

![Formulier status bewerken met Details voor de ventilator modus](./media/howto-set-up-template/statemeasurementsform.png)

Nadat u **Opslaan**hebt geselecteerd, wordt de meting van de status van de **ventilator modus** weer gegeven in de lijst met metingen. Kortom, u ziet de visualisatie van de status gegevens van het gesimuleerde apparaat.

Als het apparaat te veel gegevens punten in een kleine duur verzendt, wordt de status meting weer gegeven met een ander visueel element. Selecteer de grafiek om alle gegevens punten binnen die periode in chronologische volg orde weer te geven. U kunt ook het tijds bereik beperken om de meting te zien die is uitgezet in de grafiek.

> [!NOTE]
> Het gegevens type van de status meting is teken reeks.

### <a name="create-a-location-measurement"></a>Een locatie meting maken

Als u een nieuwe locatie meting wilt toevoegen, selecteert u **+ nieuwe meting**, kiest u **locatie** als het meet type en voert u de details in het formulier **meting maken** in.

U kunt bijvoorbeeld een nieuwe telemetrie-meting voor de locatie toevoegen:

| Weergavenaam        | Veldnaam    |
| --------------------| ------------- |
| Locatie van activum      |  assetloc     |

![Formulier ' locatie maken ' met Details voor de locatie meting](./media/howto-set-up-template/locationmeasurementsform.png)

Nadat u **Opslaan**hebt geselecteerd, wordt de **locatie** meting weer gegeven in de lijst met metingen. Kortom, u ziet de visualisatie van de locatie gegevens van het gesimuleerde apparaat.

Bij het weer geven van de locatie kunt u kiezen uit de volgende opties: meest recente locatie en locatie geschiedenis. De **locatie geschiedenis** wordt alleen toegepast gedurende het geselecteerde tijds bereik.

Het gegevens type van de meting van de locatie is een object dat een lengte graad, breedte graad en een optionele hoogte bevat. In het volgende code fragment wordt de Java script-structuur weer gegeven:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

Zodra het apparaat is verbonden, wordt de locatie die u hebt toegevoegd als meting bijgewerkt met de waarde die wordt verzonden door het apparaat. Nadat u uw locatie meting hebt geconfigureerd, kunt u [een kaart toevoegen om de locatie in het dash board van het apparaat te visualiseren](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Instellingen

Instellingen bepalen een apparaat. Hiermee kunnen Opera tors invoer leveren aan het apparaat. U kunt meerdere instellingen toevoegen aan de sjabloon voor het apparaat die als tegels worden weer gegeven op het tabblad **instellingen** voor het gebruik van Opera tors. U kunt veel soorten instellingen toevoegen: getal, tekst, datum, wissel knop en sectie label.

De instellingen kunnen een van de drie statussen hebben. Het apparaat rapporteert deze statussen.

- **Gesynchroniseerd**: Het apparaat is gewijzigd om de waarde van de instelling weer te geven.

- **In behandeling**: Het apparaat wordt momenteel gewijzigd in de waarde voor de instelling.

- **Fout**: Het apparaat heeft een fout geretourneerd.

U kunt bijvoorbeeld een nieuwe instelling voor de ventilator snelheid toevoegen door **instellingen** te selecteren en in te voeren in de nieuwe **nummer** instelling:

| Weergavenaam  | Veldnaam    |  Eenheden  | Decimalen |Oorspronkelijk|
| --------------| ------------- |---------| ---------|---- |
| Snelheid van ventilator     | fanSpeed      | RPM     | 2        | 0   |

![Formulier nummer configureren met Details voor snelheids instellingen](./media/howto-set-up-template/settingsform.png)

Nadat u **Opslaan**hebt geselecteerd, wordt de instelling voor de **ventilator snelheid** weer gegeven als een tegel. Een operator kan de instelling op de pagina **device Explorer** gebruiken om de snelheid van de ventilator van het apparaat te wijzigen.

## <a name="properties"></a>properties

Eigenschappen zijn meta gegevens die zijn gekoppeld aan het apparaat, zoals een vaste locatie van het apparaat en een serie nummer. Voeg meerdere eigenschappen toe aan de sjabloon van het apparaat die als tegels worden weer gegeven op het tabblad **Eigenschappen** . Een eigenschap heeft een type zoals getal, tekst, datum, wissel knop, apparaat-eigenschap, label of vaste locatie. Een operator geeft de waarden voor eigenschappen op wanneer ze een apparaat maken en ze kunnen deze waarden op elk gewenst moment bewerken. Apparaateigenschappen zijn alleen-lezen en worden vanaf het apparaat naar de toepassing verzonden. Een operator kan de apparaateigenschappen niet wijzigen. Wanneer een echt apparaat verbinding maakt, wordt de tegel van de apparaat-eigenschap bijgewerkt in de toepassing.

Er zijn twee categorieën eigenschappen:

- _Apparaateigenschappen_ die het apparaat rapporteert aan de IOT Central-toepassing. Apparaateigenschappen zijn alleen-lezen waarden die worden gerapporteerd door het apparaat en die in de toepassing worden bijgewerkt wanneer een echt apparaat is verbonden.
- _Toepassings eigenschappen_ die in de toepassing zijn opgeslagen en kunnen worden bewerkt door de operator. Toepassings eigenschappen worden alleen in de toepassing opgeslagen en worden nooit door een apparaat gedetecteerd.

U kunt bijvoorbeeld de laatste service datum voor het apparaat toevoegen als een nieuwe **datum** eigenschap (een toepassings eigenschap) op het tabblad **Eigenschappen** :

| Weergavenaam  | Veldnaam | Beginwaarde   |
| --------------| -----------|-----------------|
| Laatste servicebeurt      | lastServiced        | 01/29/2019     |

![Het formulier ' laatste service is geconfigureerd ' op het tabblad ' Eigenschappen '](./media/howto-set-up-template/propertiesform.png)

Nadat u **Opslaan**hebt geselecteerd, wordt de laatste service datum voor het apparaat weer gegeven als een tegel.

Nadat u de tegel hebt gemaakt, kunt u de waarde van de toepassings eigenschap in de **device Explorer**wijzigen.

### <a name="create-a-location-property"></a>Een locatie-eigenschap maken

U kunt geografische context aan uw locatie gegevens in azure IoT Central geven en de breedte-en lengte coördinaten of een straat toewijzen. Azure Maps schakelt deze functionaliteit in IoT Central.

U kunt twee typen locatie-eigenschappen toevoegen:

- **Locatie als een toepassings eigenschap**, die wordt opgeslagen in de toepassing. Toepassings eigenschappen worden alleen in de toepassing opgeslagen en worden nooit door een apparaat gedetecteerd.
- **Locatie als een eigenschap**van het apparaat, die wordt gerapporteerd aan de toepassing. Dit type eigenschap kan het beste worden gebruikt voor een statische locatie.

> [!NOTE]
> De locatie als een eigenschap bevat geen geschiedenis. Als de geschiedenis gewenst is, gebruikt u een locatie meting.

#### <a name="add-location-as-an-application-property"></a>Locatie toevoegen als een toepassings eigenschap

U kunt een locatie-eigenschap maken als eigenschap van een toepassing met behulp van Azure Maps in uw IoT Central toepassing. U kunt bijvoorbeeld het installatie adres van het apparaat toevoegen:

1. Ga naar het tabblad **Eigenschappen** .

2. Selecteer in de bibliotheek **locatie**.

3. Configureer **weergave naam**, **veld naam**en (optioneel) aanvankelijke **waarde** voor de locatie.

    | Weergavenaam  | Veldnaam | Beginwaarde |
    | --------------| -----------|---------|
    | Installatie adres | installAddress | Micro soft, 1 micro soft Way, Redmond, WA 98052   |

   ![Formulier locatie configureren met Details voor de locatie](./media/howto-set-up-template/locationcloudproperty2.png)

   Er zijn twee ondersteunde indelingen voor het toevoegen van een locatie:
   - **Locatie als een adres**
   - **Locatie als coördinaten**

4. Selecteer **Opslaan**. Een operator kan de locatie waarde in het **device Explorer**bijwerken.

#### <a name="add-location-as-a-device-property"></a>Locatie toevoegen als eigenschap van het apparaat

U kunt een locatie-eigenschap maken als eigenschap van het apparaat die het apparaat rapporteert. Als u bijvoorbeeld de locatie van het apparaat wilt bijhouden:

1. Ga naar het tabblad **Eigenschappen** .

2. Selecteer de **eigenschap apparaat** in de bibliotheek.

3. Configureer de weergave naam en de naam van het veld en selecteer **locatie** als het gegevens type:

    | Weergavenaam  | Veldnaam | Gegevenstype |
    | --------------| -----------|-----------|
    | Locatie apparaat | deviceLocation | location  |

   > [!NOTE]
   > De veld namen moeten overeenkomen met de namen van de eigenschappen in de bijbehorende apparaatcode

   ![Formulier eigenschappen van een apparaat configureren met Details voor de locatie](./media/howto-set-up-template/locationdeviceproperty2.png)

Zodra het werkelijke apparaat is verbonden, wordt de locatie die u hebt toegevoegd als eigenschap apparaat bijgewerkt met de waarde die wordt verzonden door het apparaat. Nadat u uw locatie-eigenschap hebt geconfigureerd, kunt u [een kaart toevoegen om de locatie in het dash board van het apparaat te visualiseren](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Opdrachten

Opdrachten worden gebruikt voor het extern beheren van een apparaat. Ze kunnen Opera tors gebruiken om opdrachten uit te voeren op het apparaat. U kunt meerdere opdrachten toevoegen aan de sjabloon voor het apparaat die als tegels worden weer gegeven op het tabblad **opdrachten** voor Opera tors die moeten worden gebruikt. Als de opbouw functie van het apparaat, hebt u de flexibiliteit om opdrachten te definiëren op basis van uw vereisten.

Wat is een andere opdracht dan een instelling?

- **Instelling**: Een instelling is een configuratie die u wilt Toep assen op een apparaat. U wilt dat het apparaat de configuratie persistent maken totdat u het wijzigt. U wilt bijvoorbeeld de Tempe ratuur van uw Vries instellen en u wilt die instelling zelfs wanneer de Vries opnieuw wordt gestart.

- **Opdracht**: U kunt opdrachten gebruiken om direct vanaf IoT Central een opdracht op het apparaat uit te voeren. Als een apparaat niet is verbonden, treedt er een time-out op in de opdracht. Stel dat u een apparaat opnieuw wilt opstarten.

U kunt bijvoorbeeld een nieuwe **echo** opdracht toevoegen door het tabblad **opdrachten** te selecteren, vervolgens **+ nieuwe opdracht**te selecteren en de nieuwe opdracht Details in te voeren:

| Weergavenaam  | Veldnaam | Standaardtime-out | Gegevenstype |
| --------------| -----------|---------------- | --------- |
| Opdracht Echo  | echo       |  30             | text      |

![Het formulier opdracht configureren met Details voor echo's](./media/howto-set-up-template/commandsecho1.png)

Nadat u **Opslaan**hebt geselecteerd, wordt de **echo** opdracht weer gegeven als een tegel en is deze klaar om te worden gebruikt vanaf de **device Explorer** wanneer uw echte apparaat verbinding maakt. De veld namen van de opdracht moeten overeenkomen met de namen van de eigenschappen in de bijbehorende apparaatcode, zodat de opdrachten kunnen worden uitgevoerd.

[Hier volgt een koppeling naar voorbeeld code C-apparaat.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Regels

Met regels kunnen Opera tors apparaten in bijna realtime bewaken. Regels roepen automatisch acties aan, zoals het verzenden van een e-mail bericht wanneer de regel wordt geactiveerd. Er is momenteel één type regel beschikbaar:

- Telemetrie- **regel**, die wordt geactiveerd wanneer de geselecteerde telemetrie van het apparaat een opgegeven drempel overschrijdt. Meer [informatie over regels voor](howto-create-telemetry-rules.md)telemetrie.

## <a name="dashboard"></a>Dashboard

Het dash board gaat naar een operator om informatie over een apparaat te bekijken. Als ontwerper voegt u tegels toe aan deze pagina om Opera tors te helpen begrijpen hoe het apparaat zich gedraagt. U kunt een groot aantal typen dashboard tegels toevoegen, zoals afbeelding, lijn diagram, staaf diagram, Key Performance Indicator (KPI), instellingen en eigenschappen, en label.

U kunt bijvoorbeeld een tegel met **instellingen en eigenschappen** toevoegen om een selectie van de huidige waarden van instellingen en eigenschappen weer te geven door het tabblad **dash board** en de tegel uit de bibliotheek te selecteren:

![Formulier Details van apparaat configureren met Details voor instellingen en eigenschappen](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Wanneer een operator nu het dash board weergeeft in de **device Explorer**, kunnen ze de tegel zien.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Een locatie meting in het dash board toevoegen

Als u een locatie meting hebt geconfigureerd, kunt u de locatie visualiseren met een kaart in het dash board van uw apparaat. Voor locatie metingen hebt u de optie om de locatie geschiedenis uit te zetten.

1. Ga naar het tabblad **dash board** .

1. Op het dash board van het apparaat selecteert u **kaart** uit de bibliotheek.

1. Geef de kaart een titel. In het volgende voor beeld wordt de **huidige locatie**van het apparaat van de titel. Kies vervolgens de locatie meting die u eerder hebt geconfigureerd op het tabblad **metingen** . In het volgende voor beeld wordt de meting van de **bedrijfs locatie** geselecteerd:

   ![Het formulier toewijzing configureren met Details voor titel en eigenschappen](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Selecteer **Opslaan**. Op de kaart tegel wordt nu de locatie weer gegeven die u hebt geselecteerd.

U kunt de grootte van de kaart tegel wijzigen. Wanneer een operator het dash board weergeeft in de **device Explorer**, zijn alle dashboard tegels die u hebt geconfigureerd, met inbegrip van een locatie kaart zichtbaar.

### <a name="add-a-location-property-in-the-dashboard"></a>Een locatie-eigenschap toevoegen in het dash board

Als u een locatie-eigenschap hebt geconfigureerd, kunt u de locatie visualiseren met een kaart in het dash board van uw apparaat.

1. Ga naar het tabblad **dash board** .

1. Op het dash board van het apparaat selecteert u **kaart** uit de bibliotheek.

1. Geef de kaart een titel. In het volgende voor beeld wordt de **huidige locatie**van het apparaat van de titel. Kies vervolgens de locatie-eigenschap die u eerder hebt geconfigureerd op het tabblad **Eigenschappen** . In het volgende voor beeld wordt de **locatie** voor de apparaatnaam geselecteerd:

   ![Kaart formulier configureren met Details voor titel en eigenschappen](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Selecteer **Opslaan**. Op de kaart tegel wordt nu de locatie weer gegeven die u hebt geselecteerd.

U kunt de grootte van de kaart tegel wijzigen. Wanneer een operator het dash board weergeeft in de **device Explorer**, zijn alle dashboard tegels die u hebt geconfigureerd, met inbegrip van een locatie kaart zichtbaar.

Zie [Dashboard tegels gebruiken](howto-use-tiles.md)voor meer informatie over het gebruik van tegels in azure IOT Central.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een sjabloon voor een apparaat instelt in uw Azure IoT Central-toepassing, kunt u het volgende doen:

- [Een nieuwe sjabloon versie voor een apparaat maken](howto-version-device-template.md)
- [Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing](howto-connect-devkit.md)
- [Een algemene client toepassing verbinden met uw Azure IoT Central-toepassing (node. js)](howto-connect-nodejs.md)
