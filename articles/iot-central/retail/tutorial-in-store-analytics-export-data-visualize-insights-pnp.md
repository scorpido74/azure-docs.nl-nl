---
title: 'Zelf studie: gegevens exporteren en inzichten visualiseren in azure IoT Central'
description: In deze zelf studie leert u hoe u gegevens exporteert uit IoT Central en inzichten kunt visualiseren in een Power BI dash board.
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
ms.openlocfilehash: b85dd8d899a7e5d7d9f9d41ad7e2872249ee29c5
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702006"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Zelf studie: gegevens exporteren uit Azure IoT Central en inzichten in Power BI visualiseren

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In de twee voor gaande zelf studies hebt u een IoT Central-toepassing gemaakt en aangepast met behulp van de toepassings sjabloon **in de Store Analytics-Checker** . In deze zelf studie configureert u uw IoT Central-toepassing voor het exporteren van telemetriegegevens die van de apparaten zijn verzameld. Vervolgens gebruikt u Power BI voor het maken van een aangepast dash board voor de Store Manager voor het visualiseren van de inzichten die zijn afgeleid van de telemetrie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Configureer een IoT Central-toepassing om telemetrie te exporteren naar een Event Hub.
> * Gebruik Logic Apps om gegevens te verzenden van een Event Hub naar een dataset van Power BI stream.
> * Maak een Power BI-dash board om gegevens in de streaming-gegevensset te visualiseren.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Voor het volt ooien van de vorige twee zelf studies [maakt u een in-Store Analytics-toepassing in azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) en [past u het operator dashboard aan en beheert u apparaten in azure IOT Central](./tutorial-in-store-analytics-customize-dashboard-pnp.md).
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* Een Power BI-account. Als u geen Power BI account hebt, meldt u zich aan voor een [gratis proef versie van Power bi Pro](https://app.powerbi.com/signupredirect?pbi_source=web) voordat u begint.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw Event Hub en logische app maakt, moet u een resource groep maken om deze te beheren. De resource groep moet zich op dezelfde locatie bezien als uw **in-Store Analytics-kassa IOT Central-** toepassing. Een resourcegroep maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in het navigatie venster links **resource groepen**. Selecteer vervolgens **Toevoegen**.
1. Voor het **abonnement**selecteert u de naam van het Azure-abonnement dat u hebt gebruikt om uw IOT Central-toepassing te maken.
1. Voer _Retail-Store-Analysis_* in voor de naam van de **resource groep** .
1. Voor de **regio**selecteert u de regio die u voor de IOT Central toepassing hebt gekozen.
1. Selecteer **Controleren + maken**.
1. Selecteer op de pagina **Controleren + maken** de optie **Maken**.

U hebt nu een resource groep met de naam **Retail-Store-Analysis** in uw abonnement.

## <a name="create-an-event-hub"></a>Een Event Hub maken

Voordat u de app voor het controleren van de detail handel kunt configureren voor het exporteren van telemetrie, moet u een Event Hub maken om de geëxporteerde gegevens te ontvangen. De volgende stappen laten zien hoe u uw Event Hub maakt:

1. Selecteer in de Azure Portal **een resource maken** in de linkerbovenhoek van het scherm.
1. Voer _Event hubs_in **de Marketplace zoeken**in en druk op **Enter**.
1. Selecteer op de pagina **Event hubs** **maken**.
1. Voer op de pagina **naam ruimte maken** de volgende stappen uit:
    * Voer een unieke naam in voor de naam ruimte, zoals _yournaam-Retail-Store-Analysis_. Het systeem controleert of deze naam beschikbaar is.
    * Kies de prijs categorie **Basic** .
    * Selecteer hetzelfde **abonnement** dat u hebt gebruikt om uw IOT Central-toepassing te maken.
    * Selecteer de resource groep **Retail-Store-Analysis** .
    * Selecteer de locatie die u hebt gebruikt voor uw IoT Central-toepassing.
    * Selecteer **Maken**. Mogelijk moet u enkele minuten wachten totdat het systeem de resources heeft ingericht.
1. Navigeer in de portal naar de resource groep **Retail-Store-Analysis** . Wacht tot de implementatie is voltooid. Mogelijk moet u **vernieuwen** selecteren om de implementatie status bij te werken. U kunt ook de status van het maken van de Event Hub naam ruimte in de **meldingen**controleren.
1. Selecteer in de resource groep **Retail-Store-analyse** de **Event hubs naam ruimte**. U ziet de start pagina voor uw **Event hubs naam ruimte** in de portal.

Nu u een **Event hubs naam ruimte**hebt, kunt u een **Event hub** maken voor gebruik met uw IOT Central-toepassing:

1. Selecteer op de start pagina voor uw **Event hubs-naam ruimte** in de portal **+ Event hub**.
1. Voer op de pagina **Event hub maken** de optie _Store-telemetrie_ in als de naam en selecteer vervolgens **maken**.

U hebt nu een Event Hub u kunt gebruiken bij het configureren van gegevens export vanuit uw IoT Central-toepassing:

![Event Hub](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Gegevens export configureren

Nu u een Event Hub hebt, kunt u uw **in-Store-toepassing voor uitchecken** configureren voor het exporteren van telemetrie van de verbonden apparaten. De volgende stappen laten zien hoe u de export configureert:

1. Meld u aan bij uw **in-Store Analytics-kassa IOT Central-** toepassing.
1. Selecteer **gegevens export** in het linkerdeel venster.
1. Selecteer **nieuw > Azure Event hubs**.
1. Voer een _telemetrie-export_ in als **weergave naam**.
1. Selecteer uw **Event hubs naam ruimte**.
1. Selecteer de Event Hub **Store-telemetrie** .
1. Schakel **apparaten** en **Apparaatinstellingen** uit in de sectie **gegevens die moeten worden geëxporteerd** .
1. Selecteer **Opslaan**.

Het kan enkele minuten duren voordat het exporteren van een telemetrie naar uw Event Hub wordt verzonden. U kunt de status van de export zien op de pagina **gegevens export** :

![Configuratie continue gegevens export](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>De Power BI gegevens sets maken

Uw Power BI-dash board geeft gegevens weer uit uw toepassing voor retail bewaking. In deze oplossing gebruikt u Power BI streaming-gegevens sets voor het Power BI dash board. In deze sectie definieert u het schema van de streaming-gegevens sets, zodat de logische app gegevens van de Event Hub kan door sturen. De volgende stappen laten zien hoe u twee streaming-gegevens sets voor de omgevings sensoren en één streaming-gegevensset voor de bezetting sensor maakt:

1. Meld u aan bij uw **Power BI**-account.
1. Selecteer **werk ruimten**en selecteer vervolgens **een werk ruimte maken**.
1. Voer op de pagina **een werk ruimte maken** de _in-Store Analytics in_ als de **naam van de werk ruimte**.
1. Ga naar de onderkant van de **Welkom bij de pagina in-Store analyse werk ruimte** en selecteer **overs Laan**.
1. Selecteer op de pagina werk ruimte **> streaming-gegevensset maken**.
1. Kies op de pagina **nieuwe streaming-gegevensset** de optie **API**en selecteer vervolgens **volgende**.
1. Voer _zone 1 sensor_ in als de naam van de **gegevensset**.
1. Voer in de volgende tabel de drie **waarden van de stream** in:

    | Waardenaam  | Waardetype |
    | ----------- | ---------- |
    | Tijdstempel   | Datum/tijd   |
    | Vochtigheid    | Aantal     |
    | Temperatuur | Aantal     |

1. Schakel over op de **analyse van historische gegevens** .
1. Selecteer **maken** en vervolgens **klaar**.
1. Maak een andere streaming-gegevensset met de naam **zone 2 sensor** met dezelfde schema's en instellingen als de **zone 1 sensor** streaming-gegevensset.

U hebt nu twee streaming-gegevens sets. De logische app stuurt telemetrie van de twee omgevings Sens oren die zijn verbonden met uw **in-Store-toepassing voor uitchecken** van deze twee gegevens sets:

![Zone gegevens sets](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

Deze oplossing gebruikt één streaming-gegevensset voor elke sensor, omdat het niet mogelijk is om filters toe te passen op streaminggegevens in Power BI.

U hebt ook een streaming-gegevensset nodig voor de telemetrie van de bezetting:

1. Selecteer op de pagina werk ruimte **> streaming-gegevensset maken**.
1. Kies op de pagina **nieuwe streaming-gegevensset** de optie **API**en selecteer vervolgens **volgende**.
1. Voer een _bezettings sensor_ in als de naam van de **gegevensset**.
1. Voer in de volgende tabel de vijf **waarden in van de stroom** :

    | Waardenaam     | Waardetype |
    | -------------- | ---------- |
    | Tijdstempel      | Datum/tijd   |
    | Wachtrij lengte 1 | Aantal     |
    | Wachtrij lengte 2 | Aantal     |
    | Woning tijd 1   | Aantal     |
    | Woning tijd 2   | Aantal     |

1. Schakel over op de **analyse van historische gegevens** .
1. Selecteer **maken** en vervolgens **klaar**.

U hebt nu een derde streaming-gegevensset die waarden van de gesimuleerde bezetting sensor opslaat. Deze sensor rapporteert de lengte van de wachtrij bij de twee uitchecks in de Store en hoe lang klanten in deze wacht rijen wachten:

![Bezetting-gegevensset](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Een logische app maken

In deze oplossing leest de logische app telemetrie van de Event Hub, parseert de gegevens en verzendt deze vervolgens naar de Power BI streaming-gegevens sets die u hebt gemaakt.

Voordat u de logische app maakt, moet u de apparaat-Id's van de twee RuuviTag-Sens oren die u hebt verbonden met uw IoT Central-toepassing hebben in de zelf studie [een in-Store Analytics-toepassing maken in Azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) :

1. Meld u aan bij uw **in-Store Analytics-kassa IOT Central-** toepassing.
1. Selecteer **apparaten** in het linkerdeel venster. Selecteer vervolgens **RuuviTag**.
1. Noteer de **apparaat-id's**. In de volgende scherm afbeelding zijn de Id's **f5dcf4ac32e8** en **e29ffc8d5326**:

    ![Apparaat-Id's](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

De volgende stappen laten zien hoe u de logische app maakt in de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **een resource maken** in de linkerbovenhoek van het scherm.
1. Voer in **de Marketplace zoeken de** _logische app_in en druk vervolgens op **Enter**.
1. Selecteer op de pagina **Logic app** **maken**.
1. Op de pagina **logische app** maken:
    * Voer een unieke naam in voor uw logische app, zoals naam _-Retail-Store-Analysis_.
    * Selecteer hetzelfde **abonnement** dat u hebt gebruikt om uw IOT Central-toepassing te maken.
    * Selecteer de resource groep **Retail-Store-Analysis** .
    * Selecteer de locatie die u hebt gebruikt voor uw IoT Central-toepassing.
    * Selecteer **Maken**. Mogelijk moet u enkele minuten wachten totdat het systeem de resources heeft ingericht.
1. Ga in het Azure Portal naar de nieuwe logische app.
1. Schuif op de pagina **Logic apps Designer** omlaag en selecteer **lege logische app**.
1. In **Zoek verbindingslijnen en triggers**voert u _Event hubs_in.
1. Selecteer in **Triggers** **wanneer gebeurtenissen beschikbaar zijn in Event hub**.
1. Voer een _Store-telemetrie_ in als de naam van de **verbinding**en selecteer uw **Event hubs naam ruimte**.
1. Selecteer het **RootManageSharedAccess** -beleid en selecteer **maken**.
1. In de actie **wanneer gebeurtenissen beschikbaar zijn in Event hub** :
    * Selecteer **Store-telemetrie**in de naam van de **Event hub**.
    * Selecteer in **inhouds type**de optie **Application/JSON**.
    * Stel het **interval** in op drie en de **frequentie** in seconden
1. Selecteer **Opslaan** om uw logische app op te slaan.

Selecteer de **code weergave**om de logica toe te voegen aan het ontwerp van de logische app:

1. Vervang `"actions": {},` door de volgende JSON. Vervang de twee tijdelijke aanduidingen `[YOUR RUUVITAG DEVICE ID 1]` en `[YOUR RUUVITAG DEVICE ID 2]` door de Id's die u hebt genoteerd op uw twee RuuviTag-apparaten:

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

1. Selecteer **Opslaan** en selecteer vervolgens **Designer** om de visuele versie van de logica te zien die u hebt toegevoegd:

    ![Ontwerp van logische app](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. Selecteer **Switch by DeviceID** om de actie uit te vouwen. Selecteer vervolgens **zone 1 omgeving**en selecteer **een actie toevoegen**.
1. Voer in **Zoek verbindingslijnen en**-acties **Power bi**in en druk vervolgens op **Enter**.
1. Selecteer de actie **rijen aan een gegevensset toevoegen (preview)** .
1. Selecteer **Aanmelden** en volg de aanwijzingen om u aan te melden bij uw Power bi-account.
1. Nadat het aanmeldings proces is voltooid, in de actie **rijen toevoegen aan een gegevensset** :
    * Selecteer **in-Store Analytics-Checkout** als de werk ruimte.
    * Selecteer **zone 1 sensor** als de gegevensset.
    * Selecteer **RTG** als de tabel.
    * Selecteer **nieuwe para meter toevoegen** en selecteer vervolgens de velden **tijds tempel**, **vochtigheids graad**en **temperatuur** .
    * Selecteer het **tijds tempel** veld en selecteer vervolgens **x-opt-enqueuedtime** in de lijst met **dynamische inhoud** .
    * Selecteer het veld **vochtigheid** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **vochtigheids graad**.
    * Selecteer het veld **Tempe ratuur** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **Tempe ratuur**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De actie voor de **zone 1 omgeving** ziet eruit als in de volgende scherm afbeelding: ![Zone 1 omgeving](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. Selecteer de actie voor de **zone 2 omgeving** en selecteer **een actie toevoegen**.
1. Voer in **Zoek verbindingslijnen en**-acties **Power bi**in en druk vervolgens op **Enter**.
1. Selecteer de actie **rijen aan een gegevensset toevoegen (preview)** .
1. In de actie **rijen toevoegen aan een gegevensset 2** :
    * Selecteer **in-Store Analytics-Checkout** als de werk ruimte.
    * Selecteer **zone 2 sensor** als de gegevensset.
    * Selecteer **RTG** als de tabel.
    * Selecteer **nieuwe para meter toevoegen** en selecteer vervolgens de velden **tijds tempel**, **vochtigheids graad**en **temperatuur** .
    * Selecteer het **tijds tempel** veld en selecteer vervolgens **x-opt-enqueuedtime** in de lijst met **dynamische inhoud** .
    * Selecteer het veld **vochtigheid** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **vochtigheids graad**.
    * Selecteer het veld **Tempe ratuur** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **Tempe ratuur**.
    Selecteer **Opslaan** om uw wijzigingen op te slaan.  De actie voor de **zone 2 omgeving** ziet eruit als in de volgende scherm afbeelding: ![Zone 2 omgeving](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. Selecteer de actie **bezetting** en selecteer vervolgens de actie **overschakelen op interface-id** .
1. Selecteer de actie voor de **woning tijd interface** en selecteer **een actie toevoegen**.
1. Voer in **Zoek verbindingslijnen en**-acties **Power bi**in en druk vervolgens op **Enter**.
1. Selecteer de actie **rijen aan een gegevensset toevoegen (preview)** .
1. In de actie **rijen toevoegen aan een gegevensset** :
    * Selecteer **in-Store Analytics-Checkout** als de werk ruimte.
    * Selecteer **bezettings sensor** als gegevensset.
    * Selecteer **RTG** als de tabel.
    * Selecteer **nieuwe para meter toevoegen** en selecteer vervolgens de velden **tijds tempel**, **woning tijd 1**en **woning 2** .
    * Selecteer het **tijds tempel** veld en selecteer vervolgens **x-opt-enqueuedtime** in de lijst met **dynamische inhoud** .
    * Selecteer het veld **woning tijd 1** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **DwellTime1**.
    * Selecteer het veld **woning tijd 2** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **DwellTime2**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De actie voor de **woning tijd interface** ziet eruit als in de volgende scherm afbeelding: ![bezetting actie](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. Selecteer de actie voor het **aantal personen** in de interface en selecteer **een actie toevoegen**.
1. Voer in **Zoek verbindingslijnen en**-acties **Power bi**in en druk vervolgens op **Enter**.
1. Selecteer de actie **rijen aan een gegevensset toevoegen (preview)** .
1. In de actie **rijen toevoegen aan een gegevensset** :
    * Selecteer **in-Store Analytics-Checkout** als de werk ruimte.
    * Selecteer **bezettings sensor** als gegevensset.
    * Selecteer **RTG** als de tabel.
    * Selecteer **nieuwe para meter toevoegen** en selecteer vervolgens de velden **tijds tempel**, **wachtrij lengte 1**en **wachtrij lengte 2** .
    * Selecteer het **tijds tempel** veld en selecteer vervolgens **x-opt-enqueuedtime** in de lijst met **dynamische inhoud** .
    * Selecteer het veld **wachtrij lengte 1** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **count1**.
    * Selecteer het veld **wachtrij lengte 2** en selecteer vervolgens **meer weer geven** naast **telemetrie parseren**. Selecteer vervolgens **count2**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De actie voor het **aantal personen** in de interface ziet eruit als in de volgende scherm afbeelding: ![bezetting actie](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

De logische app wordt automatisch uitgevoerd. Als u de status van elke uitvoering wilt zien, gaat u naar de **overzichts** pagina voor de logische app in de Azure portal:

## <a name="create-a-power-bi-dashboard"></a>Een Power BI-dash board maken

Nu hebt u telemetrie vanuit uw IoT Central-toepassing via uw Event Hub. Vervolgens parseert uw logische app de Event Hub berichten en voegt deze toe aan een Power BI streaming-gegevensset. U kunt nu een Power BI-dash board maken om de telemetrie te visualiseren:

1. Meld u aan bij uw **Power BI**-account.
1. Selecteer **werk ruimten > in-Store Analytics-Checker**.
1. Selecteer **> dash board maken**.
1. Voer **winkel analyse** in als de naam van het dash board en selecteer **maken**.

### <a name="add-line-charts"></a>Lijn diagrammen toevoegen

Voeg vier lijn diagram tegels toe om de Tempe ratuur en vochtigheid van de twee omgevings sensors weer te geven. Gebruik de informatie in de volgende tabel om de tegels te maken. Als u elke tegel wilt toevoegen, begint u met selecteren **... (Meer opties) > Tegel toevoegen**. Selecteer **aangepaste streaminggegevens**en selecteer vervolgens **volgende**:

| Instelling | Grafiek #1 | Grafiek #2 | Grafiek #3 | Grafiek #4 |
| ------- | -------- | -------- | -------- | -------- |
| Gegevensset | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| Visualisatie type | Lijndiagram | Lijndiagram | Lijndiagram | Lijndiagram |
| Axis | Tijdstempel | Tijdstempel | Tijdstempel | Tijdstempel |
| Waarden | Temperatuur | Vochtigheid | Temperatuur | Vochtigheid |
| Tijd venster | 60 minuten | 60 minuten | 60 minuten | 60 minuten |
| Titel | Tempe ratuur (1 uur) | Vochtigheid (1 uur) | Tempe ratuur (1 uur) | Vochtigheid (1 uur) |
| Ondertitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

Op de volgende scherm afbeelding ziet u de instellingen voor de eerste grafiek:

![Lijndiagraminstellingen](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Kaarten toevoegen om omgevings gegevens weer te geven

Voeg vier kaart tegels toe om de meest recente Tempe ratuur-en vochtigheids waarden van de twee omgevings sensors weer te geven. Gebruik de informatie in de volgende tabel om de tegels te maken. Als u elke tegel wilt toevoegen, begint u met selecteren **... (Meer opties) > Tegel toevoegen**. Selecteer **aangepaste streaminggegevens**en selecteer vervolgens **volgende**:

| Instelling | Kaart #1 | Kaart #2 | Kaart #3 | Kaart #4 |
| ------- | ------- | ------- | ------- | ------- |
| Gegevensset | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| Visualisatie type | Bedankt | Bedankt | Bedankt | Bedankt |
| Velden | Temperatuur | Vochtigheid | Temperatuur | Vochtigheid |
| Titel | Tempe ratuur (F) | Vochtigheid (%) | Tempe ratuur (F) | Vochtigheid (%) |
| Ondertitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

De volgende scherm afbeelding toont de instellingen voor de eerste kaart:

![Kaart instellingen](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Tegels toevoegen voor het weer geven van uitchecken van gegevens

Voeg vier kaart tegels toe om de lengte en de duur van de wachtrij voor de twee uitchecks in de Store weer te geven. Gebruik de informatie in de volgende tabel om de tegels te maken. Als u elke tegel wilt toevoegen, begint u met selecteren **... (Meer opties) > Tegel toevoegen**. Selecteer **aangepaste streaminggegevens**en selecteer vervolgens **volgende**:

| Instelling | Kaart #1 | Kaart #2 | Kaart #3 | Kaart #4 |
| ------- | ------- | ------- | ------- | ------- |
| Gegevensset | Bezetting sensor | Bezetting sensor | Bezetting sensor | Bezetting sensor |
| Visualisatie type | Gegroepeerd kolom diagram | Gegroepeerd kolom diagram | Meter | Meter |
| Axis    | Tijdstempel | Tijdstempel | N/A | N/A |
| Waarde | Woning tijd 1 | Woning tijd 2 | Wachtrij lengte 1 | Wachtrij lengte 2 |
| Tijd venster | 60 minuten | 60 minuten |  N/A | N/A |
| Titel | Woning tijd | Woning tijd | Lengte van wachtrij | Lengte van wachtrij |
| Ondertitel | Uitchecken 1 | Uitchecken 2 | Uitchecken 1 | Uitchecken 2 |

Wijzig de grootte van de tegels op het dash board en rang Schik deze opnieuw op de volgende scherm afbeelding:

![Power BI dash board](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

U kunt een aantal extra grafische resources toevoegen om het dash board verder aan te passen:

![Power BI dash board](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met uw IoT Central-toepassing, kunt u deze verwijderen door u aan te melden bij de toepassing en te navigeren naar de pagina **Toepassings instellingen** in de sectie **beheer** .

Als u de toepassing wilt blijven gebruiken, maar de bijbehorende kosten wilt verlagen, schakelt u de gegevens export uit die de telemetrie naar uw Event Hub verzendt.

U kunt de Event Hub en logische app verwijderen in de Azure Portal door de resource groep **Retail-Store-Analysis**te verwijderen.

U kunt de Power BI gegevens sets en het dash board verwijderen door de werk ruimte te verwijderen via de pagina Power BI-instellingen voor de werk ruimte.

## <a name="next-steps"></a>Volgende stappen

In deze drie zelf studies is een end-to-end oplossing weer gegeven die gebruikmaakt van de IoT Central toepassings sjabloon **in de Store Analytics-kassa** . U hebt apparaten verbonden met de toepassing, gebruikt IoT Central om de apparaten te bewaken en gebruikt Power BI om een dash board te maken om inzichten van de telemetrie van het apparaat weer te geven. Een aanbevolen volgende stap is om een van de andere IoT Central-toepassings sjablonen te verkennen:

> [!div class="nextstepaction"]
> * [Bouw energie oplossingen met IoT Central](../energy/overview-iot-central-energy.md)
> * [Overheids oplossingen bouwen met IoT Central](../government/overview-iot-central-government.md)
> * [Gezondheids zorg oplossingen bouwen met IoT Central](../healthcare/overview-iot-central-healthcare.md)
