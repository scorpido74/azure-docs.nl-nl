---
title: 'Zelf studie: een nieuw apparaattype definiëren in azure IoT Central'
description: Deze zelfstudie laat zien hoe u als bouwer een nieuw apparaattype kunt definiëren in uw Azure IoT Central-toepassing. U definieert de telemetrie, de status, de eigenschappen en de instellingen voor uw type.
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d986ddcf4b8eccfbd4ac1819f96157b5dfe6fe91
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705892"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Zelfstudie: Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Deze zelfstudie laat zien hoe u als bouwer een apparaatsjabloon kunt gebruiken om een nieuw apparaattype te definiëren in uw Microsoft Azure IoT Central-toepassing. Een apparaatsjabloon definieert de telemetrie, status, eigenschappen en instellingen voor uw apparaattype.

Om u in staat te stellen uw toepassing te testen voordat u een echt apparaat aansluit, genereert IoT Central een gesimuleerd apparaat op basis van de apparaatsjabloon wanneer u deze maakt.

In deze zelfstudie maakt u een apparaatsjabloon voor een **aangesloten airconditioner**. Een aangesloten airconditioningapparaat:

* Verzendt telemetrie zoals temperatuur en luchtvochtigheid.
* Rapporteert zijn status, bijvoorbeeld of het apparaat aan of uit is.
* Heeft apparaateigenschappen zoals de firmwareversie en het serienummer.
* Heeft instellingen zoals de doeltemperatuur.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe apparaatsjabloon maken
> * Telemetrie toevoegen aan uw apparaat
> * Gesimuleerde telemetrie bekijken
> * Gebeurtenismeting definiëren
> * Gesimuleerde gebeurtenissen bekijken
> * Statusmeting definiëren
> * Gesimuleerde status bekijken
> * Instellingen en eigenschappen gebruiken
> * Opdrachten gebruiken
> * Het gesimuleerde apparaat in het dashboard bekijken

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u een Azure IoT Central-toepassing nodig die is gemaakt op basis van de sjabloon voor de **aangepaste toepassing** . Als u geen toepassing hebt, voltooit u de Snelstartgids [een Azure IOT Central-toepassing maken](quick-deploy-iot-central.md) en zorgt u ervoor dat u de sjabloon voor **aangepaste toepassingen** kiest.

## <a name="create-a-device-template"></a>Een sjabloon voor een apparaat maken

Als bouwer kunt u de apparaatsjablonen in uw toepassing maken en bewerken. Wanneer u een apparaatsjabloon maakt, genereert Azure IoT Central een gesimuleerd apparaat op basis van de sjabloon. Het gesimuleerde apparaat genereert telemetrie waarmee u het gedrag van uw toepassing kunt testen voordat u een echt apparaat aansluit.

Als u een nieuwe apparaatsjabloon aan uw toepassing wilt toevoegen, gaat u naar de pagina **Apparaatsjablonen**. Hiertoe selecteert u de **Apparaatinstellingen** in het linkerdeel venster.

