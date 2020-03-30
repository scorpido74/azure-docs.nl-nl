---
title: Azure IoT Central uitbreiden met aangepaste analyses | Microsoft Documenten
description: Configureer als ontwikkelaar van oplossingen een IoT Central-toepassing om aangepaste analyses en visualisaties uit te kunnen doen. Deze oplossing maakt gebruik van Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158194"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure IoT Central uitbreiden met aangepaste analyses met Azure Databricks

Deze handleiding laat u als oplossingsontwikkelaar zien hoe u uw IoT Central-toepassing uitbreiden met aangepaste analyses en visualisaties. Het voorbeeld maakt gebruik van een [Azure Databricks-werkruimte](https://docs.microsoft.com/azure/azure-databricks/) om de IoT Central-telemetriestream te analyseren en visualisaties zoals [boxplots](https://wikipedia.org/wiki/Box_plot)te genereren.

Deze handleiding laat u zien hoe u IoT Central verder uitbreiden dan wat het al kan doen met de [ingebouwde analysetools.](./howto-create-custom-analytics.md)

In deze handleiding leer je hoe je:

* Telemetrie streamen vanuit een IoT Central-toepassing met *continue gegevensexport*.
* Maak een Azure Databricks-omgeving om telemetrie van apparaten te analyseren en uit te plannen.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

### <a name="iot-central-application"></a>IoT Central-toepassing

Maak een IoT Central-toepassing op de azure [IoT Central-applicatiebeheerwebsite](https://aka.ms/iotcentral) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Prijsplan | Standard |
| Toepassingsjabloon | In-store analytics – conditiebewaking |
| De naam van de toepassing | De standaardinstelling accepteren of uw eigen naam kiezen |
| URL | Accepteer de standaardinstelling of kies uw eigen unieke URL-voorvoegsel |
| Directory | Uw Azure Active Directory-tenant |
| Azure-abonnement | Uw Azure-abonnement |
| Regio | Uw dichtstbijzijnde regio |

De voorbeelden en screenshots in dit artikel maken gebruik van de **regio Verenigde Staten.** Kies een locatie dicht bij u en zorg ervoor dat u al uw bronnen in dezelfde regio maakt.

Deze toepassingssjabloon bevat twee gesimuleerde thermostaatapparaten die telemetrie verzenden.

### <a name="resource-group"></a>Resourcegroep

Gebruik de [Azure-portal om een brongroep](https://portal.azure.com/#create/Microsoft.ResourceGroup) met de naam **IoTCentralAnalysis** te maken om de andere resources die u maakt te bevatten. Maak uw Azure-resources op dezelfde locatie als uw IoT Central-toepassing.

### <a name="event-hubs-namespace"></a>Event Hubs-naamruimte

Gebruik de [Azure-portal om een naamruimte voor gebeurtenishubs te maken](https://portal.azure.com/#create/Microsoft.EventHub) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Name    | Kies uw naamruimtenaam |
| Prijscategorie | Basic |
| Abonnement | Uw abonnement |
| Resourcegroep | IoTCentralAnalysis |
| Locatie | VS - oost |
| Doorvoereenheden | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks-werkruimte

Gebruik de [Azure-portal om een Azure Databricks-service te maken](https://portal.azure.com/#create/Microsoft.Databricks) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam van de werkruimte    | De naam van uw werkruimte kiezen |
| Abonnement | Uw abonnement |
| Resourcegroep | IoTCentralAnalysis |
| Locatie | VS - oost |
| Prijscategorie | Standard |

Wanneer u de vereiste resources hebt gemaakt, ziet uw **IoTCentralAnalysis-brongroep** eruit als de volgende schermafbeelding:

![IoT Central analyse resource groep](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Een Event Hub maken

U een IoT Central-toepassing configureren om continu telemetrie naar een gebeurtenishub te exporteren. In deze sectie maakt u een gebeurtenishub om telemetrie van uw IoT Central-toepassing te ontvangen. De gebeurtenishub levert de telemetrie aan uw Stream Analytics-taak voor verwerking.

1. Navigeer in de Azure-portal naar de naamruimte van uw gebeurtenishubs en selecteer **+ Gebeurtenishub**.
1. Geef uw gebeurtenishub **een centrale naam**en selecteer **Maken**.
1. Selecteer **centralexport**in de lijst met gebeurtenishubs in uw naamruimte . Kies vervolgens **Beleid voor gedeelde toegang**.
1. Selecteer **+ Toevoegen**. Maak een beleid met de naam **Luisteren** met de claim **Luisteren.**
1. Wanneer het beleid klaar is, selecteert u het in de lijst en kopieert u de primaire sleutelwaarde van de **verbindingstekenreeks.**
1. Noteer deze verbindingstekenreeks, u gebruikt deze later wanneer u uw Databricks-notitieblok configureert om te lezen vanuit de gebeurtenishub.

De naamruimte van je gebeurtenishubs ziet eruit als de volgende schermafbeelding:

![Event Hubs-naamruimte](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Export configureren in IoT Central

Navigeer op de website [azure IoT Central-toepassingsbeheer](https://aka.ms/iotcentral) naar de IoT Central-toepassing die u hebt gemaakt op basis van de Contoso-sjabloon. In deze sectie configureert u de toepassing om de telemetrie van de gesimuleerde apparaten naar uw gebeurtenishub te streamen. Ga als u de export wilt configureren:

1. Navigeer naar de pagina **Gegevensexporteren,** selecteer **+ Nieuw**en vervolgens Azure **Event Hubs**.
1. Gebruik de volgende instellingen om de export te configureren en selecteer **Opslaan:**

    | Instelling | Waarde |
    | ------- | ----- |
    | Weergavenaam | Exporteren naar gebeurtenishubs |
    | Ingeschakeld | Aan |
    | Event Hubs-naamruimte | Naamruimte van uw gebeurtenishubs |
    | Event Hub | centrale uitvoer |
    | Metingen | Aan |
    | Apparaten | Uit |
    | Apparaatsjablonen | Uit |

![Configuratie van gegevensexport](media/howto-create-custom-analytics/cde-configuration.png)

Wacht tot de exportstatus **wordt uitgevoerd** voordat u verdergaat.

## <a name="configure-databricks-workspace"></a>Werkruimte Gegevensstenen configureren

Navigeer in de Azure-portal naar uw Azure Databricks-service en selecteer **Werkruimte starten.** Er wordt een nieuw tabblad geopend in uw browser en meldt u aan bij uw werkruimte.

### <a name="create-a-cluster"></a>Een cluster maken

Selecteer op de pagina **Azure Databricks** onder de lijst met veelvoorkomende taken de optie **Nieuw cluster**.

Gebruik de informatie in de volgende tabel om uw cluster te maken:

| Instelling | Waarde |
| ------- | ----- |
| Clusternaam | centrale analyse |
| Clustermodus | Standard |
| Databricks Runtime-versie | 5.5 LTS (Scala 2.11, Spark 2.4.3) |
| Python-versie | 3 |
| Automatisch schalen inschakelen | Nee |
| Eindigen na minuten van inactiviteit | 30 |
| Type werknemer | Standard_DS3_v2 |
| Werknemers | 1 |
| Type stuurprogramma | Hetzelfde als werknemer |

Het maken van een cluster kan enkele minuten duren, wachten tot de clustercreatie is voltooid voordat u verdergaat.

### <a name="install-libraries"></a>Bibliotheken installeren

Wacht **op** de pagina Clusters totdat de clusterstatus **wordt uitgevoerd**.

In de volgende stappen ziet u hoe u de bibliotheek importeert die uw voorbeeldbehoeften nodig heeft in het cluster:

1. Wacht **op** de pagina Clusters totdat de status van het interactieve cluster **centralanalysis** **actief**is.

1. Selecteer het cluster en kies het tabblad **Bibliotheken.**

1. Kies op het tabblad **Bibliotheken** de optie **Nieuw installeren**.

1. Kies **Maven** op de pagina **Bibliotheek installeren** als bron van de bibliotheek.

1. Voer in het tekstvak **Coördinaten** de volgende waarde in:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Kies **Installeren** om de bibliotheek op het cluster te installeren.

1. De bibliotheekstatus is nu **geïnstalleerd:**

    ![Bibliotheek geïnstalleerd](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Een Databricks-notitieblok importeren

Gebruik de volgende stappen om een Databricks-notitieblok te importeren dat de Python-code bevat om uw IoT Central-telemetrie te analyseren en te visualiseren:

1. Navigeer naar de **pagina Werkruimte** in uw Databricks-omgeving. Selecteer de vervolgkeuzelijst naast uw accountnaam en kies **Importeren**.

1. Kies om te importeren uit een URL en voer het volgende adres in:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Als u het notitieblok wilt importeren, kiest **u Importeren**.

1. Selecteer de **werkruimte** om het geïmporteerde notitieblok weer te geven:

    ![Geïmporteerd notitieblok](media/howto-create-custom-analytics/import-notebook.png)

1. Bewerk de code in de eerste Python-cel om de verbindingstekenreeks Van Gebeurtenishubs toe te voegen die u eerder hebt opgeslagen:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Analyse uitvoeren

Als u de analyse wilt uitvoeren, moet u het notitieblok aan het cluster koppelen:

1. Selecteer **Vrijstaand** en selecteer vervolgens het **centrale analysecluster.**
1. Als het cluster niet wordt uitgevoerd, start u het.
1. Als u het notitieblok wilt starten, selecteert u de knop Uitvoeren.

Mogelijk ziet u een fout in de laatste cel. Als dat het zo is, controleert u of de vorige cellen worden uitgevoerd, wacht u een minuut tot bepaalde gegevens naar de opslag zijn geschreven en voert u de laatste cel opnieuw uit.

### <a name="view-smoothed-data"></a>Vloeiende gegevens weergeven

Schuif in het notitieblok naar cel 14 om een plot van de voortschrijdende gemiddelde luchtvochtigheid per apparaattype te zien. Dit plot wordt voortdurend bijgewerkt als streaming telemetrie aankomt:

![Vloeiend telemetrieplot](media/howto-create-custom-analytics/telemetry-plot.png)

U het formaat van de grafiek in het notitieblok wijzigen.

### <a name="view-box-plots"></a>Vakpercelen weergeven

Schuif in het notitieblok omlaag naar cel 20 om de [vakplots](https://en.wikipedia.org/wiki/Box_plot)te zien. De box plots zijn gebaseerd op statische gegevens dus om ze bij te werken moet u de cel opnieuw uitvoeren:

![Box percelen](media/howto-create-custom-analytics/box-plots.png)

U het formaat van de percelen in het notitieblok wijzigen.

## <a name="tidy-up"></a>Opruimen

Als u na deze how-to wilt opruimen en onnodige kosten wilt voorkomen, verwijdert u de **iotCentralAnalysis-brongroep** in de Azure-portal.

U de IoT Central-toepassing verwijderen van de pagina **Beheer** in de toepassing.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding leerde je hoe je:

* Telemetrie streamen vanuit een IoT Central-toepassing met *continue gegevensexport*.
* Maak een Azure Databricks-omgeving om telemetriegegevens te analyseren en uit te stellen.

Nu u weet hoe u aangepaste analyses maken, is de voorgestelde volgende stap om te leren hoe [u uw toepassing beheren.](howto-administer.md)
