---
title: Time Series Insights integreren met externe controle-Azure | Microsoft Docs
description: In deze procedure leert u hoe u Time Series Insights kunt configureren voor een bestaande oplossing voor controle op afstand die nog geen Time Series Insights bevat.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77564641"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integreer Azure Time Series Insights met Externe bewaking

Azure Time Series Insights is een volledig beheerde analyse-, opslag-en visualisatie service voor het beheren van gegevens in de Cloud voor de tijd reeks van IoT-schaal. U kunt Time Series Insights gebruiken om gegevens van een tijd reeks op te slaan en te beheren, gebeurtenissen tegelijkertijd te verkennen en te visualiseren, de analyse van de hoofd oorzaak uit te voeren en meerdere sites en assets te vergelijken.

De oplossings versneller voor externe controle biedt nu automatische implementatie en integratie met Time Series Insights. In deze procedure leert u hoe u Time Series Insights kunt configureren voor een bestaande oplossing voor controle op afstand die nog geen Time Series Insights bevat.

> [!NOTE]
> Time Series Insights is momenteel niet beschikbaar in de Azure China-Cloud. Nieuwe implementaties van oplossingen voor externe bewaking in de cloud van Azure China gebruik Cosmos DB voor alle opslag.

## <a name="prerequisites"></a>Vereisten

Als u deze procedure wilt volt ooien, moet u al een oplossing voor externe controle hebben geïmplementeerd:

* [De oplossings versneller voor externe controle implementeren](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Een Consumer groep maken

Maak een speciale Consumer groep in uw IoT Hub die moet worden gebruikt voor het streamen van gegevens naar Time Series Insights.

> [!NOTE]
> Consumenten groepen worden gebruikt door toepassingen voor het ophalen van gegevens uit Azure IoT Hub. Elke consumenten groep biedt Maxi maal vijf uitvoer verbruikers. U moet een nieuwe Consumer groep maken voor elke vijf uitvoer-sinks en u kunt Maxi maal 32 consumenten groepen maken.

1. Klik in het Azure Portal op de knop Cloud Shell.

1. Voer de volgende opdracht uit om een nieuwe consumenten groep te maken. Gebruik de naam van de IoT-hub in uw implementatie voor externe controle en de naam van uw implementatie voor externe controle als de naam van de resource groep:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Time Series Insights implementeren

Implementeer vervolgens Time Series Insights als een extra bron in uw oplossing voor externe controle en verbind deze met de IoT-hub.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **een resource maken**  >  **Internet of Things**  >  **Time Series Insights**.

    ![Nieuwe Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Als u uw Time Series Insights omgeving wilt maken, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van omgeving | De volgende scherm afbeelding maakt gebruik van de naam **contorosrmtsi**. Kies uw eigen unieke naam wanneer u deze stap uitvoert. |
    | Abonnement | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Resourcegroep | **Bestaande gebruiken**. Selecteer de naam van de bestaande resource groep voor externe bewaking. |
    | Locatie | We gebruiken **VS-Oost**. Maak indien mogelijk uw omgeving in dezelfde regio als uw oplossing voor externe controle. |
    | Sku |**S1** |
    | Capaciteit | **1** |

    ![Time Series Insights maken](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klik op **Create**. Het kan even duren voordat de omgeving is gemaakt.

## <a name="create-event-source"></a>Gebeurtenisbron maken

Maak een nieuwe gebeurtenis bron om verbinding te maken met uw IoT-hub. Zorg ervoor dat u de Consumer groep gebruikt die in de vorige stappen is gemaakt. Time Series Insights moet elke service een speciale Consumer-groep hebben die niet wordt gebruikt door een andere service.

1. Navigeer naar uw nieuwe Time Series Insights omgeving.

1. Klik aan de linkerkant op **gebeurtenis bronnen**.

    ![Gebeurtenis bronnen weer geven](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klik op **Add**.

    ![Gebeurtenis bron toevoegen](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Als u uw IoT-hub als een nieuwe gebeurtenis bron wilt configureren, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van gebeurtenisbron | De volgende scherm afbeelding maakt gebruik van de naam **contosorm-IOT-hub**. Gebruik uw eigen unieke naam wanneer u deze stap uitvoert. |
    | Bron | **IoT Hub** |
    | Importoptie | **IoT Hub van beschik bare abonnementen gebruiken** |
    | Abonnements-id | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | IOT-hub-naam | **contosorma57a6**. Gebruik de naam van uw IoT-hub vanuit uw oplossing voor externe controle. |
    | Naam van het IoT-hub-beleid | **iothubowner** Zorg ervoor dat het gebruikte beleid een eigenaars beleid is. |
    | Beleids sleutel voor IOT hub | Dit veld wordt automatisch ingevuld. |
    | IoT-hub-consumentengroep | **timeseriesinsights** |
    | Serialisatie-indeling voor gebeurtenissen | **JSON**     | 
    | Naam van de timestamp-eigenschap | Leeg laten |

    ![Gebeurtenis bron maken](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klik op **Create**.

## <a name="configure-the-data-access-policy"></a>Het beleid voor gegevens toegang configureren

Om ervoor te zorgen dat alle gebruikers die toegang hebben tot uw oplossing voor externe controle, gegevens kunnen verkennen in de Time Series Insights Explorer, voegt u uw toepassing en gebruikers toe onder beleid voor gegevens toegang in de Azure Portal. 

1. Kies **Resourcegroepen** in de navigatielijst.

1. Kies de resource groep **ContosoRM** .

1. Kies **contosormtsi** in de lijst met Azure-resources.

1. Kies **Data Access policies** om de huidige lijst met roltoewijzingen weer te geven.

1. Kies **toevoegen** om het deel venster **gebruikers regel selecteren** te openen.

   Als u geen machtigingen hebt om rollen toe te wijzen, ziet u de optie **toevoegen** niet.

1. Selecteer in de vervolg keuzelijst **rol** een rol zoals **lezer** en **Inzender**.

1. Selecteer in de lijst **Selecteren** een gebruiker, groep of toepassing. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.

1. Kies **Opslaan** om de roltoewijzing te maken. Na enkele ogen blikken wordt aan de beveiligingsprincipal de rol toegewezen in het beleid voor gegevens toegang.

> [!NOTE]
> Als u extra gebruikers toegang wilt verlenen tot de Time Series Insights Explorer, kunt u deze stappen gebruiken om [toegang tot gegevens te verlenen](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Azure Stream Analytics configureren 

De volgende stap is het configureren van de Azure Stream Analytics Manager-micro service om het verzenden van berichten naar Cosmos DB te stoppen en deze alleen op te slaan in Time Series Insights. Sla deze stap over als u uw berichten in Cosmos DB wilt dupliceren.

1. Kies **Resourcegroepen** in de navigatielijst.

1. Kies de resource groep **ContosoRM** .

1. Zoek de streaming-taak van Azure Stream Analytics (ASA) in de lijst met resources. De resource naam begint met **streamingjobs-**.

1. Klik bovenaan op de knop om de ASA streaming-taken te stoppen.

1. Bewerk de ASA-query en verwijder de componenten **Select**, **into**en **from** die verwijzen naar de berichten stroom in Cosmos db. Deze componenten moeten aan de onderkant van de query worden weer geven, zoals in het volgende voor beeld:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Start de Azure Stream Analytics streaming-taken opnieuw.

7. Haal de meest recente wijzigingen door aan de micro service Azure Stream Analytics Manager door de volgende opdracht te typen bij de opdracht prompt:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>De telemetrie-service configureren

Haal de meest recente telemetrie-service op door de volgende opdracht in te voeren in de opdracht prompt:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Optioneel]* De Web-UI configureren om een koppeling naar de Time Series Insights Explorer te maken

Als u uw gegevens eenvoudig in de Time Series Insights Explorer wilt weer geven, kunt u het beste de gebruikers interface aanpassen, zodat u gemakkelijk kunt koppelen aan de omgeving. Als u dit wilt doen, haalt u de meest recente wijzigingen door aan de Web-UI met de volgende opdracht:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>De omgevings variabelen configureren

Als u de integratie van Time Series Insights wilt volt ooien, moet u de omgeving van uw implementatie configureren voor de bijgewerkte micro Services.

### <a name="basic-deployments"></a>Basis implementaties

Configureer de implementatie omgeving `basic` voor de bijgewerkte micro Services.

1. Klik in het Azure Portal op het tabblad **Azure Active Directory** in het deel venster aan de linkerkant.

1. Klik op **app-registraties**.

1. Zoek en klik op uw **ContosoRM** -toepassing.

1. Navigeer naar **instellingen**  >  **sleutels** en maak een nieuwe sleutel voor uw toepassing. Zorg ervoor dat u de sleutel waarde naar een veilige locatie kopieert.

1. Haal het [meest recente docker yaml-bestand](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) op uit github opslag plaats met de nieuwste tag. 

1. SSH naar de virtuele machine door de stappen te volgen die worden beschreven in het [maken en gebruiken van SSH-sleutels](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Zodra u verbinding hebt gemaakt, typt u `cd /app` .

1. Voeg de volgende omgevings variabelen toe aan elke micro service in het docker yaml-bestand en het `env-setup` script in de virtuele machine:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Ga naar de **telemetrie-service** en bewerk het bestand docker opstellen door hierboven dezelfde omgevings variabelen toe te voegen.

1. Ga naar de **service ASA Manager** en bewerk het bestand docker opstellen door het toe te voegen `PCS_TELEMETRY_STORAGE_TYPE` .

1. Start de docker-containers opnieuw `sudo ./start.sh` op via de VM.

> [!NOTE]
> De bovenstaande configuratie van omgevings variabelen is geldig voor versies van externe controle voordat 1.0.2

### <a name="standard-deployments"></a>Standaard implementaties

Configureer de implementatie omgeving `standard` voor de bijgewerkte micro services hierboven

1. Voer op de opdracht regel uit `kubectl proxy` . Zie [toegang tot de Kubernetes-API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server)voor meer informatie.

1. Open de Kubernetes-beheer console.

1. Zoek de configuratie toewijzing om de volgende nieuwe omgevings variabelen voor de TSI toe te voegen:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Bewerk het sjabloon yaml-bestand voor de telemetrie-service Pod:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Bewerk het sjabloon yaml-bestand voor de ASA Manager-service Pod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Volgende stappen

* Zie onze zelf studie over het [uitvoeren van een analyse van hoofd oorzaken](iot-accelerators-remote-monitoring-root-cause-analysis.md)voor meer informatie over het verkennen van uw gegevens en het vaststellen van een waarschuwing in de time series Insights Explorer.

* Zie de documentatie op de [Azure time series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)voor informatie over het verkennen en opvragen van gegevens in de time series Insights Explorer.