![De pagina Apparaatsjablonen](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Een apparaatsjabloon toevoegen

De volgende stappen laten zien hoe u een nieuwe apparaatsjabloon, **Verbonden airconditioner**, maakt voor apparaten die temperatuurtelemetrie naar uw toepassing verzenden:

1. Selecteer op de pagina **Apparaatinstellingen** de optie **+ Nieuw**:

    ![De pagina Apparaatsjablonen, Apparaatsjabloon maken](./media/tutorial-define-device-type/newtemplate.png)

2. Op de pagina ziet u de sjablonen waaruit u kunt kiezen.

    ![Bibliotheek met Apparaatbeheer](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Selecteer de **aangepaste**optie **aangesloten lucht voorwaarde** als de naam van de sjabloon voor het apparaat en selecteer vervolgens **maken**. U kunt ook een afbeelding van uw apparaat uploaden die zichtbaar is voor operators in de apparatenverkenner:

    ![Aangepast apparaat](./media/tutorial-define-device-type/createcustomdevice.png)

4. Ga naar het tabblad **Metingen** van de apparaatsjabloon **Aangesloten airconditioner**, waar u de telemetrie definieert. Elke apparaatsjabloon die u definieert, heeft afzonderlijke tabbladen waarmee u het volgende kunt doen:

   * De _metingen_ opgeven, zoals telemetrie, gebeurtenis en status, die door het apparaat worden verzonden.

   * De _instellingen_ definiëren die worden gebruikt om het apparaat te beheren.

   * De _eigenschappen_ definiëren die de metagegevens van het apparaat vormen.

   * De _opdrachten_ definiëren die rechtstreeks op het apparaat moeten worden uitgevoerd.

   * De _regels_ definiëren die u wilt koppelen aan het apparaat.

   * Het _dashboard_ van het apparaat aanpassen voor uw operators.

     ![Airconditionermetingen](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Als u de naam van de sjabloon wilt wijzigen, selecteert u de naam van de sjabloon boven aan de pagina.

5. Als u de meting van de telemetrische Tempe ratuur wilt toevoegen, selecteert u **+ nieuwe meting**. Kies vervolgens **Telemetrie** als het type meting:

    ![Metingen aangesloten airconditioner](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Elk type telemetrie dat u definieert voor een apparaatsjabloon bevat [configuratieopties](howto-set-up-template.md) zoals:

   * Weergaveopties.

   * Details van de telemetrie.

   * Simulatieparameters.

     Gebruik de informatie in de volgende tabel om uw **Temperatuur**-telemetrie te configureren:

     | Instelling              | Waarde         |
     | -------------------- | -----------   |
     | Weergavenaam         | Temperatuur   |
     | Veldnaam           | temperatuur   |
     | Eenheden                | F             |
     | Min.                  | 60            |
     | Max.                  | 110           |
     | Aantal decimalen       | 0             |

     U kunt ook een kleur kiezen voor de telemetrieweergave. Selecteer **Opslaan**om de definitie van de telemetrie op te slaan:

     ![Temperatuursimulatie configureren](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Na enige tijd ziet u op het tabblad **Metingen** een grafiek van de temperatuurtelemetrie van uw gesimuleerde aangesloten airconditioningapparaat. Gebruik de besturingselementen om de zichtbaarheid en aggregatie te beheren of de telemetriedefinitie te bewerken:
 
    > [!NOTE]
    > Voor telemetrie wordt **gemiddeld** ingesteld als de standaard aggregatie. 

    ![Temperatuursimulatie bekijken](./media/tutorial-define-device-type/viewsimulation.png)

8. U kunt het diagram ook aanpassen met behulp van de besturingselementen **Lijn** , **Gestapeld**en **Tijdsbereik bewerken**:

    ![De grafiek aanpassen](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Een gebeurtenismeting toevoegen

Gebruik gebeurtenissen voor het definiëren van point-in-time-gegevens die door het apparaat verzonden wanneer er een gebeurtenis plaatsvindt, zoals een fout of een storing van een onderdeel. Azure IoT Central kan apparaatgebeurtenissen simuleren, zodat u het gedrag van uw toepassing kunt testen voordat u een echt apparaat aansluit. U definieert gebeurtenismetingen voor uw apparaatsjabloon in de weergave **Metingen**.

1. Selecteer **+ nieuwe meting**om de meting van de fout gebeurtenis van de **ventilator motor** toe te voegen. Kies vervolgens **Gebeurtenis** als het type meting:

    ![Metingen aangesloten airconditioner](./media/tutorial-define-device-type/eventnew.png)

2. Elk type gebeurtenis dat u definieert voor een apparaatsjabloon bevat [configuratieopties](howto-set-up-template.md) zoals:

   * Weergavenaam.

   * Veldnaam.

   * Ernst.

     Gebruik de informatie in de volgende tabel om de gebeurtenis **Storing ventilatormotor** te configureren:

     | Instelling              | Waarde             |
     | -------------------- | -----------       |
     | Weergavenaam         | Storing ventilatormotor   |
     | Veldnaam           | storingventilatormotor       |
     | Ernst             | Fout             |

     Selecteer **Opslaan**om de gebeurtenis definitie op te slaan:

     ![Gebeurtenismeting configureren](./media/tutorial-define-device-type/eventconfiguration.png)

3. Na enige tijd ziet u op het tabblad **Metingen** een grafiek van de gebeurtenissen die willekeurig door uw gesimuleerde aangesloten airconditioningapparaat zijn gegenereerd. Gebruik de besturingselementen om de zichtbaarheid te beheren of de gebeurtenisdefinitie te bewerken:

    ![Gebeurtenissimulatie bekijken](./media/tutorial-define-device-type/eventview.png)

1. Als u meer informatie over de gebeurtenis wilt weer geven, selecteert u de gebeurtenis in de grafiek:

    ![Gebeurtenisdetails bekijken](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Een statusmeting definiëren

U kunt status gebruiken om de status van het apparaat of een onderdeel ervan in de loop van de tijd te definiëren en te visualiseren. Azure IoT Central kan een apparaatstatus simuleren, zodat u het gedrag van uw toepassing kunt testen voordat u een echt apparaat aansluit. U definieert statusmetingen voor uw apparaattype in de weergave **Metingen**.

1. Selecteer **+ nieuwe meting**om een status meting voor de **ventilator modus** toe te voegen. Kies vervolgens **Status** als het type meting:

    ![Statusmetingen aangesloten airconditioner](./media/tutorial-define-device-type/statenew.png)

2. Elk type status dat u definieert voor een apparaatsjabloon bevat [configuratieopties](howto-set-up-template.md) zoals:

   * Weergavenaam.

   * Veldnaam.

   * Waarden met optionele weergavelabels.

   * Kleur voor elke waarde.

     Gebruik de informatie in de volgende tabel om de status **Ventilatormodus** te configureren:

     | Instelling              | Waarde             |
     | -------------------- | -----------       |
     | Weergavenaam         | Ventilatormodus          |
     | Veldnaam           | ventilatormodus           |
     | Waarde                | 1                 |
     | Weergavelabel        | Actief         |
     | Waarde                | 0                 |
     | Weergavelabel        | Stopped           |

     Selecteer **Opslaan**om de definitie van de status meting op te slaan:

     ![Statusmeting configureren](./media/tutorial-define-device-type/stateconfiguration.png)

3. Na enige tijd ziet u op het tabblad **Metingen** een grafiek van de statuswaarden die willekeurig door uw gesimuleerde aangesloten airconditioningapparaat zijn gegenereerd. Gebruik de besturingselementen om de zichtbaarheid te beheren of de statusdefinitie te bewerken:

    ![Statussimulatie bekijken](./media/tutorial-define-device-type/stateview.png)

4. Als er binnen een korte tijdsduur te veel gegevenspunten door het apparaat worden verzonden, wordt de statusmeting weergegeven met een andere visualisatie. Selecteer de grafiek om alle gegevens punten binnen die tijds periode weer te geven in chronologische volg orde. U kunt ook het tijdsbereik verkleinen, zodat de metingen gedetailleerder worden weergegeven.

## <a name="settings-properties-and-commands"></a>Instellingen, eigenschappen en opdrachten

Instellingen, eigenschappen en opdrachten zijn verschillende waarden die worden gedefinieerd in een apparaatsjabloon en aan elk afzonderlijk apparaat worden gekoppeld:

* U gebruikt _instellingen_ om configuratiegegevens van uw toepassing naar een apparaat te verzenden. Een operator kan bijvoorbeeld een instelling gebruiken om het telemetrie-interval van het apparaat van twee seconden naar vijf seconden te wijzigen. Wanneer een operator een instelling wijzigt, wordt de instelling in de gebruikersinterface als in behandeling gemarkeerd totdat het apparaat reageert met een bevestiging.

* _Eigenschappen_ gebruikt u voor het definiëren van metagegevens die aan uw apparaat zijn gekoppeld. Er zijn twee categorieën eigenschappen:
    
  * _Toepassingseigenschappen_ gebruikt u om gegevens over het apparaat in uw toepassing vast te leggen. U kunt bijvoorbeeld toepassingseigenschappen gebruiken om de locatie van het apparaat en de laatste servicedatum vast te leggen. Deze eigenschappen worden opgeslagen in de toepassing en worden niet gesynchroniseerd met het apparaat. Een operator kan waarden toewijzen aan eigenschappen.

  * U gebruikt _apparaateigenschappen_  om een ​​apparaat in staat te stellen eigenschapswaarden naar uw toepassing te verzenden. Deze eigenschappen kunnen alleen worden bijgewerkt door het apparaat. Voor een operator zijn apparaateigenschappen alleen-lezen. In dit scenario met een aangesloten airconditioner zijn de firmwareversie en het serienummer van het apparaat apparaateigenschappen die door het apparaat zijn gemeld.
    
    Zie voor meer informatie [Eigenschappen](howto-set-up-template.md#properties) in de handleiding over het instellen van een apparaatsjabloon.

* U gebruikt _opdrachten_ om uw apparaat extern te beheren vanuit de toepassing. U kunt opdrachten rechtstreeks vanuit de cloud op het apparaat uitvoeren om de apparaten te beheren. Een operator kan bijvoorbeeld opdrachten uitvoeren zoals opnieuw opstarten om het apparaat onmiddellijk opnieuw op te starten.

## <a name="use-settings"></a>Instellingen gebruiken

U gebruikt *instellingen* om een operator in staat te stellen configuratiegegevens naar een apparaat te verzenden. In deze sectie voegt u een instelling toe aan uw apparaatsjabloon **Aangesloten airconditioner** waarmee een operator de doeltemperatuur van de aangesloten airconditioner kan instellen.

1. Ga naar het tabblad **Instellingen** van uw apparaatsjabloon **Aangesloten airconditioner**.

2. U kunt de instellingen maken van verschillende typen, zoals getallen of tekst. Selecteer **getal** om een instelling voor een getal toe te voegen aan uw apparaat.

3. Gebruik de informatie in de volgende tabel om uw instelling **Temperatuur instellen** te configureren:

    | Veld                | Waarde           |
    | -------------------- | -----------     |
    | Weergavenaam         | Temperatuur instellen |
    | Veldnaam           | temperatuurInstellen  |
    | Meeteenheid      | F               |
    | Aantal decimalen       | 1               |
    | Minimumwaarde        | 20              |
    | Maximumwaarde        | 200             |
    | Initiële waarde        | 80              |
    | Beschrijving          | De doeltemperatuur voor de airconditioner instellen |

    Selecteer vervolgens **Opslaan**:

    ![Stel instelling Temperatuur instellen configureren](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Wanneer het apparaat een instellingswijziging bevestigt, verandert de status van de instelling in **Gesynchroniseerd**.

4. U kunt de indeling van het tabblad **Instellingen**  aanpassen door instellingentegels te verplaatsen en een ander formaat te geven:

    ![De indeling van de instellingen aanpassen](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Eigenschappen gebruiken

U gebruikt *toepassingseigenschappen* om gegevens over uw apparaat in de toepassing op te slaan. In deze sectie gaat u toepassingseigenschappen toevoegen aan uw apparaatsjabloon **Aangesloten airconditioner** om de locatie van het apparaat en de laatste servicedatum op te slaan. Deze eigenschappen kunnen worden gewijzigd in de toepassing. Het apparaat meldt ook eigenschappen zoals het serienummer en de firmwareversie die alleen-lezen zijn in de toepassing.

1. Ga naar het tabblad **Eigenschappen** voor uw apparaatsjabloon **Aangesloten airconditioner**.

1. U kunt apparaateigenschappen maken van verschillende typen, zoals getallen of tekst. Kies **Locatie**om een locatie-eigenschap aan uw apparaatsjabloon toe te voegen. Gebruik de informatie in de volgende tabel om uw locatie-eigenschap te configureren:

    | Veld                | Waarde                |
    | -------------------- | -------------------- |
    | Weergavenaam         | Locatie             |
    | Veldnaam           | location             |
    | Initiële waarde        | Seattle, WA, Verenigde Staten          |
    | Beschrijving          | Locatie apparaat      |

    Laat andere velden op de standaardwaarde staan.

    ![De apparaateigenschappen configureren](./media/tutorial-define-device-type/configureproperties.png)

    Selecteer **Opslaan**.

1. Kies **Datum** om een eigenschap voor de laatste servicedatum aan uw apparaatsjabloon toe te voegen.

1. Gebruik de informatie in de volgende tabel om uw eigenschap Laatste servicedatum te configureren:

    | Veld                | Waarde                   |
    | -------------------- | ----------------------- |
    | Weergavenaam         | Laatste servicedatum       |
    | Veldnaam           | Servicedatum             |
    | Initiële waarde        | 1/1/2019                |
    | Beschrijving          | Laatste servicebeurt           |

    ![De apparaateigenschappen configureren](./media/tutorial-define-device-type/configureproperties2.png)

    Selecteer **Opslaan**.

1. U kunt de indeling van het tabblad **Eigenschappen**  aanpassen door eigenschapstegels te verplaatsen en het formaat ervan te wijzigen.

1. Kies **Apparaateigenschap** om een apparaateigenschap, zoals de firmwareversie, toe te voegen aan uw apparaatsjabloon.

1. Gebruik de informatie in de volgende tabel om uw firmwareversie te configureren:

    | Veld                | Waarde                   |
    | -------------------- | ----------------------- |
    | Weergavenaam         | Firmwareversie        |
    | Veldnaam           | firmwareversie         |
    | Gegevenstype            | tekst                    |
    | Beschrijving          | De firmwareversie van de airconditioner |

    ![Firmwareversie configureren](./media/tutorial-define-device-type/configureproperties3.png)

    Selecteer **Opslaan**.

1. Kies **Apparaateigenschap** om een apparaateigenschap, zoals een serienummer, toe te voegen aan uw apparaatsjabloon.

1. Gebruik de informatie in de volgende tabel om het serienummer te configureren:

    | Veld                | Waarde                   |
    | -------------------- | ----------------------- |
    | Weergavenaam         | Serienummer           |
    | Veldnaam           | Serienummer            |
    | Gegevenstype            | tekst                    |
    | Beschrijving          | Het serienummer van de airconditioner  |

    ![Het serienummer configureren](./media/tutorial-define-device-type/configureproperties4.png)

    Selecteer **Opslaan**.

    > [!NOTE]
    > De apparaateigenschap wordt van het apparaat naar de toepassing verzonden. De waarden voor de firmwareversie en het serienummer worden bijgewerkt wanneer uw fysieke apparaat verbinding maakt met IoT Central.

## <a name="use-commands"></a>Opdrachten gebruiken

U kunt _opdrachten_ gebruiken om een operator in te schakelen om opdrachten rechtstreeks op het apparaat uit te voeren. In deze sectie voegt u een opdracht toe aan de apparaatsjabloon **Aangesloten airconditioner** waarmee een operator een bepaald bericht kan doorgeven naar de aangesloten airconditioner.

1. Ga naar het tabblad **Opdrachten** van de apparaatsjabloon **Aangesloten airconditioner** om de sjabloon te bewerken.

1. Selecteer **+ nieuwe opdracht** om een opdracht aan uw apparaat toe te voegen en te beginnen met het configureren van de nieuwe opdracht.

1. Gebruik de informatie in de volgende tabel om de nieuwe opdracht te configureren:

    | Veld                | Waarde           |
    | -------------------- | -----------     |
    | Weergavenaam         | Opdracht Echo    |
    | Veldnaam           | echo            |
    | Standaardtime-out      | 30              |
    | Gegevenstype         | tekst            |
    | Beschrijving          | Apparaatopdracht  |  

    U kunt extra invoer waarden toevoegen aan de opdracht door **+** te selecteren voor **invoer velden**.

    ![Toevoegen van een instelling voorbereiden](./media/tutorial-define-device-type/commandsecho1.png)

     Selecteer **Opslaan**.

1. U kunt de indeling van het tabblad **Opdrachten** aanpassen door opdrachtentegels te verplaatsen en het formaat ervan te wijzigen.

## <a name="view-your-simulated-device"></a>Gesimuleerd apparaat bekijken

Nu u uw apparaatsjabloon **Aangesloten airconditioner** hebt gedefinieerd kunt u het **Dashboard** aanpassen om de metingen, instellingen en eigenschappen die u hebt gedefinieerd op te nemen. Vervolgens kunt u het dashboard bekijken als een operator:

1. Ga naar het tabblad **Dashboard** van uw apparaatsjabloon **Aangesloten airconditioner**.

1. Selecteer **lijn diagram** om het onderdeel aan het **dash board**toe te voegen.

1. Configureer het onderdeel **​​Lijndiagram** met behulp van de informatie in de volgende tabel:

    | Instelling      | Waarde       |
    | ------------ | ----------- |
    | Titel        | Temperatuur |
    | Tijdsbereik   | Afgelopen 30 minuten |
    | Maateenheden     | Tempe ratuur (Selecteer **zicht baarheid** naast **Tempe ratuur**) |

    ![Lijndiagraminstellingen](./media/tutorial-define-device-type/linechartsettings.png)

    Selecteer vervolgens **Opslaan**.

1. Selecteer het onderdeel **gebeurtenis geschiedenis** met behulp van de informatie in de volgende tabel:

    | Instelling      | Waarde       |
    | ------------ | ----------- |
    | Titel        | Gebeurtenissen ventilatormotor |
    | Tijdsbereik   | Afgelopen 30 minuten |
    | Maateenheden     | Fout in ventilator motor (Selecteer **zicht baarheid** naast **ventilator motor fout**) |

    ![Instellingen voor gebeurtenis grafieken](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Selecteer vervolgens **Opslaan**.

1. Configureer het onderdeel **Statusgeschiedenis** met behulp van de informatie in de volgende tabel:

    | Instelling      | Waarde       |
    | ------------ | ----------- |
    | Titel        | Ventilatormodus |
    | Tijdsbereik   | Afgelopen 30 minuten |
    | Maateenheden | Ventilator modus (Selecteer **zicht baarheid** naast **ventilator modus**) |

    ![Lijndiagraminstellingen](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Selecteer vervolgens **Opslaan**.

1. Kies **Instellingen en eigenschappen**  om de apparaatinstellingen en -eigenschappen aan het dashboard toe te voegen. Selecteer **toevoegen/verwijderen** om de instellingen of eigenschappen toe te voegen die u wilt zien in het dash board.

1. Configureer het onderdeel **Instellingen en eigenschappen** met behulp van de informatie in de volgende tabel:

    | Instelling                 | Waarde         |
    | ----------------------- | ------------- |
    | Titel                   | Apparaateigenschappen |
    | Instellingen en eigenschappen | Temperatuur instellen<br/>Serienummer<br/>Firmwareversie |

    Instellingen en eigenschappen die u eerder hebt gedefinieerd op de pagina **Instellingen en eigenschappen**, worden weergegeven bij **Beschikbare kolommen**.

    ![Instellingen voor de eigenschap Temperatuur instellen](./media/tutorial-define-device-type/propertysettings4.png)

    Selecteer vervolgens **Opslaan**.

1. U ziet nu gesimuleerde gegevens voor uw aangesloten airconditioner in het dashboard. U kunt de tegels en indeling voor het dashboard bewerken:

    ![Bekijk het dashboard](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Een nieuwe apparaatsjabloon maken
> * Telemetrie toevoegen aan uw apparaat
> * Gesimuleerde telemetrie bekijken
> * Apparaatgebeurtenissen definiëren
> * Gesimuleerde gebeurtenissen bekijken
> * Uw status definiëren
> * Gesimuleerde status bekijken
> * Instellingen en eigenschappen gebruiken
> * Opdrachten gebruiken
> * Het gesimuleerde apparaat in het dashboard bekijken

Nu u een apparaatsjabloon in uw Azure IoT Central-toepassing hebt gedefinieerd, volgen hier de aanbevolen volgende stappen:

* [Regels en acties voor uw apparaat configureren](tutorial-configure-rules.md)
* [De weergaven van de operator aanpassen](tutorial-customize-operator.md)