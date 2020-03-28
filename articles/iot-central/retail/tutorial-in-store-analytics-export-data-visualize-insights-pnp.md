---
title: Zelfstudie - Gegevens exporteren en inzichten visualiseren in Azure IoT Central
description: Leer in deze zelfstudie hoe u gegevens exporteert vanuit IoT Central en visualiseert inzichten in een Power BI-dashboard.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 9dcb185ab8375d46c75a12e6adaeeae2358c13ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022083"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Zelfstudie: Gegevens exporteren vanuit Azure IoT Central en inzichten visualiseren in Power BI



In de twee vorige zelfstudies hebt u een IoT Central-toepassing gemaakt en aangepast met behulp van de **in-store-analyse - sjabloon voor de kassatoepassing.** In deze zelfstudie configureert u uw IoT Central-toepassing om telemetrie te exporteren die is verzameld vanaf de apparaten. Vervolgens gebruikt u Power BI om een aangepast dashboard voor de winkelmanager te maken om de inzichten uit de telemetrie te visualiseren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Configureer een IoT Central-toepassing om telemetrie naar een gebeurtenishub te exporteren.
> * Gebruik Logische apps om gegevens van een gebeurtenishub naar een Power BI-streaminggegevensset te verzenden.
> * Maak een Power BI-dashboard om gegevens in de streaminggegevensset te visualiseren.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Als u de vorige twee zelfstudies wilt voltooien, [maakt u een analysetoepassing in de winkel in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) en past u het [operatordashboard aan en beheert u apparaten in Azure IoT Central.](./tutorial-in-store-analytics-customize-dashboard-pnp.md)
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* Een Power BI-account. Als je geen Power BI-account hebt, meld je je aan voor een [gratis Power BI Pro-proefversie](https://app.powerbi.com/signupredirect?pbi_source=web) voordat je begint.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw gebeurtenishub en logische app maakt, moet u een resourcegroep maken om ze te beheren. De resourcegroep moet zich op dezelfde locatie bevinden als uw **In-store analytics - checkout** IoT Central-toepassing. Een resourcegroep maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **resourcegroepen**in de linkernavigatie . Selecteer vervolgens **Toevoegen**.
1. Selecteer **voor Abonnement**de naam van het Azure-abonnement dat u hebt gebruikt om uw IoT Central-toepassing te maken.
1. Voer voor de naam van de **resourcegroep** _winkelanalyse_*.
1. Selecteer voor de **regio**dezelfde regio die u hebt gekozen voor de IoT Central-toepassing.
1. Selecteer **Controleren + maken**.
1. Selecteer op de pagina **Controleren + maken** de optie **Maken**.

U hebt nu een resourcegroep genaamd **retail-store-analyse** in uw abonnement.

## <a name="create-an-event-hub"></a>Een Event Hub maken

Voordat u de retailmonitoringtoepassing configureren om telemetrie te exporteren, moet u een gebeurtenishub maken om de geëxporteerde gegevens te ontvangen. In de volgende stappen ziet u hoe u uw gebeurtenishub maakt:

1. Selecteer in de Azure-portal **een resource** maken linksboven in het scherm.
1. Voer in **Search the Marketplace** _gebeurtenishubs_in en druk op **Enter**.
1. Selecteer op de pagina **Gebeurtenishubs** de optie **Maken**.
1. Ga op de pagina **Naamruimte maken** de volgende stappen uit:
    * Voer een unieke naam in voor de naamruimte, zoals _yourname-retail-store-analyse._ Het systeem controleert of deze naam beschikbaar is.
    * Kies de prijscategorie **Basis.**
    * Selecteer hetzelfde **abonnement dat** u hebt gebruikt om uw IoT Central-toepassing te maken.
    * Selecteer de brongroep **voor winkelanalyse.**
    * Selecteer dezelfde locatie die u hebt gebruikt voor uw IoT Central-toepassing.
    * Selecteer **Maken**. Het kan zijn dat u een paar minuten moet wachten tot het systeem de resources indient.
1. Navigeer in de portal naar de brongroep **voor winkelanalyse.** Wacht totdat de installatie is voltooid. Mogelijk moet u **Vernieuwen** selecteren om de implementatiestatus bij te werken. U ook de status controleren van de naamruimte creatie van de gebeurtenishub in de **meldingen**.
1. Selecteer in de brongroep **voor winkelanalyse** de **naamruimte gebeurtenishubs**. U ziet de startpagina voor de **naamruimte van** uw gebeurtenishubs in de portal.

Nu u een **Naamruimte voor gebeurtenishubs**hebt, u een **gebeurtenishub** maken die u gebruiken met uw IoT Central-toepassing:

1. Selecteer **+ Event Hub**op de startpagina voor de naamruimte van uw **gebeurtenishubs** in de portal.
1. Voer op de pagina **Gebeurtenishub maken** _de naam store-telemetrie_ in en selecteer **Vervolgens Maken**.

U hebt nu een gebeurtenishub die u gebruiken wanneer u gegevensexport configureert vanuit uw IoT Central-toepassing:

![Event Hub](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Gegevensexporteren configureren

Nu u een gebeurtenishub hebt, u uw **in-store-analyses** configureren - kassatoepassing om telemetrie van de verbonden apparaten te exporteren. In de volgende stappen ziet u hoe u de export configureert:

1. Meld u aan bij uw **In-store analytics - checkout** IoT Central-toepassing.
1. Selecteer **Gegevens exporteren** in het linkerdeelvenster.
1. Selecteer **Nieuwe > Azure-gebeurtenishubs**.
1. Voer _Telemetrie-export in_ als **weergavenaam**.
1. Selecteer de **naamruimte van uw gebeurtenishubs**.
1. Selecteer de **gebeurtenishub store-telemetrie.**
1. Schakel **apparaten** en **apparaatsjablonen** uit in de sectie **Gegevens om te exporteren.**
1. Selecteer **Opslaan**.

Het kan enkele minuten duren voordat de gegevens worden geëxporteerd voordat telemetrie naar uw gebeurtenishub wordt verzonden. U de status van de export bekijken op de pagina **Gegevensexport:**

![Configuratie van continue gegevensexport](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>De Power BI-gegevenssets maken

Op uw Power BI-dashboard worden gegevens van uw retailmonitoringtoepassing weergegeven. In deze oplossing gebruikt u Power BI-streaminggegevenssets als gegevensbron voor het Power BI-dashboard. In deze sectie definieert u het schema van de streaminggegevenssets, zodat de logische app gegevens kan doorsturen vanuit de gebeurtenishub. In de volgende stappen ziet u hoe u twee streamingdatasets maakt voor de omgevingssensoren en één streaminggegevensset voor de bezettingssensor:

1. Meld u aan bij uw **Power BI**-account.
1. Selecteer **Werkruimten**en selecteer **Vervolgens Een werkruimte maken**.
1. Voer op de pagina **Een werkruimte maken** in _In-store analytics in - afrekenen_ als de **naam Werkruimte**.
1. Schuif naar de onderkant van de **pagina Welkom naar de pagina In-store analytics - werkruimte voor afrekenen** en selecteer **Overslaan**.
1. Selecteer op de werkruimtepagina **de optie > streaming-gegevensset maken**.
1. Kies **API**op de pagina **Nieuwe streaminggegevensset** en selecteer **Volgende**.
1. Voer _zone 1-sensor_ in als de **naam gegevensset**.
1. Voer de drie **waarden uit stream** in de volgende tabel in:

    | Waardenaam  | Waardetype |
    | ----------- | ---------- |
    | Tijdstempel   | DateTime   |
    | Vochtigheid    | Aantal     |
    | Temperatuur | Aantal     |

1. Historische **gegevensanalyse** inschakelen.
1. Selecteer **Maken** en vervolgens **Gereed**.
1. Maak een andere streaming dataset genaamd **Zone 2 sensor** met hetzelfde schema en instellingen als de Zone 1 **sensor** streaming dataset.

Je hebt nu twee streaming datasets. De logische app stuurt telemetrie van de twee omgevingssensoren die zijn aangesloten op uw **in-store analytics - checkout-toepassing** naar deze twee gegevenssets:

![Zonegegevenssets](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

Deze oplossing maakt gebruik van één streamingdataset voor elke sensor omdat het niet mogelijk is om filters toe te passen op streaminggegevens in Power BI.

Je hebt ook een streaming dataset nodig voor de bezetting telemetrie:

1. Selecteer op de werkruimtepagina **de optie > streaming-gegevensset maken**.
1. Kies **API**op de pagina **Nieuwe streaminggegevensset** en selecteer **Volgende**.
1. Voer _de bezettingssensor_ in als de **naam Gegevensset**.
1. Voer de vijf **waarden uit stream** in de volgende tabel in:

    | Waardenaam     | Waardetype |
    | -------------- | ---------- |
    | Tijdstempel      | DateTime   |
    | Wachtrijlengte 1 | Aantal     |
    | Wachtrijlengte 2 | Aantal     |
    | Stilstaan tijd 1   | Aantal     |
    | Stilstaan Tijd 2   | Aantal     |

1. Historische **gegevensanalyse** inschakelen.
1. Selecteer **Maken** en vervolgens **Gereed**.

Je hebt nu een derde streaming dataset die waarden van de gesimuleerde bezettingssensor opslaat. Deze sensor rapporteert de wachtrijlengte bij de twee kassa's in de winkel en hoe lang klanten in deze wachtrijen wachten:

![Gegevensset van bezetting](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Een logische app maken

In deze oplossing leest de logische app telemetrie van de gebeurtenishub, ontleedt de gegevens en stuurt deze vervolgens naar de Power BI-streaminggegevenssets die u hebt gemaakt.

Voordat u de logische app maakt, hebt u de apparaat-iD's nodig van de twee RuuviTag-sensoren die u hebt aangesloten op uw IoT Central-toepassing in de analysetoepassing Maken in de winkel in de zelfstudie [van Azure IoT Central:](./tutorial-in-store-analytics-create-app-pnp.md)

1. Meld u aan bij uw **In-store analytics - checkout** IoT Central-toepassing.
1. Selecteer **Apparaten** in het linkerdeelvenster. Selecteer vervolgens **RuuviTag**.
1. Noteer de **apparaat-id's**. In de volgende screenshot, de iDs zijn **f5dcf4ac32e8** en **e29ffc8d5326:**

    ![Apparaat-iD's](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

In de volgende stappen ziet u hoe u de logische app maakt in de Azure-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Een resource** maken linksboven in het scherm.
1. Voer in **De Marktplaats zoeken**de _logica-app_in en druk op **Enter**.
1. Selecteer **op** de pagina Logic App de optie **Maken**.
1. Maak op de pagina **Logic App:**
    * Voer een unieke naam in voor uw logische app, zoals _yourname-retail-store-analyse._
    * Selecteer hetzelfde **abonnement dat** u hebt gebruikt om uw IoT Central-toepassing te maken.
    * Selecteer de brongroep **voor winkelanalyse.**
    * Selecteer dezelfde locatie die u hebt gebruikt voor uw IoT Central-toepassing.
    * Selecteer **Maken**. Het kan zijn dat u een paar minuten moet wachten tot het systeem de resources indient.
1. Navigeer in de Azure-portal naar uw nieuwe logica-app.
1. Schuif op de pagina **Logic Apps Designer** omlaag en selecteer Blank Logic **App**.
1. Voer in **Zoekconnectors en -triggers** _gebeurtenishubs_in .
1. Selecteer in **Triggers**wanneer **gebeurtenissen beschikbaar zijn in gebeurtenishub**.
1. Voer _Telemetrie opslaan_ in als **de naam Verbinding**en selecteer de **naamruimte van gebeurtenishubs**.
1. Selecteer het beleid **rootbeheerSharedAccess** en selecteer **Maken**.
1. In de actie Wanneer zijn gebeurtenissen beschikbaar in de actie **Gebeurtenishub:**
    * Selecteer in **de naam van gebeurtenishub** **winkeltelemetrie**.
    * Selecteer **toepassing/json**in **Inhoudstype**.
    * Het **interval instellen** op drie en de **frequentie** op seconden
1. Selecteer **Opslaan** om uw logica-app op te slaan.

Als u de logica wilt toevoegen aan het ontwerp van uw logische app, selecteert u **De codeweergave:**

1. Vervang `"actions": {},` door de volgende JSON. Vervang de twee `[YOUR RUUVITAG DEVICE ID 1]` `[YOUR RUUVITAG DEVICE ID 2]` tijdelijke aanduidingen en door de iD's die u hebt opgemerkt van uw twee RuuviTag-apparaten:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Selecteer **Opslaan** en selecteer **Vervolgens Ontwerper** om de visuele versie van de logica die u hebt toegevoegd, te bekijken:

    ![Ontwerp van logische apps](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. Selecteer **Switch by DeviceID** om de actie uit te vouwen. Selecteer vervolgens **zone 1-omgeving**en selecteer **Een actie toevoegen**.
1. Voer in **Zoekconnectors en -acties** **Power BI**in en druk op **Enter**.
1. Selecteer de actie **Rijen toevoegen aan een gegevensset (voorbeeld).**
1. Selecteer **Aanmelden** en volg de aanwijzingen om u aan te melden bij uw Power BI-account.
1. Nadat het aanmeldingsproces is voltooid, in de actie **Rijen toevoegen aan een gegevensset:**
    * Selecteer **In-store analytics - afreken en kassa** als werkruimte.
    * Selecteer **Zone 1-sensor** als gegevensset.
    * Selecteer **RealTimeData** als tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel,** **Vochtigheid**en **Temperatuur.**
    * Selecteer het veld **Tijdstempel** en selecteer **vervolgens x-opt-enqueuedtime** in de lijst **Dynamische inhoud.**
    * Selecteer het **veld Vochtigheid** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **vochtigheid**.
    * Selecteer het veld **Temperatuur** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **temperatuur**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De **zone 1-omgevingsactie** ziet ![eruit als de volgende schermafbeelding: Zone 1-omgeving](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. Selecteer de **omgevingsactie Zone 2** en selecteer **Een actie toevoegen**.
1. Voer in **Zoekconnectors en -acties** **Power BI**in en druk op **Enter**.
1. Selecteer de actie **Rijen toevoegen aan een gegevensset (voorbeeld).**
1. Ga in de actie **Rijen toevoegen aan een gegevensset 2:**
    * Selecteer **In-store analytics - afreken en kassa** als werkruimte.
    * Selecteer **Zone 2-sensor** als gegevensset.
    * Selecteer **RealTimeData** als tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel,** **Vochtigheid**en **Temperatuur.**
    * Selecteer het veld **Tijdstempel** en selecteer **vervolgens x-opt-enqueuedtime** in de lijst **Dynamische inhoud.**
    * Selecteer het **veld Vochtigheid** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **vochtigheid**.
    * Selecteer het veld **Temperatuur** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **temperatuur**.
    Selecteer **Opslaan** om uw wijzigingen op te slaan.  De **zone 2-omgevingsactie** ziet ![eruit als de volgende schermafbeelding: Zone 2-omgeving](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. Selecteer de actie **Bezetting** en selecteer vervolgens de actie **Switch by Interface ID.**
1. Selecteer de **interfaceactie Stilstaantijd** en selecteer **Een actie toevoegen**.
1. Voer in **Zoekconnectors en -acties** **Power BI**in en druk op **Enter**.
1. Selecteer de actie **Rijen toevoegen aan een gegevensset (voorbeeld).**
1. Ga in de actie **Rijen toevoegen aan een gegevensset:**
    * Selecteer **In-store analytics - afreken en kassa** als werkruimte.
    * Selecteer **Bezettingssensor** als gegevensset.
    * Selecteer **RealTimeData** als tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel,** **Stilstaantijd 1**en **Stilstaantijd 2.**
    * Selecteer het veld **Tijdstempel** en selecteer **vervolgens x-opt-enqueuedtime** in de lijst **Dynamische inhoud.**
    * Selecteer het veld **Afbeeldingstijd 1** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **DwellTime1**.
    * Selecteer het veld **Afbeeldingstijd 2** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **DwellTime2**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De interfaceactie **Van De Tijd van de dwell** kijkt als de volgende screenshot: ![De actie van de bezettingsgraad](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. Selecteer de **interfaceactie Tellen personen** en selecteer **Een actie toevoegen**.
1. Voer in **Zoekconnectors en -acties** **Power BI**in en druk op **Enter**.
1. Selecteer de actie **Rijen toevoegen aan een gegevensset (voorbeeld).**
1. Ga in de actie **Rijen toevoegen aan een gegevensset:**
    * Selecteer **In-store analytics - afreken en kassa** als werkruimte.
    * Selecteer **Bezettingssensor** als gegevensset.
    * Selecteer **RealTimeData** als tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel,** **Wachtrijlengte 1**en **Wachtrijlengte 2.**
    * Selecteer het veld **Tijdstempel** en selecteer **vervolgens x-opt-enqueuedtime** in de lijst **Dynamische inhoud.**
    * Selecteer het veld **Wachtrijlengte 1** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **count1**.
    * Selecteer het veld **Wachtrijlengte 2** en selecteer **Meer weergeven** naast **Parse Telemetrie**. Selecteer vervolgens **count2**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De **interfaceactie People Count** ziet ![eruit als de volgende schermafbeelding: Bezettingsactie](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

De logische app wordt automatisch uitgevoerd. Als u de status van elke run wilt zien, navigeert u naar de pagina **Overzicht** voor de logische app in de Azure-portal:

## <a name="create-a-power-bi-dashboard"></a>Een Power BI-dashboard maken

Nu heb je telemetrie die vanuit je IoT Central-toepassing via je eventhub stroomt. Vervolgens ontleden uw logische app de berichten van de gebeurtenishub en voegt deze toe aan een Power BI-streaminggegevensset. U nu een Power BI-dashboard maken om de telemetrie te visualiseren:

1. Meld u aan bij uw **Power BI**-account.
1. Selecteer **werkruimten > in-store analytics - checkout**.
1. Selecteer **> Dashboard maken**.
1. Voer **Winkelanalyses** in als de naam van het dashboard en selecteer **Maken**.

### <a name="add-line-charts"></a>Lijndiagrammen toevoegen

Voeg vier lijndiagramtegels toe om de temperatuur en vochtigheid van de twee omgevingssensoren weer te geven. Gebruik de informatie in de volgende tabel om de tegels te maken. Als u elke tegel wilt toevoegen, selecteert u **... (Meer opties) > Tegel toevoegen**. Selecteer **Aangepaste streaminggegevens**en selecteer **Volgende:**

| Instelling | Grafiek #1 | Grafiek #2 | Grafiek#3 | Grafiek #4 |
| ------- | -------- | -------- | -------- | -------- |
| Gegevensset | Zone 1-sensor | Zone 1-sensor | Zone 2-sensor | Zone 2-sensor |
| Visualisatietype | Lijndiagram | Lijndiagram | Lijndiagram | Lijndiagram |
| As | Tijdstempel | Tijdstempel | Tijdstempel | Tijdstempel |
| Waarden | Temperatuur | Vochtigheid | Temperatuur | Vochtigheid |
| Tijdvenster | 60 minuten | 60 minuten | 60 minuten | 60 minuten |
| Titel | Temperatuur (1 uur) | Vochtigheid (1 uur) | Temperatuur (1 uur) | Vochtigheid (1 uur) |
| Subtitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

De volgende schermafbeelding toont de instellingen voor de eerste grafiek:

![Lijndiagraminstellingen](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Kaarten toevoegen om omgevingsgegevens weer te geven

Voeg vier kaarttegels toe om de meest recente temperatuur- en vochtigheidswaarden van de twee omgevingssensoren weer te geven. Gebruik de informatie in de volgende tabel om de tegels te maken. Als u elke tegel wilt toevoegen, selecteert u **... (Meer opties) > Tegel toevoegen**. Selecteer **Aangepaste streaminggegevens**en selecteer **Volgende:**

| Instelling | Kaart #1 | Kaart #2 | Kaart#3 | Kaart#4 |
| ------- | ------- | ------- | ------- | ------- |
| Gegevensset | Zone 1-sensor | Zone 1-sensor | Zone 2-sensor | Zone 2-sensor |
| Visualisatietype | Kaart | Kaart | Kaart | Kaart |
| Velden | Temperatuur | Vochtigheid | Temperatuur | Vochtigheid |
| Titel | Temperatuur (F) | Vochtigheid (%) | Temperatuur (F) | Vochtigheid (%) |
| Subtitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

De volgende schermafbeelding toont de instellingen voor de eerste kaart:

![Kaartinstellingen](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Tegels toevoegen om de gegevens over de afhandelingsbezetting weer te geven

Voeg vier kaarttegels toe om de wachtrijlengte en de tijd voor de twee kassa's in de winkel weer te geven. Gebruik de informatie in de volgende tabel om de tegels te maken. Als u elke tegel wilt toevoegen, selecteert u **... (Meer opties) > Tegel toevoegen**. Selecteer **Aangepaste streaminggegevens**en selecteer **Volgende:**

| Instelling | Kaart #1 | Kaart #2 | Kaart#3 | Kaart#4 |
| ------- | ------- | ------- | ------- | ------- |
| Gegevensset | Bezettingssensor | Bezettingssensor | Bezettingssensor | Bezettingssensor |
| Visualisatietype | Gegroepeerd kolomdiagram | Gegroepeerd kolomdiagram | Meter | Meter |
| As    | Tijdstempel | Tijdstempel | N.v.t. | N.v.t. |
| Waarde | Stilstaan tijd 1 | Stilstaan Tijd 2 | Wachtrijlengte 1 | Wachtrijlengte 2 |
| Tijdvenster | 60 minuten | 60 minuten |  N.v.t. | N.v.t. |
| Titel | Stilstaan tijd | Stilstaan tijd | Wachtrijlengte | Wachtrijlengte |
| Subtitel | Afrekenen 1 | Afrekenen 2 | Afrekenen 1 | Afrekenen 2 |

Het formaat wijzigen en de tegels op uw dashboard opnieuw rangschikken om er zo uit te zien als de volgende schermafbeelding:

![Power BI-dashboard](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

U nog wat extra grafische bronnen toevoegen om het dashboard verder aan te passen:

![Power BI-dashboard](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met uw IoT Central-toepassing, u deze verwijderen door u aan te melden bij de toepassing en te navigeren naar de pagina **Toepassingsinstellingen** in de sectie **Beheer.**

Als u de toepassing wilt behouden, maar de kosten die eraan verbonden zijn wilt verlagen, schakelt u de gegevensexport uit die telemetrie naar uw gebeurtenishub verzendt.

U de gebeurtenishub en logische app in de Azure-portal verwijderen door de brongroep genaamd **retail-store-analyse**te verwijderen.

U uw Power BI-gegevenssets en -dashboard verwijderen door de werkruimte te verwijderen van de power bi-instellingenpagina voor de werkruimte.

## <a name="next-steps"></a>Volgende stappen

Deze drie tutorials hebben je een end-to-end oplossing laten zien die gebruik maakt van de **In-store analytics - checkout** IoT Central applicatie sjabloon. U hebt apparaten aangesloten op de toepassing, IoT Central gebruikt om de apparaten te controleren en Power BI gebruikt om een dashboard te bouwen om inzichten van de telemetrie van het apparaat weer te geven. Een aanbevolen volgende stap is het verkennen van een van de andere IoT Central-toepassingssjablonen:

> [!div class="nextstepaction"]
> * [Oplossingen voor de energiesector bouwen met IoT Central](../energy/overview-iot-central-energy.md)
> * [Oplossingen voor de overheid bouwen met IoT Central](../government/overview-iot-central-government.md)
> * [Oplossingen voor de gezondheidszorg bouwen met IoT Central](../healthcare/overview-iot-central-healthcare.md)
